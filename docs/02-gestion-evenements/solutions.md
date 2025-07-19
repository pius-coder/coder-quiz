# Solutions - Gestion d'Événements

## 🎯 Solutions complètes avec approche modulaire

Ces solutions utilisent des fonctions pures et une architecture modulaire sans POO.

## 🏋️ Solution Exercice 1 : Événements de base

```javascript
// Variables globales pour l'état de l'application
let isProcessing = false;
let currentUser = null;

/**
 * Initialise tous les événements de l'application
 */
function initializeEventListeners() {
  console.log('🚀 Initialisation des événements...');

  // Formulaire utilisateur
  const userForm = document.querySelector('#user-form');
  if (userForm) {
    userForm.addEventListener(
      'submit',
      safeEventHandler(handleUserFormSubmit, 'user-form-submit')
    );
  }

  // Formulaire de réponse
  const answerForm = document.querySelector('#answer-form');
  if (answerForm) {
    answerForm.addEventListener(
      'submit',
      safeEventHandler(handleAnswerSubmit, 'answer-form-submit')
    );
  }

  // Validation en temps réel
  const usernameInput = document.querySelector('#username');
  if (usernameInput) {
    // Debounced validation pour éviter trop d'appels
    const debouncedValidation = createDebouncer(validateUsernameRealTime, 300);
    usernameInput.addEventListener('input', debouncedValidation);
    usernameInput.addEventListener('blur', validateUsernameRealTime);
  }

  // Délégation pour les réponses dynamiques
  setupAnswerDelegation();

  // Boutons de contrôle
  setupControlButtons();

  console.log('✅ Événements initialisés');
}

/**
 * Gère la soumission du formulaire utilisateur
 * @param {Event} event - Événement de soumission
 */
async function handleUserFormSubmit(event) {
  event.preventDefault();

  if (isProcessing) return;

  const formData = new FormData(event.target);
  const validation = validateUserForm(formData);

  if (!validation.isValid) {
    displayValidationErrors(validation.errors);
    return;
  }

  try {
    isProcessing = true;
    updateUIState('loading', 'Vérification des données...');

    // Simuler une vérification serveur
    const result = await simulateAsyncOperation({
      username: formData.get('username'),
    });

    currentUser = result.data;
    showFeedback('Bienvenue ' + currentUser.username + ' !', 'success');

    // Passer à l'écran de quiz
    switchToQuizScreen();
  } catch (error) {
    handleEventError(error, 'user-form-submit');
  } finally {
    isProcessing = false;
    updateUIState('idle');
  }
}

/**
 * Valide les données du formulaire utilisateur
 * @param {FormData} formData - Données du formulaire
 * @returns {Object} - Résultat de la validation
 */
function validateUserForm(formData) {
  const errors = [];
  const username = formData.get('username');

  if (!username || username.trim() === '') {
    errors.push({
      field: 'username',
      message: "Le nom d'utilisateur est requis",
    });
  } else if (username.trim().length < 3) {
    errors.push({
      field: 'username',
      message: "Le nom d'utilisateur doit contenir au moins 3 caractères",
    });
  } else if (!/^[a-zA-Z0-9_]+$/.test(username.trim())) {
    errors.push({
      field: 'username',
      message:
        "Le nom d'utilisateur ne peut contenir que des lettres, chiffres et _",
    });
  }

  return {
    isValid: errors.length === 0,
    errors: errors,
  };
}

/**
 * Affiche les erreurs de validation
 * @param {Array} errors - Liste des erreurs
 */
function displayValidationErrors(errors) {
  // Nettoyer les erreurs précédentes
  clearValidationErrors();

  errors.forEach(error => {
    const field = document.querySelector(`#${error.field}`);
    const errorElement = document.querySelector(`#${error.field}-error`);

    if (field) {
      field.classList.add('error');
    }

    if (errorElement) {
      errorElement.textContent = error.message;
      errorElement.style.display = 'block';
    }
  });

  // Focus sur le premier champ en erreur
  if (errors.length > 0) {
    const firstErrorField = document.querySelector(`#${errors[0].field}`);
    if (firstErrorField) {
      firstErrorField.focus();
    }
  }
}

/**
 * Nettoie les erreurs de validation affichées
 */
function clearValidationErrors() {
  // Supprimer les classes d'erreur
  document.querySelectorAll('.error').forEach(element => {
    element.classList.remove('error');
  });

  // Vider les messages d'erreur
  document.querySelectorAll('.error-message').forEach(element => {
    element.textContent = '';
    element.style.display = 'none';
  });
}

