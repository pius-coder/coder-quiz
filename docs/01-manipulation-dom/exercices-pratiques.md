# Exercices Pratiques - Manipulation DOM

## 🎯 Instructions
Suivez ces exercices dans l'ordre. Chaque exercice s'appuie sur le précédent. Consultez le `guide-concepts.md` si vous avez besoin d'aide sur les concepts.

## 📁 Structure des fichiers
Créez cette structure dans votre dossier de travail :
```
exercice-dom/
├── index.html
├── style.css
└── script.js
```

## 🏋️ Exercice 1 : Sélection sécurisée d'éléments

### Étape 1.1 : Créer la structure HTML

Créez `index.html` :
```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Exercice DOM</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div id="app">
        <header>
            <h1 id="main-title">Mon Application Quiz</h1>
            <div class="score">Score: <span id="score-value">0</span></div>
        </header>
        
        <main>
            <section id="welcome">
                <h2>Bienvenue !</h2>
                <button id="start-btn" class="btn-primary">Commencer</button>
            </section>
            
            <section id="quiz-area" class="hidden">
                <div class="question-container">
                    <h3 id="question-text"></h3>
                    <div id="answers-list"></div>
                    <button id="submit-btn" class="btn-primary">Valider</button>
                </div>
            </section>
        </main>
    </div>
    
    <script src="script.js"></script>
</body>
</html>
```

### Étape 1.2 : CSS de base

Créez `style.css` :
```css
.hidden { display: none; }
.btn-primary {
    background: #007bff;
    color: white;
    border: none;
    padding: 10px 20px;
    border-radius: 5px;
    cursor: pointer;
}
.error { color: red; }
.success { color: green; }
```

### Étape 1.3 : Sélection sécurisée

Dans `script.js`, implémentez cette fonction :

```javascript
/**
 * Sélectionne un élément de manière sécurisée
 * @param {string} selector - Sélecteur CSS
 * @param {Element} parent - Élément parent (optionnel)
 * @returns {Element|null} - Élément trouvé ou null
 */
function safeQuerySelector(selector, parent = document) {
    // TODO: Implémenter la sélection sécurisée
    // - Utiliser try/catch
    // - Vérifier que l'élément existe
    // - Logger un warning si non trouvé
    // - Retourner l'élément ou null
}

/**
 * Récupère tous les éléments nécessaires pour l'application
 * @returns {Object} - Objet contenant tous les éléments
 */
function getRequiredElements() {
    const elements = {
        app: safeQuerySelector('#app'),
        title: safeQuerySelector('#main-title'),
        scoreValue: safeQuerySelector('#score-value'),
        startBtn: safeQuerySelector('#start-btn'),
        welcomeSection: safeQuerySelector('#welcome'),
        quizSection: safeQuerySelector('#quiz-area'),
        questionText: safeQuerySelector('#question-text'),
        answersList: safeQuerySelector('#answers-list'),
        submitBtn: safeQuerySelector('#submit-btn')
    };
    
    // TODO: Vérifier que tous les éléments critiques existent
    // - Créer un array des éléments manquants
    // - Afficher une erreur si des éléments manquent
    // - Retourner les éléments
    
    return elements;
}

// Test de la fonction
const elements = getRequiredElements();
console.log('Éléments récupérés:', elements);
```

**🎯 Objectif :** Votre fonction doit détecter les éléments manquants et afficher des messages d'erreur appropriés.

**🧪 Test :** Supprimez un élément du HTML et vérifiez que votre code détecte le problème.

## 🏋️ Exercice 2 : Création dynamique d'éléments

### Étape 2.1 : Fonction utilitaire de création

Ajoutez cette fonction à `script.js` :

```javascript
/**
 * Crée un élément avec des options
 * @param {string} tagName - Nom de la balise
 * @param {Object} options - Options de configuration
 * @returns {Element} - Élément créé
 */
function createElement(tagName, options = {}) {
    // TODO: Implémenter la création d'élément
    // - Créer l'élément
    // - Ajouter le textContent si fourni
    // - Ajouter les classes si fournies (array ou string)
    // - Ajouter les attributs si fournis (objet key-value)
    // - Ajouter les événements si fournis (objet event-handler)
    // - Retourner l'élément
}

// Exemple d'usage attendu :
const button = createElement('button', {
    textContent: 'Mon bouton',
    classes: ['btn', 'btn-primary'],
    attributes: {
        id: 'my-btn',
        type: 'button'
    },
    events: {
        click: () => console.log('Cliqué!')
    }
});
```

### Étape 2.2 : Création d'une question

Implémentez cette fonction :

```javascript
/**
 * Crée une carte de question complète
 * @param {Object} questionData - Données de la question
 * @returns {Element} - Élément de la question
 */
function createQuestionCard(questionData) {
    // TODO: Créer la structure suivante :
    // <div class="question-card">
    //   <h3>Question text</h3>
    //   <div class="answers">
    //     <div class="answer-option">
    //       <input type="radio" id="answer-0" name="current-question" value="0">
    //       <label for="answer-0">Réponse 1</label>
    //     </div>
    //     <!-- ... autres réponses -->
    //   </div>
    // </div>
    
    // Utilisez createElement() pour tout créer
    // Attention aux attributs id, name, for qui doivent correspondre
}

// Données de test
const testQuestion = {
    id: 'q1',
    question: "Quelle méthode permet de sélectionner un élément par son ID ?",
    answers: [
        "document.getElementById()",
        "document.querySelector()",
        "document.getElement()",
        "document.findById()"
    ],
    correctAnswer: 0
};

// Test de la fonction
const questionCard = createQuestionCard(testQuestion);
console.log('Carte créée:', questionCard);
```

## 🏋️ Exercice 3 : Gestion des inputs radio

