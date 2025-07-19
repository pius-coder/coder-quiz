# Exercices Pratiques - Gestion d'État et Flux de Données

## 🎯 Instructions
Ces exercices vous apprendront à gérer l'état de manière centralisée et prévisible avec une approche modulaire. Consultez le `guide-concepts.md` pour les concepts.

## 📁 Structure des fichiers
```
exercice-state/
├── index.html
├── style.css
├── js/
│   ├── state/
│   │   ├── quiz-state.js
│   │   └── actions.js
│   ├── ui/
│   │   └── quiz-ui.js
│   ├── utils/
│   │   └── validation.js
│   └── main.js
└── data/
    └── questions.js
```

## 🏋️ Exercice 1 : État centralisé avec closure

### Étape 1.1 : Structure HTML de base

Créez `index.html` :
```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Exercice Gestion d'État</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div id="app">
        <header class="app-header">
            <h1>Quiz avec Gestion d'État</h1>
            <div class="stats">
                <span>Score: <span id="score-display">0</span></span>
                <span>Question: <span id="question-counter">0/0</span></span>
                <span>État: <span id="app-status">Prêt</span></span>
            </div>
        </header>
        
        <main id="main-content">
            <!-- Écran d'accueil -->
            <section id="welcome-screen" class="screen active">
                <h2>Bienvenue au Quiz</h2>
                <div class="user-info">
                    <input type="text" id="username" placeholder="Votre nom" required>
                    <button id="start-quiz-btn">Commencer</button>
                </div>
            </section>
            
            <!-- Écran de question -->
            <section id="question-screen" class="screen">
                <div class="question-container">
                    <h3 id="question-text"></h3>
                    <div id="answers-list"></div>
                    <div class="question-actions">
                        <button id="submit-answer-btn" disabled>Valider</button>
                        <button id="skip-question-btn">Passer</button>
                    </div>
                </div>
            </section>
            
            <!-- Écran de résultats -->
            <section id="results-screen" class="screen">
                <h2>Résultats du Quiz</h2>
                <div id="results-summary"></div>
                <div class="results-actions">
                    <button id="restart-quiz-btn">Recommencer</button>
                    <button id="view-details-btn">Voir les détails</button>
                </div>
            </section>
        </main>
        
        <!-- Zone de debug -->
        <aside id="debug-panel" class="hidden">
            <h4>Debug - État de l'application</h4>
            <pre id="state-display"></pre>
            <button id="toggle-debug">Masquer Debug</button>
        </aside>
    </div>
    
    <script type="module" src="js/main.js"></script>
</body>
</html>
```

### Étape 1.2 : CSS de base

Créez `style.css` :
```css
.screen { display: none; }
.screen.active { display: block; }
.hidden { display: none; }

#debug-panel {
    position: fixed;
    top: 10px;
    right: 10px;
    width: 300px;
    background: #f0f0f0;
    border: 1px solid #ccc;
    padding: 10px;
    font-size: 12px;
}

.stats span {
    margin-right: 15px;
    font-weight: bold;
}

.answer-option {
    margin: 10px 0;
    padding: 10px;
    border: 1px solid #ddd;
    cursor: pointer;
}

.answer-option.selected {
    background: #e3f2fd;
    border-color: #2196f3;
}
```

### Étape 1.3 : Module d'état centralisé

