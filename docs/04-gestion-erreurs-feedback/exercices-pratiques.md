# Exercices Pratiques - Gestion d'Erreurs et Feedback Utilisateur

## 🎯 Instructions
Ces exercices vous apprendront à créer une expérience utilisateur robuste avec une gestion d'erreurs appropriée. Consultez le `guide-concepts.md` pour les concepts.

## 📁 Structure des fichiers
```
exercice-errors/
├── index.html
├── style.css
├── js/
│   ├── core/
│   │   ├── error-manager.js
│   │   ├── feedback-manager.js
│   │   └── ui-state-manager.js
│   ├── utils/
│   │   ├── logger.js
│   │   └── validators.js
│   ├── services/
│   │   └── quiz-service.js
│   └── main.js
└── data/
    └── questions.json
```

## 🏋️ Exercice 1 : Gestionnaire d'erreurs centralisé

### Étape 1.1 : Structure HTML avec zones d'erreur

Créez `index.html` :
```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Exercice Gestion d'Erreurs</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div id="app">
        <!-- Barre de statut global -->
        <div id="status-bar" class="status-bar">
            <span id="connection-status" class="status-indicator">●</span>
            <span id="app-status">Prêt</span>
            <button id="debug-toggle">Debug</button>
        </div>
        
        <header class="app-header">
            <h1>Quiz avec Gestion d'Erreurs</h1>
            <div class="error-summary" id="error-summary" style="display: none;">
                <span id="error-count">0</span> erreur(s) détectée(s)
                <button id="show-errors">Voir</button>
            </div>
        </header>
        
        <main id="main-content">
            <!-- Écran de chargement -->
            <section id="loading-screen" class="screen">
                <div class="loading-spinner"></div>
                <p id="loading-message">Chargement de l'application...</p>
            </section>
            
            <!-- Écran principal -->
            <section id="main-screen" class="screen hidden">
                <div class="quiz-container">
                    <h2>Testez la gestion d'erreurs</h2>
                    
                    <!-- Boutons de test d'erreurs -->
                    <div class="test-buttons">
                        <button id="test-network-error" class="test-btn">
                            Simuler erreur réseau
                        </button>
                        <button id="test-validation-error" class="test-btn">
                            Simuler erreur validation
                        </button>
                        <button id="test-critical-error" class="test-btn">
                            Simuler erreur critique
                        </button>
                        <button id="test-async-error" class="test-btn">
                            Simuler erreur async
                        </button>
                    </div>
                    
                    <!-- Formulaire de test -->
                    <form id="test-form" class="test-form">
                        <div class="form-group">
                            <label for="username">Nom d'utilisateur:</label>
                            <input type="text" id="username" name="username" required>
                            <div class="field-error" id="username-error"></div>
                        </div>
                        
                        <div class="form-group">
                            <label for="email">Email:</label>
                            <input type="email" id="email" name="email" required>
                            <div class="field-error" id="email-error"></div>
                        </div>
                        
                        <button type="submit" id="submit-form">Valider</button>
                    </form>
                </div>
            </section>
            
            <!-- Écran d'erreur critique -->
            <section id="critical-error-screen" class="screen hidden">
                <div class="critical-error">
                    <h2>⚠️ Erreur Critique</h2>
                    <p id="critical-error-message"></p>
                    <div class="error-actions">
                        <button id="reload-app">Recharger l'application</button>
                        <button id="report-error">Signaler l'erreur</button>
                    </div>
                </div>
            </section>
        </main>
        
        <!-- Zone de feedback (sera créée dynamiquement) -->
        <div id="feedback-container"></div>
        
        <!-- Panneau de debug -->
        <aside id="debug-panel" class="debug-panel hidden">
            <h3>Debug - Gestion d'Erreurs</h3>
            <div class="debug-section">
                <h4>Journal des erreurs</h4>
                <div id="error-log"></div>
                <button id="clear-errors">Vider le journal</button>
            </div>
            <div class="debug-section">
                <h4>État de l'application</h4>
                <pre id="app-state"></pre>
            </div>
        </aside>
    </div>
    
    <script type="module" src="js/main.js"></script>
</body>
</html>
```

