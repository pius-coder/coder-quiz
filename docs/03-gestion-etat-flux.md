# Exercice 3 : Gestion d'État et Flux de Données

## 🎯 Objectif
Apprendre à gérer l'état d'une application de manière cohérente et prévisible, éviter les bugs liés aux données incohérentes et créer un flux de données unidirectionnel.

## 📚 Notions à maîtriser
- Gestion centralisée de l'état
- Flux de données unidirectionnel
- Synchronisation entre état et interface
- Validation et cohérence des données
- Patterns de mise à jour d'état

## 🏋️ Exercices pratiques

### Exercice 3.1 : État centralisé vs variables globales

**Problème courant :** Variables globales dispersées et incohérentes

```javascript
// ❌ MAUVAISE PRATIQUE - Variables globales dispersées
let currentQuestion = 0;
let score = 0;
let isQuizActive = false;
let questions = [];
let userAnswers = [];

// Problèmes :
// - État dispersé
// - Pas de validation
// - Modifications non contrôlées
// - Difficile à déboguer
```

**Mission :** Créez un gestionnaire d'état centralisé :

```javascript
// ✅ BONNE PRATIQUE - À implémenter
class QuizState {
    constructor(questions) {
        // Initialiser l'état privé
        // Votre code ici...
    }
    
    // Getters pour accéder à l'état (lecture seule)
    get currentQuestionIndex() {
        // Votre code ici...
    }
    
    get score() {
        // Votre code ici...
    }
    
    get isActive() {
        // Votre code ici...
    }
    
    get currentQuestion() {
        // Votre code ici...
    }
    
    // Méthodes pour modifier l'état de manière contrôlée
    startQuiz() {
        // Votre code ici...
    }
    
    submitAnswer(answerIndex) {
        // Valider et enregistrer la réponse
        // Mettre à jour le score si correct
        // Passer à la question suivante
        // Votre code ici...
    }
    
    endQuiz() {
        // Votre code ici...
    }
    
    reset() {
        // Votre code ici...
    }
}
```

**Solution attendue :**
```javascript
class QuizState {
    constructor(questions) {
        this._questions = [...questions]; // Copie pour éviter mutations
        this._currentQuestionIndex = 0;
        this._score = 0;
        this._isActive = false;
        this._userAnswers = [];
        this._listeners = []; // Pour notifier les changements
    }
    
    // Getters (lecture seule)
    get currentQuestionIndex() { return this._currentQuestionIndex; }
    get score() { return this._score; }
    get isActive() { return this._isActive; }
    get totalQuestions() { return this._questions.length; }
    get currentQuestion() { 
        return this._questions[this._currentQuestionIndex] || null; 
    }
    get isComplete() { 
        return this._currentQuestionIndex >= this._questions.length; 
    }
    
    // Méthodes de modification contrôlée
    startQuiz() {
        this._isActive = true;
        this._currentQuestionIndex = 0;
        this._score = 0;
        this._userAnswers = [];
        this._notifyChange('quiz_started');
    }
    
    submitAnswer(answerIndex) {
        if (!this._isActive || this.isComplete) {
            throw new Error('Quiz non actif ou terminé');
        }
        
        const question = this.currentQuestion;
        const isCorrect = answerIndex === question.correctAnswer.id;
        
        this._userAnswers.push({
            questionIndex: this._currentQuestionIndex,
            answerIndex,
            isCorrect,
            timestamp: Date.now()
        });
        
        if (isCorrect) {
            this._score++;
        }
        
        this._currentQuestionIndex++;
        
        if (this.isComplete) {
            this._isActive = false;
            this._notifyChange('quiz_completed');
        } else {
            this._notifyChange('answer_submitted');
        }
        
        return { isCorrect, score: this._score };
    }
    
    // Système d'observation pour synchroniser l'UI
    addListener(callback) {
        this._listeners.push(callback);
    }
    
    _notifyChange(event) {
        this._listeners.forEach(callback => callback(event, this));
    }
}
```

### Exercice 3.2 : Synchronisation État-Interface

**Mission :** Créez un système qui synchronise automatiquement l'interface avec l'état :

```javascript
class QuizUI {
    constructor(quizState) {
        this.state = quizState;
        this.elements = this._getElements();
        this._setupStateListener();
    }
    
    _getElements() {
        // Récupérer tous les éléments DOM nécessaires
        // Votre code ici...
    }
    
    _setupStateListener() {
        // S'abonner aux changements d'état
        this.state.addListener((event, state) => {
            this._handleStateChange(event, state);
        });
    }
    
    _handleStateChange(event, state) {
        // Mettre à jour l'interface selon l'événement
        switch(event) {
            case 'quiz_started':
                // Votre code ici...
                break;
            case 'answer_submitted':
                // Votre code ici...
                break;
            case 'quiz_completed':
                // Votre code ici...
                break;
        }
    }
    
    // Méthodes de rendu
    _renderCurrentQuestion() {
        // Votre code ici...
    }
    
    _renderScore() {
        // Votre code ici...
    }
    
    _renderResults() {
        // Votre code ici...
    }
}
```

### Exercice 3.3 : Validation et cohérence des données

**Mission :** Implémentez un système de validation robuste :