Créez `js/state/quiz-state.js` :
```javascript
/**
 * Module de gestion d'état centralisé pour le quiz
 * Utilise le pattern Module avec closure pour encapsuler l'état
 */
export const QuizState = (function() {
    // État privé (encapsulé dans la closure)
    let state = {
        // TODO: Définir la structure d'état initiale
        // currentQuestionIndex: 0,
        // score: 0,
        // isActive: false,
        // questions: [],
        // userAnswers: [],
        // user: null,
        // startTime: null,
        // endTime: null
    };
    
    // Listeners pour les changements d'état
    let listeners = [];
    
    // TODO: Implémenter les fonctions publiques
    
    /**
     * Récupère l'état actuel (copie pour éviter mutations)
     * @returns {Object} - Copie de l'état actuel
     */
    function getState() {
        // TODO: Retourner une copie profonde de l'état
    }
    
    /**
     * Démarre un nouveau quiz
     * @param {Array} questions - Questions du quiz
     * @param {Object} user - Informations utilisateur
     */
    function startQuiz(questions, user) {
        // TODO: Implémenter le démarrage du quiz
        // 1. Valider les paramètres
        // 2. Réinitialiser l'état
        // 3. Configurer le nouveau quiz
        // 4. Notifier les listeners
    }
    
    /**
     * Soumet une réponse pour la question courante
     * @param {number} answerIndex - Index de la réponse sélectionnée
     * @returns {Object} - Résultat de la soumission
     */
    function submitAnswer(answerIndex) {
        // TODO: Implémenter la soumission de réponse
        // 1. Valider que le quiz est actif
        // 2. Valider l'index de réponse
        // 3. Enregistrer la réponse
        // 4. Mettre à jour le score si correct
        // 5. Passer à la question suivante ou terminer
        // 6. Notifier les listeners
        // 7. Retourner le résultat
    }
    
    /**
     * Passe à la question suivante sans répondre
     */
    function skipQuestion() {
        // TODO: Implémenter le passage de question
    }
    
    /**
     * Termine le quiz
     */
    function endQuiz() {
        // TODO: Implémenter la fin du quiz
    }
    
    /**
     * Remet à zéro l'état du quiz
     */
    function resetQuiz() {
        // TODO: Implémenter la remise à zéro
    }
    
    /**
     * Ajoute un listener pour les changements d'état
     * @param {Function} callback - Fonction appelée lors des changements
     * @returns {Function} - Fonction pour se désabonner
     */
    function addListener(callback) {
        // TODO: Implémenter l'ajout de listener
        // Retourner une fonction de désabonnement
    }
    
    /**
     * Notifie tous les listeners d'un changement d'état
     * @param {string} event - Type d'événement
     * @param {Object} data - Données additionnelles
     */
    function notifyListeners(event, data = {}) {
        // TODO: Implémenter la notification
        // Gérer les erreurs dans les callbacks
    }
    
    // Interface publique du module
    return {
        // Getters
        getState,
        getCurrentQuestion: () => {
            // TODO: Retourner la question courante ou null
        },
        getScore: () => state.score,
        isActive: () => state.isActive,
        isComplete: () => {
            // TODO: Vérifier si le quiz est terminé
        },
        
        // Actions
        startQuiz,
        submitAnswer,
        skipQuestion,
        endQuiz,
        resetQuiz,
        
        // Observation
        addListener
    };
})();
```

**🎯 Objectif :** Créer un module d'état complètement encapsulé qui ne peut être modifié que par ses méthodes publiques.

## 🏋️ Exercice 2 : Actions et flux unidirectionnel

### Étape 2.1 : Définition des actions

Créez `js/state/actions.js` :
```javascript
/**
 * Définition des actions et créateurs d'actions
 */

// Types d'actions
export const ACTION_TYPES = {
    // TODO: Définir tous les types d'actions
    // START_QUIZ: 'START_QUIZ',
    // SUBMIT_ANSWER: 'SUBMIT_ANSWER',
    // SKIP_QUESTION: 'SKIP_QUESTION',
    // END_QUIZ: 'END_QUIZ',
    // RESET_QUIZ: 'RESET_QUIZ',
    // SET_USER: 'SET_USER'
};

// Créateurs d'actions
export const ActionCreators = {
    /**
     * Crée une action pour démarrer le quiz
     * @param {Array} questions - Questions du quiz
     * @param {Object} user - Informations utilisateur
     * @returns {Object} - Action
     */
    startQuiz(questions, user) {
        // TODO: Créer et retourner l'action
        // Valider les paramètres
        // Retourner { type, payload }
    },
    
    /**
     * Crée une action pour soumettre une réponse
     * @param {number} answerIndex - Index de la réponse
     * @returns {Object} - Action
     */
    submitAnswer(answerIndex) {
        // TODO: Créer l'action de soumission
    },
    
    /**
     * Crée une action pour passer une question
     * @returns {Object} - Action
     */
    skipQuestion() {
        // TODO: Créer l'action de passage
    },
    
    // TODO: Autres créateurs d'actions
};

/**
 * Dispatch une action de manière asynchrone si nécessaire
 * @param {Object|Function} action - Action ou fonction async
 * @param {Function} dispatch - Fonction dispatch
 * @param {Function} getState - Fonction pour récupérer l'état
 */
export async function dispatchAction(action, dispatch, getState) {
    // TODO: Implémenter le dispatch avec support async
    // Si action est une fonction, l'appeler avec dispatch et getState
    // Sinon, dispatcher directement
}
```

