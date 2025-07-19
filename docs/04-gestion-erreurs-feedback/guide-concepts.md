# Guide des Concepts - Gestion d'Erreurs et Feedback Utilisateur

## 🎯 Vue d'ensemble
Ce guide explique comment créer une expérience utilisateur robuste avec une gestion d'erreurs appropriée et un système de feedback efficace, en utilisant une approche modulaire.

## 📚 Concepts fondamentaux

### 1. Types d'erreurs en JavaScript

#### Erreurs de syntaxe
```javascript
// Détectées à l'analyse du code
function malformed( {  // SyntaxError
    console.log('erreur');
}
```

#### Erreurs d'exécution
```javascript
// Détectées pendant l'exécution
const element = null;
element.addEventListener('click', handler); // TypeError
```

#### Erreurs logiques
```javascript
// Code qui s'exécute mais produit un résultat incorrect
function calculateScore(correct, total) {
    return correct / total; // Oubli de multiplier par 100 pour le pourcentage
}
```

#### Erreurs asynchrones
```javascript
// Erreurs dans les opérations async
async function loadData() {
    const response = await fetch('/api/data'); // Peut échouer
    const data = await response.json(); // Peut échouer si response n'est pas JSON
    return data;
}
```

### 2. Stratégies de gestion d'erreurs

#### Try/Catch pour les erreurs synchrones
```javascript
function safeOperation() {
    try {
        const result = riskyOperation();
        return { success: true, data: result };
    } catch (error) {
        console.error('Erreur dans safeOperation:', error);
        return { success: false, error: error.message };
    }
}
```

#### Try/Catch avec async/await
```javascript
async function safeAsyncOperation() {
    try {
        const data = await fetchData();
        const processed = processData(data);
        return { success: true, data: processed };
    } catch (error) {
        // Classifier l'erreur
        if (error.name === 'NetworkError') {
            return { success: false, error: 'Problème de connexion' };
        } else if (error.name === 'ValidationError') {
            return { success: false, error: error.message };
        } else {
            return { success: false, error: 'Erreur inattendue' };
        }
    }
}
```

#### Gestion des Promises avec .catch()
```javascript
function handlePromiseErrors() {
    return fetchData()
        .then(data => processData(data))
        .then(result => ({ success: true, data: result }))
        .catch(error => {
            console.error('Erreur Promise:', error);
            return { success: false, error: error.message };
        });
}
```

### 3. Classification et hiérarchisation des erreurs

#### Erreurs critiques (bloquent l'application)
```javascript
const ErrorSeverity = {
    CRITICAL: 'critical',    // Application inutilisable
    HIGH: 'high',           // Fonctionnalité principale cassée
    MEDIUM: 'medium',       // Fonctionnalité secondaire affectée
    LOW: 'low'              // Problème mineur, workaround possible
};

function classifyError(error) {
    // Erreurs critiques
    if (error.message.includes('DOM not ready') || 
        error.message.includes('Required elements missing')) {
        return ErrorSeverity.CRITICAL;
    }
    
    // Erreurs importantes
    if (error.name === 'NetworkError' || 
        error.message.includes('Failed to load')) {
        return ErrorSeverity.HIGH;
    }
    
    // Erreurs moyennes
    if (error.name === 'ValidationError') {
        return ErrorSeverity.MEDIUM;
    }
    
    // Erreurs mineures
    return ErrorSeverity.LOW;
}
```

### 4. Système de feedback utilisateur

#### Messages selon le contexte
```javascript
const FeedbackMessages = {
    // Messages d'erreur utilisateur-friendly
    NETWORK_ERROR: 'Problème de connexion. Vérifiez votre connexion internet.',
    VALIDATION_ERROR: 'Veuillez corriger les informations saisies.',
    SERVER_ERROR: 'Problème temporaire du serveur. Réessayez dans quelques instants.',
    UNKNOWN_ERROR: 'Une erreur inattendue s\'est produite.',
    
    // Messages de succès
    QUIZ_STARTED: 'Quiz démarré avec succès !',
    ANSWER_SAVED: 'Votre réponse a été enregistrée.',
    QUIZ_COMPLETED: 'Félicitations ! Quiz terminé.',
    
    // Messages d'information
    LOADING: 'Chargement en cours...',
    PROCESSING: 'Traitement de votre réponse...',
    SAVING: 'Sauvegarde en cours...'
};

function getUserFriendlyMessage(error) {
    if (error.message.includes('fetch')) {
        return FeedbackMessages.NETWORK_ERROR;
    } else if (error.name === 'ValidationError') {
        return error.message; // Message de validation spécifique
    } else if (error.message.includes('500')) {
        return FeedbackMessages.SERVER_ERROR;
    } else {
        return FeedbackMessages.UNKNOWN_ERROR;
    }
}
```

