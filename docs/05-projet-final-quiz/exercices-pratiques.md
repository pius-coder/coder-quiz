# Projet Final - Quiz JavaScript Complet

## 🎯 Mission
Créer une application de quiz JavaScript complète en appliquant toutes les bonnes pratiques apprises. Ce projet synthétise tous les concepts des exercices précédents.

## 📋 Checklist de démarrage

Avant de commencer, assurez-vous d'avoir :
- [ ] Terminé les exercices 1-4 ou consulté leurs solutions
- [ ] Compris les concepts de manipulation DOM, événements, état et erreurs
- [ ] Un éditeur de code configuré
- [ ] Un serveur local pour tester (Live Server, etc.)

## 🏗️ Phase 1 : Structure et fondations

### Étape 1.1 : Structure HTML complète

Créez `index.html` avec une structure sémantique :

```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Quiz JavaScript - Projet Final</title>
    <meta name="description" content="Testez vos connaissances en JavaScript avec ce quiz interactif">
    <link rel="stylesheet" href="style.css">
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
</head>
<body class="theme-light">
    <div id="app" class="app">
        <!-- Header global -->
        <header class="app-header">
            <div class="header-content">
                <h1 class="app-title">
                    <span class="title-icon">🧠</span>
                    Quiz JavaScript
                </h1>
                <div class="header-controls">
                    <div class="score-display" id="score-display">
                        Score: <span id="current-score">0</span>
                    </div>
                    <button id="theme-toggle" class="theme-toggle" aria-label="Changer de thème">
                        <span class="theme-icon">🌙</span>
                    </button>
                    <button id="debug-toggle" class="debug-toggle" aria-label="Mode debug">
                        <span class="debug-icon">🐛</span>
                    </button>
                </div>
            </div>
            <div class="progress-bar" id="progress-bar">
                <div class="progress-fill" id="progress-fill"></div>
            </div>
        </header>

        <!-- Contenu principal -->
        <main class="app-main" id="app-main">
            <!-- TODO: Créer tous les écrans nécessaires -->
            <!-- Écran d'accueil -->
            <section id="welcome-screen" class="screen active">
                <!-- TODO: Implémenter l'écran d'accueil -->
            </section>

            <!-- Écran de configuration -->
            <section id="config-screen" class="screen">
                <!-- TODO: Implémenter la configuration du quiz -->
            </section>

            <!-- Écran de quiz -->
            <section id="quiz-screen" class="screen">
                <!-- TODO: Implémenter l'écran de quiz -->
            </section>

            <!-- Écran de résultats -->
            <section id="results-screen" class="screen">
                <!-- TODO: Implémenter l'écran de résultats -->
            </section>

            <!-- Écran d'erreur -->
            <section id="error-screen" class="screen">
                <!-- TODO: Implémenter l'écran d'erreur -->
            </section>
        </main>

        <!-- Footer -->
        <footer class="app-footer">
            <p>&copy; 2024 Quiz JavaScript - Projet d'apprentissage</p>
        </footer>

        <!-- Zones dynamiques -->
        <div id="feedback-container" class="feedback-container"></div>
        <div id="modal-container" class="modal-container"></div>
        
        <!-- Panneau de debug -->
        <aside id="debug-panel" class="debug-panel hidden">
            <!-- TODO: Implémenter le panneau de debug -->
        </aside>
    </div>

    <script type="module" src="js/main.js"></script>
</body>
</html>
```

**🎯 Mission :** Complétez chaque section TODO avec le HTML approprié.

### Étape 1.2 : CSS avec variables et thèmes

Créez `style.css` avec un système de design cohérent :

