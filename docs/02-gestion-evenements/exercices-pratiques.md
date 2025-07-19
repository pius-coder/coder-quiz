# Exercices Pratiques - Gestion d'Événements

## 🎯 Instructions

Ces exercices vous apprendront à gérer les événements de manière robuste. Consultez le `guide-concepts.md` pour comprendre les concepts.

## 📁 Structure des fichiers

```
exercice-events/
├── index.html
├── style.css
└── script.js
```

## 🏋️ Exercice 1 : Événements de base et validation

### Étape 1.1 : Structure HTML

Créez `index.html` :

```html
<!DOCTYPE html>
<html lang="fr">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Exercice Événements</title>
    <link rel="stylesheet" href="style.css" />
  </head>
  <body>
    <div id="app">
      <header>
        <h1>Quiz avec Gestion d'Événements</h1>
        <div class="status" id="status">Prêt</div>
      </header>

      <main>
        <!-- Écran d'accueil -->
        <section id="welcome-screen">
          <h2>Bienvenue !</h2>
          <form id="user-form">
            <div class="form-group">
              <label for="username">Nom d'utilisateur :</label>
              <input type="text" id="username" name="username" required />
              <span class="error-message" id="username-error"></span>
            </div>
            <button type="submit" id="start-btn">Commencer le Quiz</button>
          </form>
        </section>

        <!-- Écran de quiz -->
        <section id="quiz-screen" class="hidden">
          <div class="question-container">
            <h3 id="question-text"></h3>
            <form id="answer-form">
              <div id="answers-container"></div>
              <button type="submit" id="submit-answer">
                Valider la réponse
              </button>
            </form>
          </div>
          <div class="quiz-controls">
            <button id="next-question" class="hidden">Question suivante</button>
            <button id="finish-quiz" class="hidden">Terminer le quiz</button>
          </div>
        </section>

        <!-- Zone de feedback -->
        <div id="feedback-area"></div>
      </main>
    </div>

    <script src="script.js"></script>
  </body>
</html>
```

### Étape 1.2 : CSS de base

Créez `style.css` :

```css
.hidden {
  display: none;
}
.error {
  color: red;
  border-color: red;
}
.success {
  color: green;
}
.loading {
  opacity: 0.6;
  pointer-events: none;
}

.form-group {
  margin-bottom: 15px;
}

.error-message {
  display: block;
  color: red;
  font-size: 0.8em;
  margin-top: 5px;
}

.feedback {
  padding: 10px;
  margin: 10px 0;
  border-radius: 5px;
  animation: slideIn 0.3s ease-out;
}

.feedback.success {
  background: #d4edda;
  color: #155724;
}
.feedback.error {
  background: #f8d7da;
  color: #721c24;
}
.feedback.info {
  background: #d1ecf1;
  color: #0c5460;
}

@keyframes slideIn {
  from {
    opacity: 0;
    transform: translateY(-10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
```

### Étape 1.3 : Gestion d'événements de base

Dans `script.js`, implémentez ces fonctions :

```javascript
/**
 * Initialise tous les événements de l'application
 */
function initializeEventListeners() {
  // TODO: Implémenter l'initialisation des événements
  // 1. Événement submit sur le formulaire utilisateur
  // 2. Événement submit sur le formulaire de réponse
  // 3. Événements sur les boutons de contrôle
  // 4. Validation en temps réel sur les inputs
}

/**
 * Gère la soumission du formulaire utilisateur
 * @param {Event} event - Événement de soumission
 */
function handleUserFormSubmit(event) {
  // TODO: Implémenter la gestion du formulaire
  // 1. Empêcher le rechargement de la page
  // 2. Valider les données du formulaire
  // 3. Afficher les erreurs ou passer à l'étape suivante
  // 4. Gérer l'état de chargement du bouton
}

/**
 * Valide les données du formulaire utilisateur
 * @param {FormData} formData - Données du formulaire
 * @returns {Object} - Résultat de la validation
 */
function validateUserForm(formData) {
  // TODO: Implémenter la validation
  // 1. Vérifier que le nom d'utilisateur n'est pas vide
  // 2. Vérifier la longueur minimale (3 caractères)
  // 3. Vérifier les caractères autorisés (lettres, chiffres, _)
  // 4. Retourner { isValid: boolean, errors: array }
}

/**
 * Affiche les erreurs de validation
 * @param {Array} errors - Liste des erreurs
 */
function displayValidationErrors(errors) {
  // TODO: Afficher les erreurs dans l'interface
  // 1. Nettoyer les erreurs précédentes
  // 2. Afficher chaque erreur près du champ concerné
  // 3. Ajouter la classe 'error' aux champs invalides
}
```

**🎯 Objectif :** Créer un système de validation en temps réel qui guide l'utilisateur.