### 5. Module de gestion d'erreurs centralisé

```javascript
export const ErrorManager = (function() {
    let errorLog = [];
    let errorHandlers = new Map();
    
    /**
     * Enregistre un gestionnaire pour un type d'erreur
     * @param {string} errorType - Type d'erreur
     * @param {Function} handler - Gestionnaire
     */
    function registerHandler(errorType, handler) {
        errorHandlers.set(errorType, handler);
    }
    
    /**
     * Gère une erreur de manière centralisée
     * @param {Error} error - Erreur à gérer
     * @param {string} context - Contexte de l'erreur
     * @param {Object} metadata - Métadonnées additionnelles
     */
    function handleError(error, context = '', metadata = {}) {
        // Logger l'erreur
        const errorEntry = {
            error: error,
            context: context,
            metadata: metadata,
            timestamp: new Date().toISOString(),
            severity: classifyError(error)
        };
        
        errorLog.push(errorEntry);
        console.error(`[${errorEntry.severity.toUpperCase()}] ${context}:`, error);
        
        // Appeler le gestionnaire spécifique si disponible
        const handler = errorHandlers.get(error.name) || errorHandlers.get('default');
        if (handler) {
            try {
                handler(errorEntry);
            } catch (handlerError) {
                console.error('Erreur dans le gestionnaire d\'erreur:', handlerError);
            }
        }
        
        // Afficher le feedback utilisateur
        showUserFeedback(error, errorEntry.severity);
        
        // Reporter l'erreur si en production
        if (isProduction()) {
            reportError(errorEntry);
        }
    }
    
    /**
     * Affiche un feedback à l'utilisateur
     * @param {Error} error - Erreur
     * @param {string} severity - Sévérité
     */
    function showUserFeedback(error, severity) {
        const message = getUserFriendlyMessage(error);
        const type = severity === ErrorSeverity.CRITICAL ? 'error' : 
                    severity === ErrorSeverity.HIGH ? 'error' : 'warning';
        
        FeedbackManager.show(message, type);
    }
    
    /**
     * Crée un wrapper sécurisé pour une fonction
     * @param {Function} func - Fonction à sécuriser
     * @param {string} context - Contexte pour le debug
     * @returns {Function} - Fonction sécurisée
     */
    function createSafeWrapper(func, context) {
        return function(...args) {
            try {
                const result = func.apply(this, args);
                
                // Si c'est une Promise, gérer les erreurs async
                if (result && typeof result.catch === 'function') {
                    return result.catch(error => {
                        handleError(error, context, { args });
                        throw error; // Re-throw pour permettre la gestion locale
                    });
                }
                
                return result;
            } catch (error) {
                handleError(error, context, { args });
                throw error; // Re-throw pour permettre la gestion locale
            }
        };
    }
    
    return {
        handleError,
        registerHandler,
        createSafeWrapper,
        getErrorLog: () => [...errorLog],
        clearErrorLog: () => { errorLog = []; }
    };
})();
```

### 6. Système de feedback visuel