```css
/* TODO: Implémenter le système CSS complet */

/* Variables CSS pour les thèmes */
:root {
    /* Couleurs principales */
    --primary-color: #2563eb;
    --primary-hover: #1d4ed8;
    --secondary-color: #64748b;
    
    /* Couleurs de feedback */
    --success-color: #10b981;
    --error-color: #ef4444;
    --warning-color: #f59e0b;
    --info-color: #3b82f6;
    
    /* Couleurs de fond */
    --bg-primary: #ffffff;
    --bg-secondary: #f8fafc;
    --bg-tertiary: #e2e8f0;
    
    /* Couleurs de texte */
    --text-primary: #1e293b;
    --text-secondary: #64748b;
    --text-muted: #94a3b8;
    
    /* Ombres */
    --shadow-sm: 0 1px 2px 0 rgb(0 0 0 / 0.05);
    --shadow-md: 0 4px 6px -1px rgb(0 0 0 / 0.1);
    --shadow-lg: 0 10px 15px -3px rgb(0 0 0 / 0.1);
    
    /* Rayons de bordure */
    --radius-sm: 0.375rem;
    --radius-md: 0.5rem;
    --radius-lg: 0.75rem;
    
    /* Espacements */
    --spacing-xs: 0.25rem;
    --spacing-sm: 0.5rem;
    --spacing-md: 1rem;
    --spacing-lg: 1.5rem;
    --spacing-xl: 2rem;
    
    /* Transitions */
    --transition-fast: 0.15s ease-out;
    --transition-normal: 0.3s ease-out;
    --transition-slow: 0.5s ease-out;
}

/* Thème sombre */
.theme-dark {
    --bg-primary: #0f172a;
    --bg-secondary: #1e293b;
    --bg-tertiary: #334155;
    
    --text-primary: #f1f5f9;
    --text-secondary: #cbd5e1;
    --text-muted: #64748b;
}

/* TODO: Implémenter tous les styles nécessaires */
/* - Reset CSS */
/* - Styles de base */
/* - Layout responsive */
/* - Composants (boutons, cartes, formulaires) */
/* - États et animations */
/* - Styles des écrans */
/* - Media queries */
```

### Étape 1.3 : Architecture JavaScript modulaire

Créez la structure de modules JavaScript :

#### `js/main.js` - Point d'entrée
```javascript
/**
 * Point d'entrée principal de l'application Quiz
 * Initialise tous les modules et gère le cycle de vie de l'app
 */

// TODO: Importer tous les modules nécessaires
// import { AppController } from './core/app-controller.js';
// import { ErrorManager } from './core/error-manager.js';
// import { ThemeService } from './services/theme-service.js';

/**
 * Initialise l'application
 */
async function initializeApp() {
    try {
        console.log('🚀 Initialisation de l\'application Quiz...');
        
        // TODO: Implémenter l'initialisation complète
        // 1. Vérifier les prérequis (DOM, localStorage, etc.)
        // 2. Initialiser les services
        // 3. Configurer la gestion d'erreurs globale
        // 4. Charger les préférences utilisateur
        // 5. Initialiser le contrôleur principal
        // 6. Afficher l'écran d'accueil
        
        console.log('✅ Application initialisée avec succès');
        
    } catch (error) {
        console.error('❌ Erreur critique lors de l\'initialisation:', error);
        showCriticalError(error);
    }
}

/**
 * Affiche un écran d'erreur critique
 */
function showCriticalError(error) {
    // TODO: Implémenter l'affichage d'erreur critique
    // Interface de fallback si l'app ne peut pas démarrer
}

/**
 * Vérifie les prérequis de l'application
 */
function checkPrerequisites() {
    // TODO: Vérifier :
    // - Support JavaScript moderne (ES6+)
    // - localStorage disponible
    // - Éléments DOM requis présents
    // - Polyfills nécessaires
}

// Initialisation au chargement du DOM
if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', initializeApp);
} else {
    initializeApp();
}
```

## 🏗️ Phase 2 : Modules core

### Étape 2.1 : Contrôleur principal

Créez `js/core/app-controller.js` :

```javascript
/**
 * Contrôleur principal de l'application
 * Orchestre tous les autres modules
 */

export const AppController = (function() {
    // TODO: Implémenter le contrôleur principal
    
    let isInitialized = false;
    let currentScreen = 'welcome';
    let modules = {};
    
    /**
     * Initialise le contrôleur et tous les modules
     */
    async function initialize() {
        // TODO: Initialiser tous les modules
        // - QuizState
        // - ScreenManager  
        // - ErrorManager
        // - FeedbackManager
        // - Services (Storage, Theme, Quiz)
    }
    
    /**
     * Démarre un nouveau quiz
     */
    async function startQuiz(config) {
        // TODO: Démarrer le quiz avec la configuration
    }
    
    /**
     * Gère la navigation entre écrans
     */
    function navigateToScreen(screenName, data = {}) {
        // TODO: Navigation avec validation et animations
    }
    
    /**
     * Gère les actions utilisateur globales
     */
    function handleGlobalAction(action, data = {}) {
        // TODO: Dispatcher les actions globales
    }
    
    return {
        initialize,
        startQuiz,
        navigateToScreen,
        handleGlobalAction
    };
})();
```

### Étape 2.2 : Gestion d'état centralisée

Créez `js/core/quiz-state.js` :