```javascript
class QuizValidator {
    static validateQuestions(questions) {
        // Vérifier la structure des questions
        // Votre code ici...
    }
    
    static validateAnswer(answerIndex, question) {
        // Vérifier que la réponse est valide
        // Votre code ici...
    }
    
    static validateState(state) {
        // Vérifier la cohérence de l'état
        // Votre code ici...
    }
}

// Usage dans QuizState
submitAnswer(answerIndex) {
    // Validation avant traitement
    const validation = QuizValidator.validateAnswer(answerIndex, this.currentQuestion);
    if (!validation.isValid) {
        throw new Error(validation.error);
    }
    
    // Traitement...
}
```

**Solution de validation :**
```javascript
class QuizValidator {
    static validateQuestions(questions) {
        if (!Array.isArray(questions) || questions.length === 0) {
            return { isValid: false, error: 'Questions invalides ou vides' };
        }
        
        for (let i = 0; i < questions.length; i++) {
            const question = questions[i];
            
            if (!question.question || typeof question.question !== 'string') {
                return { isValid: false, error: `Question ${i}: texte manquant` };
            }
            
            if (!Array.isArray(question.answers) || question.answers.length < 2) {
                return { isValid: false, error: `Question ${i}: réponses insuffisantes` };
            }
            
            const correctAnswer = question.correctAnswer;
            if (!correctAnswer || 
                typeof correctAnswer.id !== 'number' ||
                correctAnswer.id < 0 || 
                correctAnswer.id >= question.answers.length) {
                return { isValid: false, error: `Question ${i}: réponse correcte invalide` };
            }
        }
        
        return { isValid: true };
    }
    
    static validateAnswer(answerIndex, question) {
        if (typeof answerIndex !== 'number') {
            return { isValid: false, error: 'Index de réponse doit être un nombre' };
        }
        
        if (!question) {
            return { isValid: false, error: 'Question manquante' };
        }
        
        if (answerIndex < 0 || answerIndex >= question.answers.length) {
            return { isValid: false, error: 'Index de réponse hors limites' };
        }
        
        return { isValid: true };
    }
}
```

### Exercice 3.4 : Flux de données unidirectionnel

**Mission :** Implémentez un flux de données prévisible :

```
Action → État → Interface → Action
```

```javascript
// Actions possibles
const ACTIONS = {
    START_QUIZ: 'START_QUIZ',
    SUBMIT_ANSWER: 'SUBMIT_ANSWER',
    END_QUIZ: 'END_QUIZ',
    RESET_QUIZ: 'RESET_QUIZ'
};

class QuizController {
    constructor(questions) {
        this.state = new QuizState(questions);
        this.ui = new QuizUI(this.state);
        this._setupEventHandlers();
    }
    
    // Point d'entrée unique pour toutes les actions
    dispatch(action, payload) {
        console.log('Action:', action, payload);
        
        switch(action) {
            case ACTIONS.START_QUIZ:
                this._handleStartQuiz();
                break;
            case ACTIONS.SUBMIT_ANSWER:
                this._handleSubmitAnswer(payload);
                break;
            // Autres actions...
        }
    }
    
    _handleStartQuiz() {
        try {
            this.state.startQuiz();
        } catch (error) {
            this._handleError(error);
        }
    }
    
    _handleSubmitAnswer(answerIndex) {
        try {
            const result = this.state.submitAnswer(answerIndex);
            // L'UI se met à jour automatiquement via les listeners
        } catch (error) {
            this._handleError(error);
        }
    }
    
    _setupEventHandlers() {
        // Connecter les événements DOM aux actions
        // Votre code ici...
    }
}
```

## 🧪 Tests à effectuer

### Test 1 : Cohérence d'état
```javascript
// Vérifiez que l'état reste cohérent dans tous les scénarios
const state = new QuizState(questions);
state.startQuiz();
// Tentez des modifications invalides
// L'état doit rester cohérent
```

### Test 2 : Synchronisation UI
```javascript
// Vérifiez que l'interface se met à jour automatiquement
// quand l'état change
```

### Test 3 : Validation
```javascript
// Testez tous les cas de validation
// Questions malformées, réponses invalides, etc.
```

## 💡 Conseils pratiques

1. **État immutable** : Ne modifiez jamais l'état directement
2. **Validation systématique** : Validez toutes les entrées
3. **Flux unidirectionnel** : Action → État → Interface
4. **Séparation des responsabilités** : État, UI, et Contrôleur séparés
5. **Observateurs** : Utilisez le pattern Observer pour la synchronisation

## 🎓 Points clés à retenir

- L'état doit être la source unique de vérité
- Toutes les modifications passent par des méthodes contrôlées
- L'interface se synchronise automatiquement avec l'état
- La validation empêche les états incohérents
- Le flux unidirectionnel rend l'application prévisible

## 🚀 Défi bonus

Implémentez un système d'historique (undo/redo) :

```javascript
class QuizStateWithHistory extends QuizState {
    constructor(questions) {
        super(questions);
        this._history = [];
        this._historyIndex = -1;
    }
    
    _saveState() {
        // Sauvegarder l'état actuel dans l'historique
    }
    
    undo() {
        // Revenir à l'état précédent
    }
    
    redo() {
        // Avancer dans l'historique
    }
}
```

---

**Prochaine étape :** Passez à l'exercice 4 sur la gestion d'erreurs et le feedback utilisateur !