/**
 * Validation en temps réel du nom d'utilisateur
 * @param {Event} event - Événement input ou blur
 */
function validateUsernameRealTime(event) {
  const username = event.target.value;
  const errorElement = document.querySelector('#username-error');
  const field = event.target;

  // Nettoyer l'état précédent
  field.classList.remove('error');
  if (errorElement) {
    errorElement.textContent = '';
    errorElement.style.display = 'none';
  }

  // Valider seulement si l'utilisateur a commencé à taper
  if (username.length === 0) return;

  const formData = new FormData();
  formData.set('username', username);
  const validation = validateUserForm(formData);

  if (!validation.isValid) {
    const usernameError = validation.errors.find(e => e.field === 'username');
    if (usernameError) {
      field.classList.add('error');
      if (errorElement) {
        errorElement.textContent = usernameError.message;
        errorElement.style.display = 'block';
      }
    }
  }
}
```

## 🏋️ Solution Exercice 2 : Délégation d'événements

```javascript
/**
 * Configure la délégation d'événements pour les réponses
 */
function setupAnswerDelegation() {
  const answersContainer = document.querySelector('#answers-container');
  if (!answersContainer) return;

  // Délégation pour les changements de sélection
  answersContainer.addEventListener('change', function (event) {
    if (event.target.type === 'radio') {
      handleAnswerSelection(event.target);
    }
  });

  // Délégation pour les effets visuels (hover, focus)
  answersContainer.addEventListener('focusin', function (event) {
    if (event.target.type === 'radio') {
      event.target.closest('.answer-option')?.classList.add('focused');
    }
  });

  answersContainer.addEventListener('focusout', function (event) {
    if (event.target.type === 'radio') {
      event.target.closest('.answer-option')?.classList.remove('focused');
    }
  });

  console.log("✅ Délégation d'événements configurée");
}

/**
 * Gère la sélection d'une réponse
 * @param {HTMLInputElement} radioInput - Input radio sélectionné
 */
function handleAnswerSelection(radioInput) {
  console.log('📝 Réponse sélectionnée:', radioInput.value);

  // Mettre à jour l'interface visuelle
  updateAnswerSelection(radioInput);

  // Activer le bouton de validation
  const submitButton = document.querySelector('#submit-answer');
  if (submitButton) {
    submitButton.disabled = false;
    submitButton.textContent = 'Valider la réponse';
  }

  // Feedback visuel optionnel
  showFeedback('Réponse sélectionnée', 'info', 1000);
}

/**
 * Met à jour l'affichage visuel de la sélection
 * @param {HTMLInputElement} selectedInput - Input sélectionné
 */
function updateAnswerSelection(selectedInput) {
  const container = document.querySelector('#answers-container');
  if (!container) return;

  // Supprimer la classe 'selected' de toutes les options
  container.querySelectorAll('.answer-option').forEach(option => {
    option.classList.remove('selected');
  });

  // Ajouter la classe 'selected' à l'option choisie
  const selectedOption = selectedInput.closest('.answer-option');
  if (selectedOption) {
    selectedOption.classList.add('selected');
  }
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

  questionData.answers.forEach((answerText, index) => {
    // Container de l'option
    const optionDiv = createElement('div', {
      classes: 'answer-option',
    });

    // Input radio
    const radioInput = createElement('input', {
      attributes: {
        type: 'radio',
        id: `${questionData.id}-answer-${index}`,
        name: `question-${questionData.id}`,
        value: index.toString(),
      },
    });

    // Label
    const label = createElement('label', {
      textContent: answerText,
      attributes: {
        htmlFor: `${questionData.id}-answer-${index}`,
      },
    });

    // Assembler
    optionDiv.appendChild(radioInput);
    optionDiv.appendChild(label);
    container.appendChild(optionDiv);
  });

  // Désactiver le bouton de validation
  const submitButton = document.querySelector('#submit-answer');
  if (submitButton) {
    submitButton.disabled = true;
    submitButton.textContent = 'Sélectionnez une réponse';
  }

  console.log('✅ Réponses dynamiques créées pour:', questionData.id);
}

/**
 * Teste la délégation d'événements
 */
