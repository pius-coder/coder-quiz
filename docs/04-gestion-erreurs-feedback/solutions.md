# Solutions - Gestion d'Erreurs et Feedback Utilisateur

## 🎯 Solutions complètes avec approche modulaire

Ces solutions implémentent une gestion d'erreurs robuste et un système de feedback utilisateur efficace.

## 🏋️ Solution Exercice 1 : Gestionnaire d'erreurs centralisé

```javascript
// js/core/error-manager.js

/**
 * Gestionnaire d'erreurs centralisé
 */

// Types de sévérité des erreurs
export const ErrorSeverity = {
    CRITICAL: 'critical',
    HIGH: 'high',
    MEDIUM: 'medium',
    LOW: 'low'
};

// Classes d'erreurs personnalisées
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
    let errorLog = [];
    let errorHandlers = new Map();
    let listeners = [];
    let maxLogSize = 100;
    
    /**
     * Classifie une erreur selon sa sévérité
     */
    function classifyError(error) {
        if (error instanceof CriticalError || 
            error.message.includes('DOM not ready') ||
            error.message.includes('Required elements missing')) {
            return ErrorSeverity.CRITICAL;
        }
        
        if (error instanceof NetworkError ||
            error.message.includes('fetch') ||
            error.message.includes('network') ||
            error.message.includes('Failed to load')) {
            return ErrorSeverity.HIGH;
        }
        
        if (error instanceof ValidationError ||
            error.name === 'ValidationError') {
            return ErrorSeverity.MEDIUM;
        }
        
        return ErrorSeverity.LOW;
    }
    
    /**
     * Gère une erreur de manière centralisée
     */
    function handleError(error, context = '', metadata = {}) {
        const severity = classifyError(error);
        
        // Créer l'entrée de log
        const errorEntry = {
            id: generateErrorId(),
            error: {
                name: error.name,
                message: error.message,
                stack: error.stack
            },
            context: context,
            metadata: metadata,
            severity: severity,
            timestamp: new Date().toISOString(),
            userAgent: navigator.userAgent,
            url: window.location.href
        };
        
        // Ajouter au log (avec limite de taille)
        errorLog.push(errorEntry);
        if (errorLog.length > maxLogSize) {
            errorLog.shift();
        }
        
        // Logger dans la console
        const logMethod = severity === ErrorSeverity.CRITICAL ? 'error' : 
                         severity === ErrorSeverity.HIGH ? 'error' : 
                         severity === ErrorSeverity.MEDIUM ? 'warn' : 'log';
        
        console[logMethod](`[${severity.toUpperCase()}] ${context}:`, error, metadata);
        
        // Appeler le gestionnaire spécifique
        const handler = errorHandlers.get(error.name) || 
                       errorHandlers.get(severity) || 
                       errorHandlers.get('default');
        
        if (handler) {
            try {
                handler(errorEntry);
            } catch (handlerError) {
                console.error('Erreur dans le gestionnaire d\'erreur:', handlerError);
            }
        }
        
        // Notifier les listeners
        notifyListeners('error', errorEntry);
        
        // Afficher le feedback utilisateur
        showUserFeedback(error, severity);
        
        // Reporter l'erreur si en production
        if (isProduction()) {
            reportError(errorEntry);
        }
        
        return errorEntry;
    }
    
    /**
     * Affiche un feedback à l'utilisateur
     */
    function showUserFeedback(error, severity) {
        // Import dynamique pour éviter les dépendances circulaires
        import('./feedback-manager.js').then(({ FeedbackManager }) => {
            const message = getUserFriendlyMessage(error);
            const type = severity === ErrorSeverity.CRITICAL ? 'error' : 
                        severity === ErrorSeverity.HIGH ? 'error' : 'warning';
            
            const duration = severity === ErrorSeverity.CRITICAL ? 0 : 5000;
            
            FeedbackManager.show(message, type, duration, {
                closable: severity !== ErrorSeverity.CRITICAL
            });
        });
    }
    
    /**
     * Convertit une erreur technique en message utilisateur
     */
    function getUserFriendlyMessage(error) {
        if (error instanceof NetworkError || error.message.includes('fetch')) {
            return 'Problème de connexion. Vérifiez votre connexion internet et réessayez.';
        }
        
        if (error instanceof ValidationError) {
            return error.message; // Les erreurs de validation sont déjà user-friendly
        }
        
        if (error instanceof CriticalError) {
            return 'Une erreur critique s\'est produite. L\'application doit être rechargée.';
        }
        
        if (error.message.includes('500')) {
            return 'Problème temporaire du serveur. Réessayez dans quelques instants.';
        }
        
        if (error.message.includes('404')) {
            return 'Ressource non trouvée. Veuillez contacter le support.';
        }
        
        return 'Une erreur inattendue s\'est produite. Si le problème persiste, contactez le support.';
    }
    
    /**
     * Enregistre un gestionnaire pour un type d'erreur
     */
    function registerHandler(errorType, handler) {
        errorHandlers.set(errorType, handler);
        console.log(`Gestionnaire enregistré pour: ${errorType}`);
    }
    
    /**
     * Crée un wrapper sécurisé pour une fonction
     */
    function createSafeWrapper(func, context) {
        return function(...args) {
            try {
                const result = func.apply(this, args);
                
                // Si c'est une Promise, gérer les erreurs async
                if (result && typeof result.catch === 'function') {
                    return result.catch(error => {
                        handleError(error, context, { args, function: func.name });
                        throw error; // Re-throw pour permettre la gestion locale
                    });
                }
                
                return result;
            } catch (error) {
                handleError(error, context, { args, function: func.name });
                throw error; // Re-throw pour permettre la gestion locale
            }
        };
    }
    
    /**
     * Ajoute un listener pour les erreurs
     */
    function addListener(callback) {
        listeners.push(callback);
        return () => {
            listeners = listeners.filter(l => l !== callback);
        };
    }
    
    /**
     * Notifie tous les listeners
     */
    function notifyListeners(event, data) {
        listeners.forEach(callback => {
            try {
                callback(event, data);
            } catch (error) {
                console.error('Erreur dans listener ErrorManager:', error);
            }
        });
    }
    
    /**
     * Récupère le journal des erreurs
     */
    function getErrorLog(severity = null) {
        if (severity) {
            return errorLog.filter(entry => entry.severity === severity);
        }
        return [...errorLog];
    }
    
    /**
     * Vide le journal des erreurs
     */
    function clearErrorLog() {
        const clearedCount = errorLog.length;
        errorLog = [];
        notifyListeners('log-cleared', { clearedCount });
        console.log(`Journal d'erreurs vidé (${clearedCount} entrées supprimées)`);
    }
    
    /**
     * Génère un ID unique pour une erreur
     */
    function generateErrorId() {
        return Date.now().toString(36) + Math.random().toString(36).substr(2);
    }
    
    /**
     * Vérifie si on est en production
     */
    function isProduction() {
        return window.location.hostname !== 'localhost' && 
               window.location.hostname !== '127.0.0.1';
    }
    
    /**
     * Reporte une erreur au serveur (en production)
     */
    async function reportError(errorEntry) {
        try {
            await fetch('/api/errors', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(errorEntry)
            });
        } catch (reportingError) {
            console.error('Impossible de reporter l\'erreur:', reportingError);
        }
    }
    
    // Configuration des gestionnaires par défaut
    registerHandler('default', (errorEntry) => {
        console.log('Gestionnaire par défaut appelé pour:', errorEntry);
    });
    
    registerHandler(ErrorSeverity.CRITICAL, (errorEntry) => {
        console.error('ERREUR CRITIQUE détectée:', errorEntry);
        // Déclencher l'écran d'erreur critique
        setTimeout(() => {
            import('./ui-state-manager.js').then(({ UIStateManager }) => {
                UIStateManager.setState(UIStateManager.STATES.CRITICAL_ERROR, errorEntry);
            });
        }, 100);
    });
    
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