### Étape 1.2 : CSS pour les états d'erreur

Créez `style.css` :
```css
/* États globaux */
.state-loading { cursor: wait; }
.state-error { border-left: 4px solid #f44336; }
.state-success { border-left: 4px solid #4caf50; }

/* Feedback messages */
.feedback-container {
    position: fixed;
    top: 20px;
    right: 20px;
    z-index: 1000;
    max-width: 400px;
}

.feedback-message {
    margin-bottom: 10px;
    padding: 12px 16px;
    border-radius: 4px;
    box-shadow: 0 2px 8px rgba(0,0,0,0.1);
    transform: translateX(100%);
    transition: transform 0.3s ease-out;
}

.feedback-message.show {
    transform: translateX(0);
}

.feedback-message.hide {
    transform: translateX(100%);
}

.feedback-success {
    background: #d4edda;
    color: #155724;
    border: 1px solid #c3e6cb;
}

.feedback-error {
    background: #f8d7da;
    color: #721c24;
    border: 1px solid #f5c6cb;
}

.feedback-warning {
    background: #fff3cd;
    color: #856404;
    border: 1px solid #ffeaa7;
}

.feedback-info {
    background: #d1ecf1;
    color: #0c5460;
    border: 1px solid #bee5eb;
}

/* Erreurs de champs */
.field-error {
    color: #f44336;
    font-size: 0.8em;
    margin-top: 4px;
    display: none;
}

.field-error.show {
    display: block;
}

.form-group.error input {
    border-color: #f44336;
    background-color: #ffebee;
}

/* Debug panel */
.debug-panel {
    position: fixed;
    bottom: 20px;
    left: 20px;
    width: 400px;
    max-height: 300px;
    background: #f5f5f5;
    border: 1px solid #ddd;
    padding: 15px;
    font-size: 12px;
    overflow-y: auto;
}

/* Loading spinner */
.loading-spinner {
    border: 4px solid #f3f3f3;
    border-top: 4px solid #3498db;
    border-radius: 50%;
    width: 40px;
    height: 40px;
    animation: spin 2s linear infinite;
    margin: 0 auto 20px;
}

@keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
}
```

### Étape 1.3 : Gestionnaire d'erreurs centralisé