## 🏋️ Exercice 2 : Délégation d'événements

### Étape 2.1 : Gestion des réponses dynamiques

Ajoutez ces fonctions :

```javascript
/**
 * Configure la délégation d'événements pour les réponses
 */
function setupAnswerDelegation() {
  // TODO: Implémenter la délégation d'événements
  // 1. Écouter les événements 'change' sur le container des réponses
  // 2. Vérifier que l'élément est un input radio
  // 3. Appeler handleAnswerSelection avec l'input sélectionné
}

/**
 * Gère la sélection d'une réponse
 * @param {HTMLInputElement} radioInput - Input radio sélectionné
 */
function handleAnswerSelection(radioInput) {
  // TODO: Implémenter la gestion de sélection
  // 1. Mettre à jour l'interface pour montrer la sélection
  // 2. Activer le bouton de validation
  // 3. Optionnel: feedback visuel sur la réponse sélectionnée
}

/**
 * Crée des réponses dynamiques pour une question
 * @param {Object} questionData - Données de la question
 */
function createDynamicAnswers(questionData) {
  const container = document.querySelector('#answers-container');
  if (!container) return;

  // Nettoyer les réponses précédentes
  container.innerHTML = '';

  // TODO: Créer les réponses dynamiquement
  // 1. Pour chaque réponse, créer un container
  // 2. Créer l'input radio avec les bons attributs
  // 3. Créer le label associé
  // 4. Ajouter au container
  // Note: Les événements seront gérés par délégation
}
```

### Étape 2.2 : Test de la délégation

```javascript
/**
 * Teste la délégation d'événements avec des éléments créés dynamiquement
 */
function testEventDelegation() {
  const testQuestion = {
    id: 'test-q1',
    question: "Test de délégation d'événements",
    answers: [
      'Réponse créée dynamiquement 1',
      'Réponse créée dynamiquement 2',
      'Réponse créée dynamiquement 3',
    ],
  };

  // TODO: Tester la délégation
  // 1. Créer les réponses dynamiquement
  // 2. Vérifier que les événements fonctionnent
  // 3. Logger les sélections dans la console
}
```

## 🏋️ Exercice 3 : Gestion asynchrone et états

### Étape 3.1 : Opérations asynchrones

```javascript
/**
 * Simule une opération asynchrone (ex: vérification serveur)
 * @param {any} data - Données à traiter
 * @returns {Promise} - Promesse de traitement
 */
function simulateAsyncOperation(data) {
  return new Promise((resolve, reject) => {
    // Simuler un délai réseau
    setTimeout(() => {
      // Simuler parfois une erreur
      if (Math.random() < 0.1) {
        reject(new Error('Erreur réseau simulée'));
      } else {
        resolve({ success: true, data: data });
      }
    }, 1000 + Math.random() * 2000); // 1-3 secondes
  });
}

/**
 * Gère la soumission d'une réponse avec opération asynchrone
 * @param {Event} event - Événement de soumission
 */
async function handleAnswerSubmit(event) {
  // TODO: Implémenter la gestion asynchrone
  // 1. Empêcher la soumission par défaut
  // 2. Valider qu'une réponse est sélectionnée
  // 3. Désactiver l'interface pendant le traitement
  // 4. Afficher un indicateur de chargement
  // 5. Traiter la réponse de manière asynchrone
  // 6. Gérer les erreurs potentielles
  // 7. Réactiver l'interface
  // 8. Afficher le résultat
}

/**
 * Met à jour l'état de l'interface pendant les opérations
 * @param {string} state - État: 'idle', 'loading', 'success', 'error'
 * @param {string} message - Message à afficher
 */
function updateUIState(state, message = '') {
  // TODO: Implémenter la gestion d'état
  // 1. Mettre à jour l'élément de statut
  // 2. Désactiver/activer les contrôles selon l'état
  // 3. Afficher les indicateurs visuels appropriés
}
```

### Étape 3.2 : Prévention des clics multiples

```javascript
/**
 * Crée un wrapper qui empêche les appels multiples
 * @param {Function} asyncFunction - Fonction asynchrone à protéger
 * @returns {Function} - Fonction protégée
 */
function preventMultipleCalls(asyncFunction) {
  // TODO: Implémenter la protection
  // 1. Utiliser une variable pour tracker l'état
  // 2. Retourner une fonction qui vérifie l'état
  // 3. Empêcher l'exécution si déjà en cours
  // 4. Remettre à jour l'état à la fin
}

// Usage attendu :
const protectedSubmit = preventMultipleCalls(handleAnswerSubmit);
// Utiliser protectedSubmit au lieu de handleAnswerSubmit
```

## 🏋️ Exercice 4 : Validation avancée et feedback

### Étape 4.1 : Système de feedback