function testEventDelegation() {
  console.log("🧪 Test de délégation d'événements...");

  const testQuestion = {
    id: 'test-q1',
    question:
      "Test de délégation d'événements - Quelle couleur préférez-vous ?",
    answers: [
      'Rouge (créé dynamiquement)',
      'Bleu (créé dynamiquement)',
      'Vert (créé dynamiquement)',
      'Jaune (créé dynamiquement)',
    ],
  };

  // Afficher la question de test
  const questionText = document.querySelector('#question-text');
  if (questionText) {
    questionText.textContent = testQuestion.question;
  }

  // Créer les réponses
  createDynamicAnswers(testQuestion);

  // Afficher l'écran de quiz
  switchToQuizScreen();

  // Test automatique après 2 secondes
  setTimeout(() => {
    const randomIndex = Math.floor(Math.random() * testQuestion.answers.length);
    const radioToSelect = document.querySelector(
      `input[name="question-${testQuestion.id}"][value="${randomIndex}"]`
    );

    if (radioToSelect) {
      radioToSelect.checked = true;
      radioToSelect.dispatchEvent(new Event('change', { bubbles: true }));
      console.log('✅ Test automatique: réponse', randomIndex, 'sélectionnée');
    }
  }, 2000);
}
```

## 🏋️ Solution Exercice 3 : Gestion asynchrone

```javascript
/**
 * Simule une opération asynchrone
 * @param {any} data - Données à traiter
 * @returns {Promise} - Promesse de traitement
 */
function simulateAsyncOperation(data) {
  return new Promise((resolve, reject) => {
    const delay = 1000 + Math.random() * 2000; // 1-3 secondes

    setTimeout(() => {
      // Simuler parfois une erreur (10% de chance)
      if (Math.random() < 0.1) {
        reject(new Error('Erreur réseau simulée'));
      } else {
        resolve({
          success: true,
          data: data,
          timestamp: Date.now(),
        });
      }
    }, delay);
  });
}

/**
 * Gère la soumission d'une réponse avec opération asynchrone
 * @param {Event} event - Événement de soumission
 */
async function handleAnswerSubmit(event) {
  event.preventDefault();

  if (isProcessing) {
    console.log('⚠️ Opération déjà en cours, ignorée');
    return;
  }

  try {
    // Validation de la sélection
    const formData = new FormData(event.target);
    const selectedAnswer = getSelectedAnswerFromForm(formData);

    if (selectedAnswer === null) {
      showFeedback('Veuillez sélectionner une réponse', 'error');
      return;
    }

    // Démarrer le traitement
    isProcessing = true;
    updateUIState('loading', 'Vérification de votre réponse...');

    // Opération asynchrone
    const result = await simulateAsyncOperation({
      answer: selectedAnswer,
      timestamp: Date.now(),
    });

    // Traitement réussi
    updateUIState('success', 'Réponse traitée avec succès !');
    showFeedback('Votre réponse a été enregistrée', 'success');

    // Activer les boutons de navigation
    showNavigationButtons();

    console.log('✅ Réponse traitée:', result);
  } catch (error) {
    updateUIState('error', 'Erreur lors du traitement');
    handleEventError(error, 'answer-submit');
  } finally {
    isProcessing = false;

    // Réactiver l'interface après un délai
    setTimeout(() => {
      if (!isProcessing) {
        updateUIState('idle');
      }
    }, 2000);
  }
}

/**
 * Met à jour l'état de l'interface
 * @param {string} state - État: 'idle', 'loading', 'success', 'error'
 * @param {string} message - Message à afficher
 */
function updateUIState(state, message = '') {
  const statusElement = document.querySelector('#status');
  const submitButton = document.querySelector('#submit-answer');
  const form = document.querySelector('#answer-form');

  // Mettre à jour le statut
  if (statusElement) {
    statusElement.textContent = message || getDefaultStatusMessage(state);
    statusElement.className = `status ${state}`;
  }

  // Gérer les contrôles selon l'état
  switch (state) {
    case 'loading':
      if (submitButton) {
        submitButton.disabled = true;
        submitButton.textContent = 'Traitement...';
      }
      if (form) {
        form.classList.add('loading');
      }
      break;

    case 'success':
      if (submitButton) {
        submitButton.disabled = true;
        submitButton.textContent = 'Réponse validée ✓';
      }
      break;

    case 'error':
      if (submitButton) {
        submitButton.disabled = false;
        submitButton.textContent = 'Réessayer';
      }
      if (form) {
        form.classList.remove('loading');
      }
      break;

    case 'idle':
    default:
      if (submitButton) {
        const hasSelection = document.querySelector(
          '#answers-container input:checked'
        );
        submitButton.disabled = !hasSelection;
        submitButton.textContent = hasSelection
          ? 'Valider la réponse'
          : 'Sélectionnez une réponse';
      }
      if (form) {
        form.classList.remove('loading');
      }
      break;
  }
}