### Étape 3.1 : Configuration correcte des radios

Implémentez cette fonction spécialisée :

```javascript
/**
 * Crée un input radio avec son label correctement configuré
 * @param {string} questionId - ID unique de la question
 * @param {number} answerIndex - Index de la réponse
 * @param {string} answerText - Texte de la réponse
 * @returns {Element} - Container avec input et label
 */
function createRadioOption(questionId, answerIndex, answerText) {
    // TODO: Créer la structure :
    // <div class="answer-option">
    //   <input type="radio" id="q1-answer-0" name="question-q1" value="0">
    //   <label for="q1-answer-0">Texte de la réponse</label>
    // </div>
    
    // Points critiques :
    // - ID unique : questionId + '-answer-' + answerIndex
    // - Name unique par question : 'question-' + questionId
    // - Value = answerIndex
    // - htmlFor du label = ID de l'input
}

/**
 * Récupère la réponse sélectionnée pour une question
 * @param {string} questionId - ID de la question
 * @returns {number|null} - Index de la réponse sélectionnée ou null
 */
function getSelectedAnswer(questionId) {
    // TODO: Implémenter la récupération de la réponse
    // - Utiliser querySelector avec le bon sélecteur
    // - Vérifier qu'une réponse est sélectionnée
    // - Retourner l'index (parseInt de la value) ou null
}
```

### Étape 3.2 : Test des groupes indépendants

Créez ce test :

```javascript
/**
 * Teste que les groupes de radio buttons sont indépendants
 */
function testRadioGroups() {
    const container = safeQuerySelector('#answers-list');
    if (!container) return;
    
    // Créer deux questions
    const question1 = {
        id: 'q1',
        question: 'Question 1 ?',
        answers: ['Réponse 1A', 'Réponse 1B', 'Réponse 1C']
    };
    
    const question2 = {
        id: 'q2',
        question: 'Question 2 ?',
        answers: ['Réponse 2A', 'Réponse 2B', 'Réponse 2C']
    };
    
    // TODO: Créer les deux groupes de radios
    // TODO: Vérifier qu'on peut sélectionner une réponse dans chaque groupe
    // TODO: Vérifier que sélectionner dans un groupe n'affecte pas l'autre
}

// Lancer le test
testRadioGroups();
```

## 🏋️ Exercice 4 : Intégration complète

### Étape 4.1 : Fonction principale

Créez une fonction qui utilise tout ce que vous avez appris :

```javascript
/**
 * Affiche une question dans l'interface
 * @param {Object} questionData - Données de la question
 */
function displayQuestion(questionData) {
    const elements = getRequiredElements();
    
    // TODO: Implémenter l'affichage complet
    // 1. Vérifier que les éléments nécessaires existent
    // 2. Mettre à jour le texte de la question
    // 3. Vider la liste des réponses précédentes
    // 4. Créer et ajouter les nouvelles options de réponse
    // 5. Afficher la section quiz et masquer l'accueil
    // 6. Gérer les erreurs potentielles
}

/**
 * Valide et récupère la réponse de l'utilisateur
 * @param {string} questionId - ID de la question
 * @returns {Object} - Résultat de la validation
 */
function validateUserAnswer(questionId) {
    // TODO: Retourner un objet avec :
    // {
    //   isValid: boolean,
    //   answerIndex: number|null,
    //   error: string|null
    // }
}
```

### Étape 4.2 : Test complet

```javascript
// Données de test complètes
const sampleQuestions = [
    {
        id: 'q1',
        question: "Quelle méthode permet de sélectionner un élément par son ID ?",
        answers: [
            "document.getElementById()",
            "document.querySelector()",
            "document.getElement()",
            "document.findById()"
        ],
        correctAnswer: 0
    },
    {
        id: 'q2',
        question: "Comment créer un nouvel élément HTML ?",
        answers: [
            "document.newElement()",
            "document.createElement()",
            "document.addElement()",
            "document.makeElement()"
        ],
        correctAnswer: 1
    }
];

// TODO: Créer une fonction de test qui :
// 1. Affiche la première question
// 2. Simule une sélection de réponse
// 3. Valide la réponse
// 4. Affiche le résultat
// 5. Passe à la question suivante
```

## 🧪 Tests de validation

### Test 1 : Robustesse
```javascript
// Supprimez des éléments du HTML et vérifiez que votre code :
// - Détecte les éléments manquants
// - Affiche des erreurs appropriées
// - Ne plante pas l'application
```

### Test 2 : Fonctionnalité
```javascript
// Vérifiez que :
// - Les radios buttons fonctionnent correctement
// - Chaque groupe est indépendant
// - La validation fonctionne
// - Les IDs et attributs sont corrects
```

### Test 3 : Accessibilité
```javascript
// Testez avec le clavier :
// - Tab pour naviguer entre les éléments
// - Flèches pour sélectionner les radios
// - Espace/Entrée pour activer les boutons
```

## 🎯 Critères de réussite

- [ ] Toutes les fonctions sont implémentées
- [ ] La sélection d'éléments est sécurisée
- [ ] Les éléments sont créés correctement
- [ ] Les radios buttons fonctionnent indépendamment
- [ ] La validation des réponses fonctionne
- [ ] Le code gère les erreurs gracieusement
- [ ] L'interface est accessible au clavier

## 💡 Conseils

1. **Testez chaque fonction** individuellement avant de passer à la suivante
2. **Utilisez la console** pour déboguer vos sélecteurs
3. **Vérifiez les attributs** avec l'inspecteur du navigateur
4. **Testez les cas d'erreur** en modifiant le HTML

---

**Prochaine étape :** Une fois tous les exercices terminés, consultez les solutions dans `solutions.md` !