```javascript
/**
 * Affiche un message de feedback à l'utilisateur
 * @param {string} message - Message à afficher
 * @param {string} type - Type: 'success', 'error', 'info'
 * @param {number} duration - Durée d'affichage en ms
 */
function showFeedback(message, type = 'info', duration = 3000) {
  // TODO: Implémenter le système de feedback
  // 1. Créer un élément de message
  // 2. Ajouter les classes CSS appropriées
  // 3. Ajouter au container de feedback
  // 4. Programmer la suppression automatique
  // 5. Permettre la suppression manuelle (bouton X)
}

/**
 * Valide un formulaire complet avec feedback en temps réel
 * @param {HTMLFormElement} form - Formulaire à valider
 * @returns {Object} - Résultat de la validation
 */
function validateFormWithFeedback(form) {
  // TODO: Validation complète avec feedback
  // 1. Valider chaque champ
  // 2. Afficher les erreurs en temps réel
  // 3. Mettre à jour l'état visuel des champs
  // 4. Retourner un résumé de la validation
}
```

### Étape 4.2 : Gestion d'erreurs robuste

```javascript
/**
 * Gestionnaire d'erreurs global pour les événements
 * @param {Error} error - Erreur capturée
 * @param {string} context - Contexte de l'erreur
 */
function handleEventError(error, context = '') {
  // TODO: Gestion d'erreurs
  // 1. Logger l'erreur pour le debug
  // 2. Classifier le type d'erreur
  // 3. Afficher un message utilisateur approprié
  // 4. Remettre l'interface dans un état stable
}

/**
 * Wrapper sécurisé pour les gestionnaires d'événements
 * @param {Function} handler - Gestionnaire d'événement
 * @param {string} context - Contexte pour le debug
 * @returns {Function} - Gestionnaire sécurisé
 */
function safeEventHandler(handler, context = '') {
  // TODO: Créer un wrapper sécurisé
  // 1. Retourner une fonction qui encapsule handler
  // 2. Utiliser try/catch pour capturer les erreurs
  // 3. Appeler handleEventError en cas d'erreur
}
```

## 🧪 Tests à effectuer

### Test 1 : Validation en temps réel

```javascript
// TODO: Créer un test qui :
// 1. Saisit des données invalides
// 2. Vérifie que les erreurs s'affichent
// 3. Corrige les données
// 4. Vérifie que les erreurs disparaissent
```

### Test 2 : Délégation d'événements

```javascript
// TODO: Créer un test qui :
// 1. Crée des éléments dynamiquement
// 2. Vérifie que les événements fonctionnent
// 3. Supprime et recrée des éléments
// 4. Vérifie que tout fonctionne encore
```

### Test 3 : Gestion asynchrone

```javascript
// TODO: Créer un test qui :
// 1. Déclenche une opération async
// 2. Vérifie l'état de chargement
// 3. Tente des clics multiples
// 4. Vérifie la gestion d'erreurs
```

## 🎯 Critères de réussite

- [ ] Les formulaires ne rechargent pas la page
- [ ] La validation fonctionne en temps réel
- [ ] Les éléments dynamiques ont des événements
- [ ] Les opérations async gèrent les états
- [ ] Les erreurs sont capturées et affichées
- [ ] L'interface reste utilisable en cas d'erreur
- [ ] Les clics multiples sont prévenus

## 💡 Conseils

1. **Testez chaque fonction** individuellement
2. **Utilisez la console** pour déboguer les événements
3. **Simulez les erreurs** pour tester la robustesse
4. **Vérifiez l'accessibilité** avec le clavier

## 🚀 Défi bonus

Si vous terminez rapidement, implémentez ces fonctionnalités avancées :

### Debouncing pour la validation

```javascript
/**
 * Crée une fonction avec debouncing
 * @param {Function} func - Fonction à débouncer
 * @param {number} delay - Délai en millisecondes
 * @returns {Function} - Fonction débouncée
 */
function createDebouncer(func, delay) {
  // TODO: Implémenter le debouncing
  // Utile pour éviter trop de validations pendant la saisie
}
```

### Système de raccourcis clavier

```javascript
/**
 * Gère les raccourcis clavier
 * @param {KeyboardEvent} event - Événement clavier
 */
function handleKeyboardShortcuts(event) {
  // TODO: Implémenter des raccourcis
  // Ex: Ctrl+Enter pour valider, Échap pour annuler
}
```

### Historique des actions

```javascript
/**
 * Enregistre les actions utilisateur pour debug/analytics
 * @param {string} action - Action effectuée
 * @param {Object} data - Données associées
 */
function logUserAction(action, data) {
  // TODO: Système de logging des interactions
}
```

---

**Prochaine étape :** Consultez les solutions pour comparer vos implémentations !