### Étape 2.2 : Validation des données

Créez `js/utils/validation.js` :
```javascript
/**
 * Fonctions de validation pour les données du quiz
 */

/**
 * Valide les données d'une question
 * @param {Object} question - Question à valider
 * @param {number} index - Index de la question
 * @returns {Object} - Résultat de validation
 */
export function validateQuestion(question, index) {
    // TODO: Implémenter la validation d'une question
    // Vérifier la structure, les types, les valeurs
    // Retourner { isValid: boolean, errors: array }
}

/**
 * Valide un tableau de questions
 * @param {Array} questions - Questions à valider
 * @returns {Object} - Résultat de validation
 */
export function validateQuestions(questions) {
    // TODO: Valider toutes les questions
    // Utiliser validateQuestion pour chaque question
}

/**
 * Valide les données utilisateur
 * @param {Object} user - Données utilisateur
 * @returns {Object} - Résultat de validation
 */
export function validateUser(user) {
    // TODO: Valider les données utilisateur
    // Nom requis, longueur minimale, caractères autorisés
}

/**
 * Valide un index de réponse
 * @param {number} answerIndex - Index à valider
 * @param {Object} question - Question correspondante
 * @returns {Object} - Résultat de validation
 */
export function validateAnswerIndex(answerIndex, question) {
    // TODO: Valider l'index de réponse
    // Vérifier que c'est un nombre, dans les limites, etc.
}
```

## 🏋️ Exercice 3 : Synchronisation État-Interface

### Étape 3.1 : Module d'interface

