# Exercice 4 : Gestion d'Erreurs et Feedback Utilisateur

## 🎯 Objectif
Créer une expérience utilisateur robuste avec une gestion d'erreurs appropriée, des messages de feedback clairs et une interface qui reste utilisable même en cas de problème.

## 📚 Notions à maîtriser
- Try/catch et gestion d'erreurs asynchrones
- Messages de feedback utilisateur
- États de chargement et transitions
- Récupération gracieuse d'erreurs
- Accessibilité des messages d'erreur

## 🏋️ Exercices pratiques

### Exercice 4.1 : Gestion d'erreurs robuste

**Problème courant :** Code qui plante silencieusement ou avec des erreurs cryptiques

```javascript
// ❌ MAUVAISE PRATIQUE - Code fragile
function handleSubmit() {
    const answer = document.querySelector('input:checked').value; // Peut être null
    const result = processAnswer(answer); // Peut lever une exception
    showNextQuestion(); // Exécuté même si erreur
}

async function loadQuestions() {
    const response = await fetch('/api/questions'); // Peut échouer
    const data = await response.json(); // Peut échouer
    return data.questions; // data peut être undefined
}
```

**Mission :** Créez un système de gestion d'erreurs robuste :

```javascript
// ✅ BONNE PRATIQUE - À implémenter
class ErrorHandler {
    static handle(error, context = '') {
        // Classifier l'erreur
        // Logger pour le debug
        // Afficher un message utilisateur approprié
        // Votre code ici...
    }
    
    static async withErrorHandling(asyncFunction, fallback = null) {
        // Wrapper pour fonctions async
        // Votre code ici...
    }
}

// Usage
async function handleSubmit() {
    try {
        // Votre code sécurisé ici...
    } catch (error) {
        ErrorHandler.handle(error, 'submit_answer');
    }
}
```

**Solution attendue :**
```javascript
class ErrorHandler {
    static handle(error, context = '') {
        console.error(`Erreur dans ${context}:`, error);
        
        let userMessage;
        let severity = 'error';
        
        // Classification des erreurs
        if (error.name === 'ValidationError') {
            userMessage = error.message;
            severity = 'warning';
        } else if (error.name === 'NetworkError') {
            userMessage = 'Problème de connexion. Veuillez réessayer.';
        } else if (error.name === 'TypeError' && error.message.includes('null')) {
            userMessage = 'Veuillez sélectionner une réponse.';
            severity = 'info';
        } else {
            userMessage = 'Une erreur inattendue s\'est produite.';
        }
        
        // Afficher le message à l'utilisateur
        FeedbackManager.show(userMessage, severity);
        
        // Reporter l'erreur (en production)
        if (typeof window.reportError === 'function') {
            window.reportError(error, context);
        }
    }
    
    static async withErrorHandling(asyncFunction, fallback = null) {
        try {
            return await asyncFunction();
        } catch (error) {
            this.handle(error, 'async_operation');
            return fallback;
        }
    }
    
    static createValidationError(message) {
        const error = new Error(message);
        error.name = 'ValidationError';
        return error;
    }
}
```

### Exercice 4.2 : Système de feedback utilisateur

**Mission :** Créez un gestionnaire de messages de feedback :

```javascript
class FeedbackManager {
    static show(message, type = 'info', duration = 3000) {
        // Types : 'success', 'error', 'warning', 'info'
        // Créer et afficher le message
        // Auto-suppression après duration
        // Votre code ici...
    }
    
    static showLoading(message = 'Chargement...') {
        // Afficher un indicateur de chargement
        // Votre code ici...
    }
    
    static hideLoading() {
        // Masquer l'indicateur de chargement
        // Votre code ici...
    }
    
    static confirm(message) {
        // Afficher une confirmation personnalisée
        // Retourner une Promise<boolean>
        // Votre code ici...
    }
}
```