/**
 * Retourne un message de statut par défaut
 * @param {string} state - État actuel
 * @returns {string} - Message par défaut
 */
function getDefaultStatusMessage(state) {
  const messages = {
    idle: 'Prêt',
    loading: 'Chargement...',
    success: 'Succès',
    error: 'Erreur',
  };
  return messages[state] || 'État inconnu';
}

/**
 * Crée un wrapper qui empêche les appels multiples
 * @param {Function} asyncFunction - Fonction asynchrone à protéger
 * @returns {Function} - Fonction protégée
 */
function preventMultipleCalls(asyncFunction) {
  let isRunning = false;

  return async function (...args) {
    if (isRunning) {
      console.log('⚠️ Appel multiple empêché');
      return;
    }

    try {
      isRunning = true;
      return await asyncFunction.apply(this, args);
    } finally {
      isRunning = false;
    }
  };
}
```

## 🏋️ Solution Exercice 4 : Validation avancée et feedback

```javascript
/**
 * Affiche un message de feedback à l'utilisateur
 * @param {string} message - Message à afficher
 * @param {string} type - Type: 'success', 'error', 'info'
 * @param {number} duration - Durée d'affichage en ms
 */
function showFeedback(message, type = 'info', duration = 3000) {
  const container = document.querySelector('#feedback-area');
  if (!container) return;

  // Créer l'élément de feedback
  const feedbackElement = createElement('div', {
    classes: ['feedback', type],
    textContent: message,
  });

  // Bouton de fermeture
  const closeButton = createElement('button', {
    textContent: '×',
    classes: 'close-btn',
    events: {
      click: () => feedbackElement.remove(),
    },
  });

  feedbackElement.appendChild(closeButton);
  container.appendChild(feedbackElement);

  // Suppression automatique
  if (duration > 0) {
    setTimeout(() => {
      if (feedbackElement.parentNode) {
        feedbackElement.style.animation = 'slideOut 0.3s ease-in';
        setTimeout(() => feedbackElement.remove(), 300);
      }
    }, duration);
  }

  return feedbackElement;
}

/**
 * Gestionnaire d'erreurs global pour les événements
 * @param {Error} error - Erreur capturée
 * @param {string} context - Contexte de l'erreur
 */
function handleEventError(error, context = '') {
  console.error(`❌ Erreur dans ${context}:`, error);

  // Classifier l'erreur et choisir le message utilisateur
  let userMessage = "Une erreur inattendue s'est produite.";
  let feedbackType = 'error';

  if (error.message.includes('réseau') || error.message.includes('fetch')) {
    userMessage = 'Problème de connexion. Veuillez réessayer.';
  } else if (error.message.includes('validation')) {
    userMessage = error.message;
    feedbackType = 'warning';
  } else if (error.name === 'TypeError') {
    userMessage = 'Erreur de données. Veuillez actualiser la page.';
  }

  // Afficher le message à l'utilisateur
  showFeedback(userMessage, feedbackType, 5000);

  // Remettre l'interface dans un état stable
  updateUIState('error', 'Erreur - Veuillez réessayer');
}

/**
 * Wrapper sécurisé pour les gestionnaires d'événements
 * @param {Function} handler - Gestionnaire d'événement
 * @param {string} context - Contexte pour le debug
 * @returns {Function} - Gestionnaire sécurisé
 */
function safeEventHandler(handler, context = '') {
  return function (event) {
    try {
      return handler(event);
    } catch (error) {
      handleEventError(error, context);
    }
  };
}
```

## 🛠️ Fonctions utilitaires

```javascript
/**
 * Crée une fonction avec debouncing
 * @param {Function} func - Fonction à débouncer
 * @param {number} delay - Délai en millisecondes
 * @returns {Function} - Fonction débouncée
 */
function createDebouncer(func, delay) {
  let timeoutId;

  return function (...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => func.apply(this, args), delay);
  };
}

/**
 * Récupère la réponse sélectionnée depuis un formulaire
 * @param {FormData} formData - Données du formulaire
 * @returns {number|null} - Index de la réponse ou null
 */