Créez `js/ui/quiz-ui.js` :
```javascript
/**
 * Module de gestion de l'interface utilisateur
 * Se synchronise automatiquement avec l'état
 */

export const QuizUI = (function() {
    // Éléments DOM cachés
    let elements = {};
    
    /**
     * Initialise le module UI
     * @param {Object} stateManager - Gestionnaire d'état
     */
    function initialize(stateManager) {
        // TODO: Implémenter l'initialisation
        // 1. Récupérer tous les éléments DOM
        // 2. S'abonner aux changements d'état
        // 3. Configurer les événements utilisateur
    }
    
    /**
     * Récupère et cache les éléments DOM
     */
    function cacheElements() {
        elements = {
            // TODO: Récupérer tous les éléments nécessaires
            // scoreDisplay: document.querySelector('#score-display'),
            // questionCounter: document.querySelector('#question-counter'),
            // appStatus: document.querySelector('#app-status'),
            // screens: {
            //     welcome: document.querySelector('#welcome-screen'),
            //     question: document.querySelector('#question-screen'),
            //     results: document.querySelector('#results-screen')
            // },
            // etc.
        };
        
        // Vérifier que tous les éléments existent
        validateElements();
    }
    
    /**
     * Valide que tous les éléments DOM requis existent
     */
    function validateElements() {
        // TODO: Vérifier l'existence des éléments critiques
        // Logger les éléments manquants
    }
    
    /**
     * Gère les changements d'état
     * @param {string} event - Type d'événement
     * @param {Object} state - Nouvel état
     */
    function handleStateChange(event, state) {
        console.log('UI: Changement d\'état reçu:', event);
        
        // TODO: Implémenter la gestion des changements
        switch(event) {
            case 'QUIZ_STARTED':
                // TODO: Afficher l'écran de question
                // Mettre à jour les compteurs
                // Afficher la première question
                break;
                
            case 'ANSWER_SUBMITTED':
                // TODO: Mettre à jour le score
                // Afficher la question suivante ou les résultats
                break;
                
            case 'QUIZ_COMPLETED':
                // TODO: Afficher l'écran de résultats
                break;
                
            case 'QUIZ_RESET':
                // TODO: Retourner à l'écran d'accueil
                break;
                
            default:
                console.warn('Événement non géré:', event);
        }
        
        // Toujours mettre à jour l'affichage de l'état
        updateStateDisplay(state);
    }
    
    /**
     * Affiche une question
     * @param {Object} question - Question à afficher
     * @param {number} questionIndex - Index de la question
     * @param {number} totalQuestions - Nombre total de questions
     */
    function displayQuestion(question, questionIndex, totalQuestions) {
        // TODO: Implémenter l'affichage de question
        // 1. Mettre à jour le texte de la question
        // 2. Créer les options de réponse
        // 3. Mettre à jour le compteur
        // 4. Réinitialiser les boutons
    }
    
    /**
     * Crée les options de réponse pour une question
     * @param {Object} question - Question
     * @returns {DocumentFragment} - Fragment contenant les options
     */
    function createAnswerOptions(question) {
        // TODO: Créer les options de réponse
        // Utiliser la délégation d'événements
        // Retourner un DocumentFragment
    }
    
    /**
     * Affiche l'écran spécifié
     * @param {string} screenName - Nom de l'écran à afficher
     */
    function showScreen(screenName) {
        // TODO: Gérer l'affichage des écrans
        // Masquer tous les écrans
        // Afficher l'écran demandé
    }
    
    /**
     * Met à jour l'affichage de l'état (debug)
     * @param {Object} state - État actuel
     */
    function updateStateDisplay(state) {
        // TODO: Mettre à jour le panneau de debug
        // Afficher l'état formaté en JSON
    }
    
    // Interface publique
    return {
        initialize,
        showScreen,
        displayQuestion,
        updateStateDisplay
    };
})();
```

### Étape 3.2 : Données de test

Créez `data/questions.js` :
```javascript
/**
 * Questions de test pour le quiz
 */
export const sampleQuestions = [
    {
        id: 'q1',
        question: 'Quelle est la meilleure façon de gérer l\'état dans une application JavaScript ?',
        answers: [
            'Variables globales dispersées',
            'État centralisé avec validation',
            'Stockage dans le DOM',
            'Variables dans le localStorage'
        ],
        correctAnswer: { id: 1, answer: 'État centralisé avec validation' },
        explanation: 'Un état centralisé permet un meilleur contrôle et une meilleure prévisibilité.'
    },
    {
        id: 'q2',
        question: 'Que signifie "flux unidirectionnel" dans la gestion d\'état ?',
        answers: [
            'Les données circulent dans un seul sens',
            'Une seule fonction peut modifier l\'état',
            'L\'interface ne peut pas modifier l\'état',
            'Les actions sont synchrones uniquement'
        ],
        correctAnswer: { id: 0, answer: 'Les données circulent dans un seul sens' },
        explanation: 'Le flux unidirectionnel suit le pattern Action → État → Interface.'
    },
    {
        id: 'q3',
        question: 'Pourquoi valider les données avant de modifier l\'état ?',
        answers: [
            'Pour améliorer les performances',
            'Pour éviter les états incohérents',
            'Pour réduire la taille du code',
            'Pour faciliter le debug uniquement'
        ],
        correctAnswer: { id: 1, answer: 'Pour éviter les états incohérents' },
        explanation: 'La validation empêche les données invalides de corrompre l\'état.'
    }
];
```

## 🏋️ Exercice 4 : Intégration complète