Créez `js/core/error-manager.js` :
```javascript
/**
 * Gestionnaire d'erreurs centralisé
 * Gère la classification, le logging et la notification des erreurs
 */

// Types de sévérité des erreurs
export const ErrorSeverity = {
    CRITICAL: 'critical',
    HIGH: 'high',
    MEDIUM: 'medium',
    LOW: 'low'
};

// Types d'erreurs personnalisés
export class ValidationError extends Error {
    constructor(message, field = null) {
        super(message);
        this.name = 'ValidationError';
        this.field = field;
    }
}

export class NetworkError extends Error {
    constructor(message, status = null) {
        super(message);
        this.name = 'NetworkError';
        this.status = status;
    }
}

export class CriticalError extends Error {
    constructor(message) {
        super(message);
        this.name = 'CriticalError';
    }
}

export const ErrorManager = (function() {
    // TODO: Implémenter le gestionnaire d'erreurs
    let errorLog = [];
    let errorHandlers = new Map();
    let listeners = [];
    
    /**
     * Classifie une erreur selon sa sévérité
     * @param {Error} error - Erreur à classifier
     * @returns {string} - Niveau de sévérité
     */
    function classifyError(error) {
        // TODO: Implémenter la classification
        // - CriticalError → CRITICAL
        // - NetworkError → HIGH
        // - ValidationError → MEDIUM
        // - Autres → LOW
    }
    
    /**
     * Gère une erreur de manière centralisée
     * @param {Error} error - Erreur à gérer
     * @param {string} context - Contexte de l'erreur
     * @param {Object} metadata - Métadonnées additionnelles
     */
    function handleError(error, context = '', metadata = {}) {
        // TODO: Implémenter la gestion d'erreur
        // 1. Créer une entrée de log
        // 2. Classifier l'erreur
        // 3. Logger dans la console
        // 4. Appeler les gestionnaires spécifiques
        // 5. Notifier les listeners
        // 6. Afficher le feedback utilisateur
    }
    
    /**
     * Enregistre un gestionnaire pour un type d'erreur
     * @param {string} errorType - Type d'erreur
     * @param {Function} handler - Gestionnaire
     */
    function registerHandler(errorType, handler) {
        // TODO: Enregistrer le gestionnaire
    }
    
    /**
     * Crée un wrapper sécurisé pour une fonction
     * @param {Function} func - Fonction à sécuriser
     * @param {string} context - Contexte pour le debug
     * @returns {Function} - Fonction sécurisée
     */
    function createSafeWrapper(func, context) {
        // TODO: Créer le wrapper
        // Gérer les erreurs sync et async
        // Re-throw après gestion pour permettre gestion locale
    }
    
    /**
     * Ajoute un listener pour les erreurs
     * @param {Function} callback - Callback à appeler
     * @returns {Function} - Fonction de désabonnement
     */
    function addListener(callback) {
        // TODO: Ajouter le listener
        // Retourner fonction de désabonnement
    }
    
    /**
     * Récupère le journal des erreurs
     * @param {string} severity - Filtrer par sévérité (optionnel)
     * @returns {Array} - Journal filtré
     */
    function getErrorLog(severity = null) {
        // TODO: Retourner le journal, filtré si nécessaire
    }
    
    /**
     * Vide le journal des erreurs
     */
    function clearErrorLog() {
        // TODO: Vider le journal et notifier
    }
    
    // Interface publique
    return {
        handleError,
        registerHandler,
        createSafeWrapper,
        addListener,
        getErrorLog,
        clearErrorLog,
        
        // Classes d'erreurs
        ValidationError,
        NetworkError,
        CriticalError,
        ErrorSeverity
    };
})();
```

**🎯 Objectif :** Créer un système centralisé qui capture, classifie et gère toutes les erreurs de l'application.

## 🏋️ Exercice 2 : Système de feedback utilisateur

### Étape 2.1 : Gestionnaire de feedback