```javascript
export const FeedbackManager = (function() {
    let container = null;
    let activeMessages = new Set();
    
    /**
     * Initialise le gestionnaire de feedback
     */
    function initialize() {
        if (!container) {
            container = document.createElement('div');
            container.id = 'feedback-container';
            container.className = 'feedback-container';
            document.body.appendChild(container);
        }
    }
    
    /**
     * Affiche un message de feedback
     * @param {string} message - Message à afficher
     * @param {string} type - Type: 'success', 'error', 'warning', 'info'
     * @param {number} duration - Durée d'affichage (0 = permanent)
     * @param {Object} options - Options additionnelles
     */
    function show(message, type = 'info', duration = 5000, options = {}) {
        initialize();
        
        const messageElement = createMessageElement(message, type, options);
        container.appendChild(messageElement);
        activeMessages.add(messageElement);
        
        // Animation d'entrée
        requestAnimationFrame(() => {
            messageElement.classList.add('show');
        });
        
        // Suppression automatique
        if (duration > 0) {
            setTimeout(() => {
                hide(messageElement);
            }, duration);
        }
        
        return messageElement;
    }
    
    /**
     * Crée un élément de message
     * @param {string} message - Message
     * @param {string} type - Type
     * @param {Object} options - Options
     * @returns {HTMLElement} - Élément créé
     */
    function createMessageElement(message, type, options) {
        const element = document.createElement('div');
        element.className = `feedback-message feedback-${type}`;
        
        // Contenu du message
        const content = document.createElement('div');
        content.className = 'feedback-content';
        content.textContent = message;
        element.appendChild(content);
        
        // Bouton de fermeture si demandé
        if (options.closable !== false) {
            const closeButton = document.createElement('button');
            closeButton.className = 'feedback-close';
            closeButton.innerHTML = '×';
            closeButton.onclick = () => hide(element);
            element.appendChild(closeButton);
        }
        
        // Actions personnalisées
        if (options.actions) {
            const actionsContainer = document.createElement('div');
            actionsContainer.className = 'feedback-actions';
            
            options.actions.forEach(action => {
                const button = document.createElement('button');
                button.textContent = action.text;
                button.className = `feedback-action ${action.type || 'secondary'}`;
                button.onclick = () => {
                    if (action.handler) action.handler();
                    if (action.closeOnClick !== false) hide(element);
                };
                actionsContainer.appendChild(button);
            });
            
            element.appendChild(actionsContainer);
        }
        
        return element;
    }
    
    /**
     * Masque un message
     * @param {HTMLElement} messageElement - Élément à masquer
     */
    function hide(messageElement) {
        if (!activeMessages.has(messageElement)) return;
        
        messageElement.classList.add('hide');
        activeMessages.delete(messageElement);
        
        setTimeout(() => {
            if (messageElement.parentNode) {
                messageElement.parentNode.removeChild(messageElement);
            }
        }, 300);
    }
    
    /**
     * Affiche un indicateur de chargement
     * @param {string} message - Message de chargement
     * @returns {HTMLElement} - Élément de chargement
     */
    function showLoading(message = 'Chargement...') {
        return show(message, 'loading', 0, {
            closable: false,
            icon: 'spinner'
        });
    }
    
    /**
     * Masque tous les messages de chargement
     */
    function hideLoading() {
        const loadingMessages = container.querySelectorAll('.feedback-loading');
        loadingMessages.forEach(hide);
    }
    
    /**
     * Affiche une confirmation
     * @param {string} message - Message de confirmation
     * @param {Object} options - Options
     * @returns {Promise<boolean>} - Résultat de la confirmation
     */
    function confirm(message, options = {}) {
        return new Promise((resolve) => {
            show(message, 'confirm', 0, {
                closable: false,
                actions: [
                    {
                        text: options.cancelText || 'Annuler',
                        type: 'secondary',
                        handler: () => resolve(false)
                    },
                    {
                        text: options.confirmText || 'Confirmer',
                        type: 'primary',
                        handler: () => resolve(true)
                    }
                ]
            });
        });
    }
    
    return {
        show,
        hide,
        showLoading,
        hideLoading,
        confirm,
        clear: () => {
            activeMessages.forEach(hide);
        }
    };
})();
```

### 7. États de l'interface et transitions