### Étape 4.1 : Point d'entrée principal

Créez `js/main.js` :
```javascript
/**
 * Point d'entrée principal de l'application
 */

import { QuizState } from './state/quiz-state.js';
import { QuizUI } from './ui/quiz-ui.js';
import { ActionCreators } from './state/actions.js';
import { sampleQuestions } from '../data/questions.js';

/**
 * Contrôleur principal de l'application
 */
const AppController = (function() {
    let isInitialized = false;
    
    /**
     * Initialise l'application
     */
    function initialize() {
        if (isInitialized) return;
        
        console.log('🚀 Initialisation de l\'application...');
        
        try {
            // TODO: Initialiser l'application
            // 1. Initialiser l'interface
            // 2. Configurer les événements utilisateur
            // 3. Activer le mode debug si nécessaire
            // 4. Marquer comme initialisé
            
            console.log('✅ Application initialisée avec succès');
            
        } catch (error) {
            console.error('❌ Erreur lors de l\'initialisation:', error);
            showCriticalError(error);
        }
    }
    
    /**
     * Configure les événements utilisateur
     */
    function setupUserEvents() {
        // TODO: Configurer tous les événements
        // Bouton démarrer quiz
        // Bouton valider réponse
        // Bouton passer question
        // Bouton recommencer
        // etc.
    }
    
    /**
     * Gère le démarrage du quiz
     */
    function handleStartQuiz() {
        // TODO: Implémenter le démarrage
        // 1. Récupérer le nom d'utilisateur
        // 2. Valider les données
        // 3. Dispatcher l'action START_QUIZ
    }
    
    /**
     * Gère la soumission d'une réponse
     */
    function handleSubmitAnswer() {
        // TODO: Implémenter la soumission
        // 1. Récupérer la réponse sélectionnée
        // 2. Valider la sélection
        // 3. Dispatcher l'action SUBMIT_ANSWER
    }
    
    /**
     * Active/désactive le mode debug
     */
    function toggleDebugMode() {
        // TODO: Basculer l'affichage du panneau debug
    }
    
    /**
     * Affiche une erreur critique
     * @param {Error} error - Erreur à afficher
     */
    function showCriticalError(error) {
        // TODO: Afficher une interface d'erreur
        // Permettre le rechargement de la page
    }
    
    return {
        initialize,
        toggleDebugMode
    };
})();

// Initialisation au chargement du DOM
if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', AppController.initialize);
} else {
    AppController.initialize();
}

// Exposer le contrôleur pour le debug
window.AppController = AppController;
window.QuizState = QuizState;
```

## 🧪 Tests à effectuer

### Test 1 : État centralisé
```javascript
// TODO: Créer des tests pour vérifier :
// - L'état ne peut pas être modifié directement
// - Les actions modifient l'état correctement
// - Les listeners sont notifiés des changements
```

### Test 2 : Validation des données
```javascript
// TODO: Tester la validation avec :
// - Données valides
// - Données invalides
// - Cas limites
```

### Test 3 : Synchronisation UI
```javascript
// TODO: Vérifier que :
// - L'interface se met à jour avec l'état
// - Les changements d'état sont reflétés visuellement
// - Les interactions utilisateur déclenchent les bonnes actions
```

## 🎯 Critères de réussite

- [ ] L'état est complètement encapsulé
- [ ] Toutes les modifications passent par des actions
- [ ] L'interface se synchronise automatiquement
- [ ] Les données sont validées avant modification
- [ ] Le flux est unidirectionnel et prévisible
- [ ] Le debug permet de suivre les changements
- [ ] L'application gère les erreurs gracieusement

## 💡 Conseils

1. **Commencez simple** - Implémentez une action à la fois
2. **Testez chaque étape** - Vérifiez que l'état change correctement
3. **Utilisez le debug** - Affichez l'état à chaque changement
4. **Validez tout** - Ne faites jamais confiance aux données externes

---

**Prochaine étape :** Consultez les solutions pour comparer vos implémentations !