Créez `js/core/feedback-manager.js` :
```javascript
/**
 * Gestionnaire de feedback utilisateur
 * Affiche des messages, indicateurs de chargement, confirmations
 */

export const FeedbackManager = (function() {
    let container = null;
    let activeMessages = new Set();
    let loadingElements = new Set();
    
    /**
     * Initialise le gestionnaire de feedback
     */
    function initialize() {
        // TODO: Créer le container s'il n'existe pas
        // L'ajouter au DOM
    }
    
    /**
     * Affiche un message de feedback
     * @param {string} message - Message à afficher
     * @param {string} type - Type: 'success', 'error', 'warning', 'info'
     * @param {number} duration - Durée d'affichage (0 = permanent)
     * @param {Object} options - Options additionnelles
     * @returns {HTMLElement} - Élément créé
     */
    function show(message, type = 'info', duration = 5000, options = {}) {
        // TODO: Implémenter l'affichage de message
        // 1. Initialiser si nécessaire
        // 2. Créer l'élément de message
        // 3. L'ajouter au container
        // 4. Gérer l'animation d'entrée
        // 5. Programmer la suppression automatique
        // 6. Retourner l'élément
    }
    
    /**
     * Crée un élément de message
     * @param {string} message - Message
     * @param {string} type - Type
     * @param {Object} options - Options
     * @returns {HTMLElement} - Élément créé
     */
    function createMessageElement(message, type, options) {
        // TODO: Créer l'élément avec :
        // - Classes CSS appropriées
        // - Contenu du message
        // - Bouton de fermeture (si options.closable !== false)
        // - Actions personnalisées (si options.actions)
        // - Icône (si options.icon)
    }
    
    /**
     * Masque un message
     * @param {HTMLElement} messageElement - Élément à masquer
     */
    function hide(messageElement) {
        // TODO: Masquer avec animation
        // Supprimer du DOM après animation
        // Nettoyer les références
    }
    
    /**
     * Affiche un indicateur de chargement
     * @param {string} message - Message de chargement
     * @param {Object} options - Options
     * @returns {HTMLElement} - Élément de chargement
     */
    function showLoading(message = 'Chargement...', options = {}) {
        // TODO: Créer un indicateur de chargement
        // Avec spinner et message
        // Ajouter à loadingElements pour tracking
    }
    
    /**
     * Masque tous les indicateurs de chargement
     */
    function hideLoading() {
        // TODO: Masquer tous les éléments de chargement
        // Vider loadingElements
    }
    
    /**
     * Affiche une boîte de confirmation
     * @param {string} message - Message de confirmation
     * @param {Object} options - Options
     * @returns {Promise<boolean>} - Résultat de la confirmation
     */
    function confirm(message, options = {}) {
        // TODO: Créer une boîte de confirmation
        // Retourner une Promise qui résout avec true/false
        // Gérer les boutons Confirmer/Annuler
    }
    
    /**
     * Affiche une erreur de champ de formulaire
     * @param {string} fieldId - ID du champ
     * @param {string} message - Message d'erreur
     */
    function showFieldError(fieldId, message) {
        // TODO: Afficher l'erreur près du champ
        // Ajouter les classes CSS d'erreur
        // Mettre à jour l'élément d'erreur associé
    }
    
    /**
     * Masque l'erreur d'un champ
     * @param {string} fieldId - ID du champ
     */
    function hideFieldError(fieldId) {
        // TODO: Masquer l'erreur du champ
        // Supprimer les classes CSS d'erreur
    }
    
    /**
     * Vide tous les messages
     */
    function clear() {
        // TODO: Masquer tous les messages actifs
    }
    
    // Interface publique
    return {
        initialize,
        show,
        hide,
        showLoading,
        hideLoading,
        confirm,
        showFieldError,
        hideFieldError,
        clear
    };
})();
```

### Étape 2.2 : Gestionnaire d'état de l'interface

Créez `js/core/ui-state-manager.js` :
```javascript
/**
 * Gestionnaire d'état de l'interface utilisateur
 * Gère les transitions d'état et les indicateurs visuels
 */

export const UIStateManager = (function() {
    let currentState = 'idle';
    let stateHistory = [];
    let stateHandlers = new Map();
    
    // États possibles
    const STATES = {
        IDLE: 'idle',
        LOADING: 'loading',
        PROCESSING: 'processing',
        SUCCESS: 'success',
        ERROR: 'error',
        CRITICAL_ERROR: 'critical-error',
        DISABLED: 'disabled'
    };
    
    /**
     * Change l'état de l'interface
     * @param {string} newState - Nouvel état
     * @param {Object} data - Données associées à l'état
     */
    function setState(newState, data = {}) {
        // TODO: Implémenter le changement d'état
        // 1. Sauvegarder l'état précédent dans l'historique
        // 2. Mettre à jour currentState
        // 3. Logger le changement
        // 4. Appeler le gestionnaire d'état si défini
        // 5. Mettre à jour les classes CSS globales
        // 6. Notifier les listeners
    }
    
    /**
     * Met à jour les classes CSS globales selon l'état
     * @param {string} newState - Nouvel état
     * @param {string} previousState - État précédent
     */
    function updateGlobalClasses(newState, previousState) {
        // TODO: Mettre à jour les classes sur document.body
        // Supprimer l'ancienne classe state-*
        // Ajouter la nouvelle classe state-*
    }
    
    /**
     * Enregistre un gestionnaire pour un état
     * @param {string} state - État
     * @param {Function} handler - Gestionnaire
     */
    function registerStateHandler(state, handler) {
        // TODO: Enregistrer le gestionnaire
    }
    
    /**
     * Exécute une opération avec gestion d'état automatique
     * @param {Function} operation - Opération à exécuter (peut être async)
     * @param {Object} options - Options
     * @returns {Promise} - Résultat de l'opération
     */
    async function executeWithState(operation, options = {}) {
        // TODO: Implémenter l'exécution avec gestion d'état
        // 1. Passer en état LOADING
        // 2. Afficher le message de chargement
        // 3. Exécuter l'opération
        // 4. Gérer le succès (état SUCCESS + message)
        // 5. Gérer les erreurs (état ERROR + message)
        // 6. Retourner à IDLE après un délai
    }
    
    /**
     * Active/désactive des éléments selon l'état
     * @param {string} selector - Sélecteur des éléments
     * @param {boolean} enabled - Activer ou désactiver
     */
    function setElementsEnabled(selector, enabled) {
        // TODO: Activer/désactiver les éléments
        // Gérer disabled, readonly, classes CSS
    }
    
    /**
     * Affiche/masque des écrans
     * @param {string} screenId - ID de l'écran à afficher
     */
    function showScreen(screenId) {
        // TODO: Gérer l'affichage des écrans
        // Masquer tous les écrans (.screen)
        // Afficher l'écran demandé
        // Mettre à jour l'état si nécessaire
    }
    
    // Interface publique
    return {
        STATES,
        setState,
        getState: () => currentState,
        getStateHistory: () => [...stateHistory],
        registerStateHandler,
        executeWithState,
        setElementsEnabled,
        showScreen
    };
})();
```