```javascript
export const UIStateManager = (function() {
    let currentState = 'idle';
    let stateHistory = [];
    let stateHandlers = new Map();
    
    const STATES = {
        IDLE: 'idle',
        LOADING: 'loading',
        PROCESSING: 'processing',
        SUCCESS: 'success',
        ERROR: 'error',
        DISABLED: 'disabled'
    };
    
    /**
     * Change l'état de l'interface
     * @param {string} newState - Nouvel état
     * @param {Object} data - Données associées
     */
    function setState(newState, data = {}) {
        const previousState = currentState;
        stateHistory.push({ state: previousState, timestamp: Date.now() });
        currentState = newState;
        
        console.log(`État UI: ${previousState} → ${newState}`);
        
        // Appeler les gestionnaires d'état
        const handler = stateHandlers.get(newState);
        if (handler) {
            try {
                handler(data, previousState);
            } catch (error) {
                ErrorManager.handleError(error, 'state-handler');
            }
        }
        
        // Mettre à jour les classes CSS globales
        updateGlobalClasses(newState, previousState);
    }
    
    /**
     * Met à jour les classes CSS globales
     * @param {string} newState - Nouvel état
     * @param {string} previousState - État précédent
     */
    function updateGlobalClasses(newState, previousState) {
        const body = document.body;
        
        // Supprimer l'ancienne classe d'état
        if (previousState) {
            body.classList.remove(`state-${previousState}`);
        }
        
        // Ajouter la nouvelle classe d'état
        body.classList.add(`state-${newState}`);
    }
    
    /**
     * Enregistre un gestionnaire pour un état
     * @param {string} state - État
     * @param {Function} handler - Gestionnaire
     */
    function registerStateHandler(state, handler) {
        stateHandlers.set(state, handler);
    }
    
    /**
     * Exécute une opération avec gestion d'état automatique
     * @param {Function} operation - Opération à exécuter
     * @param {Object} options - Options
     */
    async function executeWithState(operation, options = {}) {
        const {
            loadingMessage = 'Traitement en cours...',
            successMessage = 'Opération réussie',
            errorMessage = 'Erreur lors de l\'opération'
        } = options;
        
        try {
            setState(STATES.LOADING);
            if (loadingMessage) {
                FeedbackManager.showLoading(loadingMessage);
            }
            
            const result = await operation();
            
            setState(STATES.SUCCESS);
            FeedbackManager.hideLoading();
            
            if (successMessage) {
                FeedbackManager.show(successMessage, 'success');
            }
            
            return result;
            
        } catch (error) {
            setState(STATES.ERROR);
            FeedbackManager.hideLoading();
            
            ErrorManager.handleError(error, 'executeWithState');
            
            if (errorMessage) {
                FeedbackManager.show(errorMessage, 'error');
            }
            
            throw error;
        } finally {
            // Retourner à l'état idle après un délai
            setTimeout(() => {
                if (currentState !== STATES.LOADING) {
                    setState(STATES.IDLE);
                }
            }, 2000);
        }
    }
    
    return {
        STATES,
        setState,
        getState: () => currentState,
        getStateHistory: () => [...stateHistory],
        registerStateHandler,
        executeWithState
    };
})();
```

## 🔧 Outils de débogage et monitoring

### Logging structuré
```javascript
const Logger = {
    levels: { ERROR: 0, WARN: 1, INFO: 2, DEBUG: 3 },
    currentLevel: 2, // INFO par défaut
    
    log(level, message, data = {}) {
        if (this.levels[level] <= this.currentLevel) {
            const timestamp = new Date().toISOString();
            console[level.toLowerCase()](`[${timestamp}] ${level}: ${message}`, data);
        }
    },
    
    error: (msg, data) => Logger.log('ERROR', msg, data),
    warn: (msg, data) => Logger.log('WARN', msg, data),
    info: (msg, data) => Logger.log('INFO', msg, data),
    debug: (msg, data) => Logger.log('DEBUG', msg, data)
};
```

## ⚠️ Pièges courants à éviter

1. **Messages d'erreur techniques** exposés aux utilisateurs
2. **Pas de fallback** pour les erreurs critiques
3. **Gestion d'erreurs trop générique** qui masque les vrais problèmes
4. **Feedback utilisateur absent** ou trop tardif
5. **États d'interface incohérents** après erreurs

## 🎯 Bonnes pratiques

1. **Classifier les erreurs** selon leur impact
2. **Messages utilisateur clairs** et actionnables
3. **États visuels** pour toutes les opérations
4. **Récupération gracieuse** quand possible
5. **Logging détaillé** pour le debug
6. **Tests des cas d'erreur** systématiques

---

**Prochaine étape :** Passez aux exercices pratiques pour implémenter ces concepts !