## 🏋️ Solution Exercice 2 : Système de feedback

```javascript
// js/core/feedback-manager.js

/**
 * Gestionnaire de feedback utilisateur
 */

export const FeedbackManager = (function() {
    let container = null;
    let activeMessages = new Set();
    let loadingElements = new Set();
    let messageCounter = 0;
    
    /**
     * Initialise le gestionnaire de feedback
     */
    function initialize() {
        if (!container) {
            container = document.createElement('div');
            container.id = 'feedback-container';
            container.className = 'feedback-container';
            
            // Styles inline pour garantir l'affichage
            container.style.cssText = `
                position: fixed;
                top: 20px;
                right: 20px;
                z-index: 10000;
                max-width: 400px;
                pointer-events: none;
            `;
            
            document.body.appendChild(container);
        }
    }
    
    /**
     * Affiche un message de feedback
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
     */
    function createMessageElement(message, type, options) {
        const messageId = `feedback-${++messageCounter}`;
        const element = document.createElement('div');
        element.id = messageId;
        element.className = `feedback-message feedback-${type}`;
        
        // Styles inline pour garantir l'affichage
        element.style.cssText = `
            margin-bottom: 10px;
            padding: 12px 16px;
            border-radius: 6px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.15);
            transform: translateX(100%);
            transition: transform 0.3s ease-out, opacity 0.3s ease-out;
            opacity: 0;
            pointer-events: auto;
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            font-size: 14px;
            line-height: 1.4;
            position: relative;
        `;
        
        // Couleurs selon le type
        const colors = {
            success: { bg: '#d4edda', color: '#155724', border: '#c3e6cb' },
            error: { bg: '#f8d7da', color: '#721c24', border: '#f5c6cb' },
            warning: { bg: '#fff3cd', color: '#856404', border: '#ffeaa7' },
            info: { bg: '#d1ecf1', color: '#0c5460', border: '#bee5eb' },
            loading: { bg: '#e3f2fd', color: '#1976d2', border: '#bbdefb' }
        };
        
        const colorScheme = colors[type] || colors.info;
        element.style.backgroundColor = colorScheme.bg;
        element.style.color = colorScheme.color;
        element.style.border = `1px solid ${colorScheme.border}`;
        
        // Contenu du message
        const content = document.createElement('div');
        content.className = 'feedback-content';
        content.style.cssText = 'margin-right: 20px;';
        
        // Icône selon le type
        const icons = {
            success: '✓',
            error: '✕',
            warning: '⚠',
            info: 'ℹ',
            loading: '⟳'
        };
        
        const icon = icons[type] || icons.info;
        content.innerHTML = `<strong>${icon}</strong> ${message}`;
        
        element.appendChild(content);
        
        // Bouton de fermeture
        if (options.closable !== false) {
            const closeButton = document.createElement('button');
            closeButton.className = 'feedback-close';
            closeButton.innerHTML = '×';
            closeButton.style.cssText = `
                position: absolute;
                top: 8px;
                right: 8px;
                background: none;
                border: none;
                font-size: 18px;
                cursor: pointer;
                color: inherit;
                opacity: 0.7;
                padding: 0;
                width: 20px;
                height: 20px;
                display: flex;
                align-items: center;
                justify-content: center;
            `;
            
            closeButton.onmouseover = () => closeButton.style.opacity = '1';
            closeButton.onmouseout = () => closeButton.style.opacity = '0.7';
            closeButton.onclick = () => hide(element);
            
            element.appendChild(closeButton);
        }
        
        // Actions personnalisées
        if (options.actions && options.actions.length > 0) {
            const actionsContainer = document.createElement('div');
            actionsContainer.className = 'feedback-actions';
            actionsContainer.style.cssText = `
                margin-top: 10px;
                display: flex;
                gap: 8px;
                justify-content: flex-end;
            `;
            
            options.actions.forEach(action => {
                const button = document.createElement('button');
                button.textContent = action.text;
                button.className = `feedback-action ${action.type || 'secondary'}`;
                button.style.cssText = `
                    padding: 6px 12px;
                    border: 1px solid currentColor;
                    border-radius: 4px;
                    background: ${action.type === 'primary' ? 'currentColor' : 'transparent'};
                    color: ${action.type === 'primary' ? colorScheme.bg : 'currentColor'};
                    cursor: pointer;
                    font-size: 12px;
                    transition: all 0.2s ease;
                `;
                
                button.onclick = () => {
                    if (action.handler) action.handler();
                    if (action.closeOnClick !== false) hide(element);
                };
                
                actionsContainer.appendChild(button);
            });
            
            element.appendChild(actionsContainer);
        }
        
        // Animation de rotation pour loading
        if (type === 'loading') {
            const iconElement = content.querySelector('strong');
            iconElement.style.cssText = `
                display: inline-block;
                animation: spin 1s linear infinite;
            `;
            
            // Ajouter l'animation CSS si elle n'existe pas
            if (!document.querySelector('#feedback-animations')) {
                const style = document.createElement('style');
                style.id = 'feedback-animations';
                style.textContent = `
                    @keyframes spin {
                        from { transform: rotate(0deg); }
                        to { transform: rotate(360deg); }
                    }
                `;
                document.head.appendChild(style);
            }
        }
        
        return element;
    }
    
    /**
     * Masque un message
     */
    function hide(messageElement) {
        if (!activeMessages.has(messageElement)) return;
        
        messageElement.style.transform = 'translateX(100%)';
        messageElement.style.opacity = '0';
        activeMessages.delete(messageElement);
        
        setTimeout(() => {
            if (messageElement.parentNode) {
                messageElement.parentNode.removeChild(messageElement);
            }
        }, 300);
    }
    
    /**
     * Affiche un indicateur de chargement
     */
    function showLoading(message = 'Chargement...', options = {}) {
        const loadingElement = show(message, 'loading', 0, {
            closable: false,
            ...options
        });
        
        loadingElements.add(loadingElement);
        return loadingElement;
    }
    
    /**
     * Masque tous les indicateurs de chargement
     */
    function hideLoading() {
        loadingElements.forEach(element => {
            hide(element);
        });
        loadingElements.clear();
    }
    
    /**
     * Affiche une boîte de confirmation
     */
    function confirm(message, options = {}) {
        return new Promise((resolve) => {
            show(message, 'info', 0, {
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
    
    /**
     * Affiche une erreur de champ de formulaire
     */
    function showFieldError(fieldId, message) {
        const field = document.getElementById(fieldId);
        const errorElement = document.getElementById(`${fieldId}-error`);
        
        if (field) {
            field.classList.add('error');
            field.closest('.form-group')?.classList.add('error');
        }
        
        if (errorElement) {
            errorElement.textContent = message;
            errorElement.classList.add('show');
        }
    }
    
    /**
     * Masque l'erreur d'un champ
     */
    function hideFieldError(fieldId) {
        const field = document.getElementById(fieldId);
        const errorElement = document.getElementById(`${fieldId}-error`);
        
        if (field) {
            field.classList.remove('error');
            field.closest('.form-group')?.classList.remove('error');
        }
        
        if (errorElement) {
            errorElement.textContent = '';
            errorElement.classList.remove('show');
        }
    }
    
    /**
     * Vide tous les messages
     */
    function clear() {
        activeMessages.forEach(element => {
            hide(element);
        });
        loadingElements.clear();
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

---

**Suite des solutions dans le prochain fichier...**