```javascript
/**
 * Gestion d'état centralisée pour le quiz
 * Utilise le pattern Module avec état immutable
 */

export const QuizState = (function() {
    // TODO: Implémenter la gestion d'état complète
    
    // État initial
    let state = {
        config: {
            difficulty: 'medium',
            questionsCount: 10,
            timePerQuestion: 30,
            theme: 'auto'
        },
        user: {
            name: '',
            preferences: {}
        },
        quiz: {
            isActive: false,
            currentQuestionIndex: 0,
            questions: [],
            userAnswers: [],
            score: 0,
            startTime: null,
            endTime: null
        },
        ui: {
            currentScreen: 'welcome',
            isLoading: false,
            errors: []
        }
    };
    
    let listeners = [];
    
    /**
     * Actions pour modifier l'état
     */
    const actions = {
        // TODO: Implémenter toutes les actions
        SET_USER: 'SET_USER',
        START_QUIZ: 'START_QUIZ',
        SUBMIT_ANSWER: 'SUBMIT_ANSWER',
        END_QUIZ: 'END_QUIZ',
        SET_SCREEN: 'SET_SCREEN',
        SET_LOADING: 'SET_LOADING',
        ADD_ERROR: 'ADD_ERROR'
    };
    
    /**
     * Dispatch une action
     */
    function dispatch(action, payload) {
        // TODO: Implémenter le dispatcher avec validation
    }
    
    /**
     * Réducteur pour gérer les changements d'état
     */
    function reducer(currentState, action) {
        // TODO: Implémenter le réducteur complet
    }
    
    return {
        getState: () => ({ ...state }),
        dispatch,
        subscribe: (callback) => {
            listeners.push(callback);
            return () => {
                listeners = listeners.filter(l => l !== callback);
            };
        },
        actions
    };
})();
```

## 🏗️ Phase 3 : Interface utilisateur

### Étape 3.1 : Gestionnaire d'écrans

Créez `js/ui/screen-manager.js` :

```javascript
/**
 * Gestionnaire d'écrans avec animations
 */

export const ScreenManager = (function() {
    // TODO: Implémenter la gestion des écrans
    
    let currentScreen = null;
    let screens = new Map();
    let isTransitioning = false;
    
    /**
     * Enregistre un écran
     */
    function registerScreen(name, element, controller = null) {
        // TODO: Enregistrer l'écran avec son contrôleur
    }
    
    /**
     * Affiche un écran avec animation
     */
    async function showScreen(screenName, data = {}, animation = 'fade') {
        // TODO: Transition animée entre écrans
    }
    
    /**
     * Animations disponibles
     */
    const animations = {
        fade: {
            // TODO: Animation de fondu
        },
        slide: {
            // TODO: Animation de glissement
        },
        scale: {
            // TODO: Animation de zoom
        }
    };
    
    return {
        registerScreen,
        showScreen,
        getCurrentScreen: () => currentScreen
    };
})();
```

### Étape 3.2 : Rendu des questions

Créez `js/ui/question-renderer.js` :

```javascript
/**
 * Rendu des questions avec gestion des interactions
 */

export const QuestionRenderer = (function() {
    // TODO: Implémenter le rendu des questions
    
    /**
     * Rend une question complète
     */
    function renderQuestion(question, questionIndex, totalQuestions) {
        // TODO: Créer l'interface de question
        // - Numéro et progression
        // - Texte de la question
        // - Options de réponse
        // - Timer si activé
        // - Boutons d'action
    }
    
    /**
     * Crée les options de réponse
     */
    function createAnswerOptions(question, questionIndex) {
        // TODO: Créer les options avec délégation d'événements
    }
    
    /**
     * Met à jour l'affichage du timer
     */
    function updateTimer(timeRemaining, totalTime) {
        // TODO: Mettre à jour le timer avec animation
    }
    
    /**
     * Affiche le feedback pour une réponse
     */
    function showAnswerFeedback(isCorrect, correctAnswer, explanation) {
        // TODO: Feedback visuel avec animation
    }
    
    return {
        renderQuestion,
        updateTimer,
        showAnswerFeedback
    };
})();
```

## 🏗️ Phase 4 : Services et utilitaires

### Étape 4.1 : Service de données

Créez `js/services/quiz-service.js` :

```javascript
/**
 * Service de gestion des données de quiz
 */

export const QuizService = (function() {
    // TODO: Implémenter le service de données
    
    /**
     * Charge les questions selon la difficulté
     */
    async function loadQuestions(difficulty, count) {
        // TODO: Charger et mélanger les questions
    }
    
    /**
     * Valide une réponse
     */
    function validateAnswer(question, answerIndex) {
        // TODO: Validation avec calcul de score
    }
    
    /**
     * Calcule le score final
     */
    function calculateFinalScore(answers, config) {
        // TODO: Calcul avec bonus de temps
    }
    
    return {
        loadQuestions,
        validateAnswer,
        calculateFinalScore
    };
})();
```