## 🏋️ Exercice 3 : Validation et récupération d'erreurs

### Étape 3.1 : Système de validation

Créez `js/utils/validators.js` :
```javascript
/**
 * Système de validation avec gestion d'erreurs
 */

import { ValidationError } from '../core/error-manager.js';

/**
 * Valide un nom d'utilisateur
 * @param {string} username - Nom à valider
 * @returns {Object} - Résultat de validation
 */
export function validateUsername(username) {
    // TODO: Implémenter la validation
    // - Requis
    // - Longueur minimale 3 caractères
    // - Caractères autorisés : lettres, chiffres, _
    // - Pas d'espaces
    // Retourner { isValid: boolean, error: string|null }
}

/**
 * Valide une adresse email
 * @param {string} email - Email à valider
 * @returns {Object} - Résultat de validation
 */
export function validateEmail(email) {
    // TODO: Implémenter la validation email
    // - Requis
    // - Format email valide
    // - Longueur raisonnable
}

/**
 * Valide un formulaire complet
 * @param {HTMLFormElement} form - Formulaire à valider
 * @returns {Object} - Résultat de validation avec détails par champ
 */
export function validateForm(form) {
    // TODO: Valider tous les champs du formulaire
    // Retourner {
    //   isValid: boolean,
    //   errors: { fieldName: errorMessage, ... },
    //   validFields: [fieldNames...]
    // }
}

/**
 * Valide des données de question de quiz
 * @param {Object} questionData - Données à valider
 * @returns {Object} - Résultat de validation
 */
export function validateQuestionData(questionData) {
    // TODO: Valider la structure d'une question
    // - question (string, non vide)
    // - answers (array, min 2 éléments)
    // - correctAnswer.id (number, dans les limites)
}

/**
 * Crée un validateur en temps réel pour un champ
 * @param {HTMLInputElement} input - Champ à valider
 * @param {Function} validator - Fonction de validation
 * @param {number} debounceMs - Délai de debounce
 * @returns {Function} - Fonction de nettoyage
 */
export function createRealTimeValidator(input, validator, debounceMs = 300) {
    // TODO: Créer un validateur en temps réel
    // - Écouter les événements input et blur
    // - Debouncer la validation
    // - Afficher/masquer les erreurs
    // - Retourner une fonction pour nettoyer les listeners
}
```

### Étape 3.2 : Service avec gestion d'erreurs