**Solution attendue :**
```javascript
class FeedbackManager {
    static container = null;
    static loadingElement = null;
    
    static init() {
        // Créer le conteneur de messages s'il n'existe pas
        if (!this.container) {
            this.container = document.createElement('div');
            this.container.id = 'feedback-container';
            this.container.style.cssText = `
                position: fixed;
                top: 20px;
                right: 20px;
                z-index: 1000;
                max-width: 300px;
            `;
            document.body.appendChild(this.container);
        }
    }
    
    static show(message, type = 'info', duration = 3000) {
        this.init();
        
        const messageElement = document.createElement('div');
        messageElement.className = `feedback-message feedback-${type}`;
        messageElement.textContent = message;
        
        // Styles selon le type
        const styles = {
            info: { backgroundColor: '#e3f2fd', color: '#1976d2', border: '1px solid #2196f3' },
            success: { backgroundColor: '#e8f5e8', color: '#2e7d32', border: '1px solid #4caf50' },
            warning: { backgroundColor: '#fff3e0', color: '#f57c00', border: '1px solid #ff9800' },
            error: { backgroundColor: '#ffebee', color: '#d32f2f', border: '1px solid #f44336' }
        };
        
        Object.assign(messageElement.style, {
            padding: '12px 16px',
            marginBottom: '8px',
            borderRadius: '4px',
            fontSize: '14px',
            fontWeight: '500',
            boxShadow: '0 2px 8px rgba(0,0,0,0.1)',
            animation: 'slideIn 0.3s ease-out',
            ...styles[type]
        });
        
        this.container.appendChild(messageElement);
        
        // Auto-suppression
        if (duration > 0) {
            setTimeout(() => {
                if (messageElement.parentNode) {
                    messageElement.style.animation = 'slideOut 0.3s ease-in';
                    setTimeout(() => messageElement.remove(), 300);
                }
            }, duration);
        }
        
        return messageElement;
    }
    
    static showLoading(message = 'Chargement...') {
        this.hideLoading(); // Supprimer le précédent
        
        this.loadingElement = document.createElement('div');
        this.loadingElement.className = 'loading-overlay';
        this.loadingElement.innerHTML = `
            <div class="loading-content">
                <div class="spinner"></div>
                <p>${message}</p>
            </div>
        `;
        
        this.loadingElement.style.cssText = `
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.5);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 2000;
        `;
        
        document.body.appendChild(this.loadingElement);
    }
    
    static hideLoading() {
        if (this.loadingElement) {
            this.loadingElement.remove();
            this.loadingElement = null;
        }
    }
    
    static async confirm(message, options = {}) {
        return new Promise((resolve) => {
            const modal = document.createElement('div');
            modal.className = 'confirm-modal';
            modal.innerHTML = `
                <div class="confirm-content">
                    <p>${message}</p>
                    <div class="confirm-buttons">
                        <button class="btn-cancel">${options.cancelText || 'Annuler'}</button>
                        <button class="btn-confirm">${options.confirmText || 'Confirmer'}</button>
                    </div>
                </div>
            `;
            
            // Styles et événements...
            modal.style.cssText = `
                position: fixed;
                top: 0;
                left: 0;
                width: 100%;
                height: 100%;
                background: rgba(0,0,0,0.5);
                display: flex;
                justify-content: center;
                align-items: center;
                z-index: 3000;
            `;
            
            modal.querySelector('.btn-confirm').onclick = () => {
                modal.remove();
                resolve(true);
            };
            
            modal.querySelector('.btn-cancel').onclick = () => {
                modal.remove();
                resolve(false);
            };
            
            document.body.appendChild(modal);
        });
    }
}
```

### Exercice 4.3 : États de chargement et transitions

**Mission :** Gérez les états de l'interface pendant les opérations asynchrones :

```javascript
class UIStateManager {
    constructor(elements) {
        this.elements = elements;
        this.currentState = 'idle';
    }
    
    setState(newState, data = {}) {
        // États possibles : 'idle', 'loading', 'success', 'error'
        // Mettre à jour l'interface selon l'état
        // Votre code ici...
    }
    
    async executeWithState(asyncOperation, loadingMessage = 'Chargement...') {
        // Wrapper qui gère automatiquement les états
        // Votre code ici...
    }
}

// Usage
const uiManager = new UIStateManager({
    submitButton: document.querySelector('#submit'),
    form: document.querySelector('#quiz-form'),
    container: document.querySelector('#quiz-container')
});

async function handleSubmit() {
    await uiManager.executeWithState(async () => {
        // Opération asynchrone
        const result = await processAnswer();
        return result;
    }, 'Vérification de la réponse...');
}
```