### Étape 4.2 : Service de stockage

Créez `js/services/storage-service.js` :

```javascript
/**
 * Service de persistance locale
 */

export const StorageService = (function() {
    // TODO: Implémenter la persistance
    
    const STORAGE_KEYS = {
        USER_PREFERENCES: 'quiz_user_preferences',
        QUIZ_HISTORY: 'quiz_history',
        HIGH_SCORES: 'quiz_high_scores'
    };
    
    /**
     * Sauvegarde les préférences utilisateur
     */
    function saveUserPreferences(preferences) {
        // TODO: Sauvegarder avec validation
    }
    
    /**
     * Charge les préférences utilisateur
     */
    function loadUserPreferences() {
        // TODO: Charger avec valeurs par défaut
    }
    
    /**
     * Sauvegarde un résultat de quiz
     */
    function saveQuizResult(result) {
        // TODO: Ajouter à l'historique
    }
    
    /**
     * Récupère l'historique des quiz
     */
    function getQuizHistory(limit = 10) {
        // TODO: Récupérer l'historique trié
    }
    
    return {
        saveUserPreferences,
        loadUserPreferences,
        saveQuizResult,
        getQuizHistory
    };
})();
```

## 🧪 Phase 5 : Tests et validation

### Étape 5.1 : Tests fonctionnels

Créez une suite de tests pour valider votre application :

```javascript
/**
 * Suite de tests pour l'application Quiz
 */

// TODO: Implémenter les tests
const TestSuite = {
    async runAllTests() {
        console.log('🧪 Démarrage des tests...');
        
        await this.testInitialization();
        await this.testUserFlow();
        await this.testErrorHandling();
        await this.testDataPersistence();
        
        console.log('✅ Tous les tests terminés');
    },
    
    async testInitialization() {
        // TODO: Tester l'initialisation
    },
    
    async testUserFlow() {
        // TODO: Tester le parcours utilisateur complet
    },
    
    async testErrorHandling() {
        // TODO: Tester la gestion d'erreurs
    },
    
    async testDataPersistence() {
        // TODO: Tester la sauvegarde/chargement
    }
};

// Exposer pour les tests manuels
window.TestSuite = TestSuite;
```

## 🎯 Critères de validation

### Fonctionnalités obligatoires
- [ ] Écran d'accueil avec saisie du nom
- [ ] Configuration du quiz (difficulté, nombre de questions)
- [ ] Affichage séquentiel des questions
- [ ] Validation des réponses avec feedback
- [ ] Calcul et affichage du score
- [ ] Écran de résultats avec statistiques
- [ ] Sauvegarde des préférences
- [ ] Gestion d'erreurs robuste
- [ ] Interface responsive

### Fonctionnalités avancées (bonus)
- [ ] Timer par question
- [ ] Thème sombre/clair
- [ ] Animations fluides
- [ ] Mode debug
- [ ] Export des résultats
- [ ] Historique des scores
- [ ] Sons de feedback
- [ ] Raccourcis clavier

### Qualité du code
- [ ] Architecture modulaire respectée
- [ ] Gestion d'état centralisée
- [ ] Gestion d'erreurs complète
- [ ] Code documenté et lisible
- [ ] Validation des données
- [ ] Performance optimisée

## 💡 Conseils de développement

### Organisation du travail
1. **Développez par phases** - Ne passez pas à la phase suivante tant que la précédente n'est pas stable
2. **Testez continuellement** - Vérifiez chaque fonctionnalité avant d'ajouter la suivante
3. **Commitez souvent** - Sauvegardez votre progression avec Git
4. **Documentez** - Commentez les parties complexes

### Debug et optimisation
1. **Utilisez le mode debug** - Affichez l'état de l'application
2. **Console du navigateur** - Surveillez les erreurs et warnings
3. **Performance** - Utilisez les DevTools pour identifier les goulots d'étranglement
4. **Tests utilisateur** - Faites tester par d'autres personnes

### Finalisation
1. **Tests complets** - Testez tous les scénarios possibles
2. **Optimisation** - Minifiez le CSS/JS si nécessaire
3. **Documentation** - Rédigez un README complet
4. **Déploiement** - Testez sur différents navigateurs

---

**Bonne chance pour votre projet final ! 🚀**