Créez `js/services/quiz-service.js` :
```javascript
/**
 * Service de quiz avec gestion d'erreurs robuste
 */

import { NetworkError, ValidationError } from '../core/error-manager.js';

export const QuizService = (function() {
    
    /**
     * Simule le chargement de questions depuis un serveur
     * @param {Object} options - Options de chargement
     * @returns {Promise<Array>} - Questions chargées
     */
    async function loadQuestions(options = {}) {
        // TODO: Implémenter le chargement avec gestion d'erreurs
        // 1. Simuler un délai réseau
        // 2. Simuler parfois des erreurs réseau
        // 3. Valider les données reçues
        // 4. Retourner les questions ou lever une erreur appropriée
    }
    
    /**
     * Simule la sauvegarde d'une réponse
     * @param {Object} answerData - Données de la réponse
     * @returns {Promise<Object>} - Résultat de la sauvegarde
     */
    async function saveAnswer(answerData) {
        // TODO: Implémenter la sauvegarde avec validation
        // 1. Valider les données d'entrée
        // 2. Simuler l'envoi au serveur
        // 3. Gérer les erreurs possibles
        // 4. Retourner le résultat
    }
    
    /**
     * Simule la soumission du quiz complet
     * @param {Object} quizData - Données du quiz
     * @returns {Promise<Object>} - Résultats du quiz
     */
    async function submitQuiz(quizData) {
        // TODO: Implémenter la soumission
        // Avec retry automatique en cas d'erreur réseau
    }
    
    /**
     * Retry automatique pour les opérations réseau
     * @param {Function} operation - Opération à réessayer
     * @param {number} maxRetries - Nombre max de tentatives
     * @param {number} delay - Délai entre tentatives
     * @returns {Promise} - Résultat de l'opération
     */
    async function withRetry(operation, maxRetries = 3, delay = 1000) {
        // TODO: Implémenter le retry avec backoff exponentiel
        // Réessayer seulement pour les erreurs réseau
    }
    
    /**
     * Simule différents types d'erreurs pour les tests
     * @param {string} errorType - Type d'erreur à simuler
     */
    function simulateError(errorType) {
        // TODO: Créer différents types d'erreurs pour les tests
        // - 'network' : NetworkError
        // - 'validation' : ValidationError  
        // - 'critical' : CriticalError
        // - 'timeout' : Erreur de timeout
    }
    
    return {
        loadQuestions,
        saveAnswer,
        submitQuiz,
        simulateError
    };
})();
```

## 🏋️ Exercice 4 : Intégration et tests d'erreurs

### Étape 4.1 : Point d'entrée avec gestion d'erreurs globale