### Exercice 4.4 : Récupération gracieuse d'erreurs

**Mission :** Implémentez des mécanismes de récupération :

```javascript
class RecoveryManager {
    static async retryOperation(operation, maxRetries = 3, delay = 1000) {
        // Réessayer une opération avec délai croissant
        // Votre code ici...
    }
    
    static createFallbackUI(error) {
        // Créer une interface de secours en cas d'erreur critique
        // Votre code ici...
    }
    
    static async gracefulDegradation(primaryOperation, fallbackOperation) {
        // Essayer l'opération principale, sinon utiliser le fallback
        // Votre code ici...
    }
}

// Exemple d'usage
async function loadQuestions() {
    return await RecoveryManager.gracefulDegradation(
        () => fetch('/api/questions').then(r => r.json()),
        () => {
            // Fallback : questions par défaut
            FeedbackManager.show('Mode hors ligne activé', 'warning');
            return getDefaultQuestions();
        }
    );
}
```

**Solution de récupération :**
```javascript
class RecoveryManager {
    static async retryOperation(operation, maxRetries = 3, delay = 1000) {
        let lastError;
        
        for (let attempt = 1; attempt <= maxRetries; attempt++) {
            try {
                return await operation();
            } catch (error) {
                lastError = error;
                
                if (attempt === maxRetries) {
                    throw error;
                }
                
                // Délai croissant (exponential backoff)
                const waitTime = delay * Math.pow(2, attempt - 1);
                FeedbackManager.show(
                    `Tentative ${attempt}/${maxRetries} échouée. Nouvel essai dans ${waitTime/1000}s...`,
                    'warning',
                    waitTime
                );
                
                await new Promise(resolve => setTimeout(resolve, waitTime));
            }
        }
        
        throw lastError;
    }
    
    static createFallbackUI(error) {
        const fallbackElement = document.createElement('div');
        fallbackElement.className = 'fallback-ui';
        fallbackElement.innerHTML = `
            <div class="error-state">
                <h2>Oups ! Quelque chose s'est mal passé</h2>
                <p>Nous rencontrons des difficultés techniques.</p>
                <button onclick="location.reload()">Recharger la page</button>
                <details>
                    <summary>Détails techniques</summary>
                    <pre>${error.stack || error.message}</pre>
                </details>
            </div>
        `;
        
        return fallbackElement;
    }
}
```

## 🧪 Tests à effectuer

### Test 1 : Gestion d'erreurs
```javascript
// Simulez différents types d'erreurs
// Vérifiez que l'application reste utilisable
// Testez la récupération automatique
```

### Test 2 : Feedback utilisateur
```javascript
// Testez tous les types de messages
// Vérifiez l'accessibilité (lecteurs d'écran)
// Testez sur mobile
```

### Test 3 : États de chargement
```javascript
// Simulez des opérations lentes
// Vérifiez que l'interface se bloque correctement
// Testez l'annulation d'opérations
```

## 💡 Conseils pratiques

1. **Toujours prévoir le cas d'erreur** dans chaque fonction
2. **Messages utilisateur clairs** et non techniques
3. **États visuels** pour toutes les opérations async
4. **Récupération automatique** quand possible
5. **Logging détaillé** pour le debug

## 🎓 Points clés à retenir

- Gérer les erreurs de manière proactive, pas réactive
- Fournir un feedback constant à l'utilisateur
- Maintenir l'interface utilisable même en cas d'erreur
- Implémenter des mécanismes de récupération
- Tester tous les cas d'erreur possibles

## 🚀 Défi bonus

Créez un système de rapport d'erreurs automatique :

```javascript
class ErrorReporter {
    static init(config) {
        // Configuration du système de rapport
        // Capture automatique des erreurs non gérées
    }
    
    static report(error, context, userAction) {
        // Envoyer le rapport d'erreur au serveur
        // Inclure contexte utilisateur et technique
    }
}
```

---

**Prochaine étape :** Passez au projet final - recréer le quiz complet avec toutes ces bonnes pratiques !