function getSelectedAnswerFromForm(formData) {
  // Chercher tous les champs radio
  for (const [key, value] of formData.entries()) {
    if (key.startsWith('question-')) {
      return parseInt(value, 10);
    }
  }
  return null;
}

/**
 * Passe à l'écran de quiz
 */
function switchToQuizScreen() {
  const welcomeScreen = document.querySelector('#welcome-screen');
  const quizScreen = document.querySelector('#quiz-screen');

  if (welcomeScreen) welcomeScreen.classList.add('hidden');
  if (quizScreen) quizScreen.classList.remove('hidden');
}

/**
 * Affiche les boutons de navigation
 */
function showNavigationButtons() {
  const nextButton = document.querySelector('#next-question');
  const finishButton = document.querySelector('#finish-quiz');

  if (nextButton) nextButton.classList.remove('hidden');
  if (finishButton) finishButton.classList.remove('hidden');
}

/**
 * Configure les boutons de contrôle
 */
function setupControlButtons() {
  const nextButton = document.querySelector('#next-question');
  const finishButton = document.querySelector('#finish-quiz');

  if (nextButton) {
    nextButton.addEventListener(
      'click',
      safeEventHandler(handleNextQuestion, 'next-question')
    );
  }

  if (finishButton) {
    finishButton.addEventListener(
      'click',
      safeEventHandler(handleFinishQuiz, 'finish-quiz')
    );
  }
}

/**
 * Gère le passage à la question suivante
 * @param {Event} event - Événement de clic
 */
function handleNextQuestion(event) {
  showFeedback('Passage à la question suivante...', 'info', 1000);
  // Logique pour charger la question suivante
  console.log('➡️ Question suivante demandée');
}

/**
 * Gère la fin du quiz
 * @param {Event} event - Événement de clic
 */
function handleFinishQuiz(event) {
  showFeedback('Quiz terminé ! Merci de votre participation.', 'success', 3000);
  console.log('🏁 Fin du quiz demandée');
}

/**
 * Fonction d'initialisation principale
 */
function initializeApp() {
  console.log("🚀 Initialisation de l'application...");

  // Initialiser les événements
  initializeEventListeners();

  // État initial
  updateUIState('idle', 'Application prête');

  console.log('✅ Application initialisée avec succès');
}

// Démarrage de l'application
if (document.readyState === 'loading') {
  document.addEventListener('DOMContentLoaded', initializeApp);
} else {
  initializeApp();
}
```

## 🧪 Tests de validation

```javascript
/**
 * Suite de tests pour valider les fonctionnalités
 */
function runEventTests() {
  console.log("🧪 Démarrage des tests d'événements...");

  // Test 1: Validation en temps réel
  setTimeout(() => {
    console.log('Test 1: Validation temps réel');
    const usernameInput = document.querySelector('#username');
    if (usernameInput) {
      usernameInput.value = 'ab'; // Trop court
      usernameInput.dispatchEvent(new Event('input'));

      setTimeout(() => {
        usernameInput.value = 'validuser123';
        usernameInput.dispatchEvent(new Event('input'));
      }, 1000);
    }
  }, 1000);

  // Test 2: Délégation d'événements
  setTimeout(() => {
    console.log("Test 2: Délégation d'événements");
    testEventDelegation();
  }, 3000);

  // Test 3: Gestion d'erreurs
  setTimeout(() => {
    console.log("Test 3: Gestion d'erreurs");
    try {
      throw new Error("Test d'erreur simulée");
    } catch (error) {
      handleEventError(error, 'test-error');
    }
  }, 6000);
}

// Lancer les tests (décommenter pour tester)
// runEventTests();
```

## 🎯 Points clés des solutions

### Approche modulaire

- Fonctions spécialisées avec responsabilités claires
- État global minimal et contrôlé
- Réutilisabilité maximale des composants

### Gestion d'erreurs robuste

- Wrapper sécurisé pour tous les événements
- Classification des erreurs avec messages appropriés
- Récupération gracieuse de l'interface

### Performance optimisée

- Debouncing pour la validation temps réel
- Prévention des appels multiples
- Délégation d'événements efficace

### Expérience utilisateur

- Feedback visuel constant
- États de chargement clairs
- Validation progressive et informative

---

**Prochaine étape :** Passez au dossier suivant pour apprendre la gestion d'état !