Créez `js/main.js` :
```javascript
/**
 * Point d'entrée principal avec gestion d'erreurs globale
 */

import { ErrorManager, CriticalError } from './core/error-manager.js';
import { FeedbackManager } from './core/feedback-manager.js';
import { UIStateManager } from './core/ui-state-manager.js';
import { QuizService } from './services/quiz-service.js';
import { validateForm, createRealTimeValidator } from './utils/validators.js';

/**
 * Contrôleur principal de l'application
 */
const AppController = (function() {
    let isInitialized = false;
    let debugMode = false;
    
    /**
     * Initialise l'application avec gestion d'erreurs
     */
    async function initialize() {
        if (isInitialized) return;
        
        try {
            console.log('🚀 Initialisation de l\'application...');
            
            // TODO: Initialiser l'application
            // 1. Configurer la gestion d'erreurs globale
            // 2. Initialiser les gestionnaires
            // 3. Configurer les événements
            // 4. Charger les données initiales
            // 5. Afficher l'interface principale
            
            isInitialized = true;
            console.log('✅ Application initialisée avec succès');
            
        } catch (error) {
            console.error('❌ Erreur critique lors de l\'initialisation:', error);
            showCriticalErrorScreen(error);
        }
    }
    
    /**
     * Configure la gestion d'erreurs globale
     */
    function setupGlobalErrorHandling() {
        // TODO: Configurer les gestionnaires d'erreurs globaux
        // 1. window.addEventListener('error', ...)
        // 2. window.addEventListener('unhandledrejection', ...)
        // 3. Enregistrer les gestionnaires spécifiques dans ErrorManager
        // 4. Configurer les listeners pour le debug
    }
    
    /**
     * Configure tous les événements de l'application
     */
    function setupEventListeners() {
        // TODO: Configurer tous les événements
        // Boutons de test d'erreurs
        // Formulaire de test
        // Boutons de debug
        // Utiliser ErrorManager.createSafeWrapper pour sécuriser
    }
    
    /**
     * Configure la validation en temps réel
     */
    function setupRealTimeValidation() {
        // TODO: Configurer la validation temps réel
        // Pour les champs username et email
        // Utiliser createRealTimeValidator
    }
    
    /**
     * Gère les tests d'erreurs
     * @param {string} errorType - Type d'erreur à tester
     */
    async function handleErrorTest(errorType) {
        // TODO: Implémenter les tests d'erreurs
        // Utiliser QuizService.simulateError
        // Utiliser UIStateManager.executeWithState
    }
    
    /**
     * Gère la soumission du formulaire de test
     * @param {Event} event - Événement de soumission
     */
    async function handleFormSubmit(event) {
        // TODO: Gérer la soumission avec validation
        // 1. Empêcher la soumission par défaut
        // 2. Valider le formulaire
        // 3. Afficher les erreurs ou traiter les données
        // 4. Simuler une opération async
    }
    
    /**
     * Affiche l'écran d'erreur critique
     * @param {Error} error - Erreur critique
     */
    function showCriticalErrorScreen(error) {
        // TODO: Afficher l'écran d'erreur critique
        // Masquer les autres écrans
        // Afficher les détails de l'erreur
        // Proposer des actions de récupération
    }
    
    /**
     * Active/désactive le mode debug
     */
    function toggleDebugMode() {
        // TODO: Basculer le mode debug
        // Afficher/masquer le panneau debug
        // Mettre à jour l'affichage des logs
    }
    
    /**
     * Met à jour l'affichage du debug
     */
    function updateDebugDisplay() {
        // TODO: Mettre à jour le panneau debug
        // Afficher le journal des erreurs
        // Afficher l'état de l'application
    }
    
    // Interface publique
    return {
        initialize,
        toggleDebugMode
    };
})();

// Initialisation
if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', AppController.initialize);
} else {
    AppController.initialize();
}

// Exposer pour le debug
window.AppController = AppController;
window.ErrorManager = ErrorManager;
window.FeedbackManager = FeedbackManager;
window.UIStateManager = UIStateManager;
```

## 🧪 Tests à effectuer

### Test 1 : Classification des erreurs
```javascript
// TODO: Tester que les erreurs sont correctement classifiées
// - CriticalError → CRITICAL
// - NetworkError → HIGH  
// - ValidationError → MEDIUM
// - Error générique → LOW
```

### Test 2 : Feedback utilisateur
```javascript
// TODO: Tester tous les types de feedback
// - Messages de succès, erreur, warning, info
// - Indicateurs de chargement
// - Confirmations
// - Erreurs de champs
```

### Test 3 : Récupération d'erreurs
```javascript
// TODO: Tester la récupération
// - Retry automatique pour erreurs réseau
// - Fallback pour erreurs critiques
// - Validation et correction des données
```

### Test 4 : États de l'interface
```javascript
// TODO: Tester les transitions d'état
// - idle → loading → success/error → idle
// - Désactivation des contrôles pendant loading
// - Classes CSS correctes
```

## 🎯 Critères de réussite

- [ ] Toutes les erreurs sont capturées et gérées
- [ ] Les messages utilisateur sont clairs et actionnables
- [ ] L'interface reste utilisable même en cas d'erreur
- [ ] Les erreurs critiques ont un écran de fallback
- [ ] Le système de retry fonctionne pour les erreurs réseau
- [ ] La validation en temps réel guide l'utilisateur
- [ ] Le mode debug permet de suivre les erreurs

## 💡 Conseils

1. **Testez tous les cas d'erreur** - Ne laissez rien au hasard
2. **Messages utilisateur clairs** - Évitez le jargon technique
3. **Récupération gracieuse** - L'app doit rester utilisable
4. **Logging détaillé** - Pour faciliter le debug
5. **Classification appropriée** - Toutes les erreurs ne sont pas égales

---

**Prochaine étape :** Consultez les solutions pour comparer vos implémentations !
