# Solutions - Manipulation DOM

## 🎯 Solutions complètes des exercices

Ces solutions utilisent une approche modulaire avec des fonctions pures (pas de POO).

## 🏋️ Solution Exercice 1 : Sélection sécurisée

```javascript
/**
 * Sélectionne un élément de manière sécurisée
 * @param {string} selector - Sélecteur CSS
 * @param {Element} parent - Élément parent (optionnel)
 * @returns {Element|null} - Élément trouvé ou null
 */
function safeQuerySelector(selector, parent = document) {
    try {
        const element = parent.querySelector(selector);
        if (!element) {
            console.warn(`⚠️ Élément non trouvé: ${selector}`);
        }
        return element;
    } catch (error) {
        console.error(`❌ Erreur de sélection: ${selector}`, error);
        return null;
    }
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
    
    // Vérifier les éléments critiques
    const criticalElements = ['app', 'startBtn', 'welcomeSection', 'quizSection'];
    const missingElements = criticalElements.filter(key => !elements[key]);
    
    if (missingElements.length > 0) {
        console.error('❌ Éléments critiques manquants:', missingElements);
        showError(`Éléments manquants: ${missingElements.join(', ')}`);
    } else {
        console.log('✅ Tous les éléments critiques sont présents');
    }
    
    return elements;
}

/**
 * Affiche un message d'erreur à l'utilisateur
 * @param {string} message - Message d'erreur
 */
function showError(message) {
    const errorDiv = document.createElement('div');
    errorDiv.className = 'error';
    errorDiv.textContent = `Erreur: ${message}`;
    errorDiv.style.cssText = `
        position: fixed;
        top: 20px;
        right: 20px;
        background: #f8d7da;
        color: #721c24;
        padding: 10px;
        border-radius: 5px;
        border: 1px solid #f5c6cb;
        z-index: 1000;
    `;
    
    document.body.appendChild(errorDiv);
    
    // Auto-suppression après 5 secondes
    setTimeout(() => {
        if (errorDiv.parentNode) {
            errorDiv.remove();
        }
    }, 5000);
}
```

## 🏋️ Solution Exercice 2 : Création d'éléments

```javascript
/**
 * Crée un élément avec des options
 * @param {string} tagName - Nom de la balise
 * @param {Object} options - Options de configuration
 * @returns {Element} - Élément créé
 */
function createElement(tagName, options = {}) {
    const element = document.createElement(tagName);
    
    // Ajouter le contenu texte
    if (options.textContent) {
        element.textContent = options.textContent;
    }
    
    // Ajouter les classes
    if (options.classes) {
        if (Array.isArray(options.classes)) {
            element.classList.add(...options.classes);
        } else {
            element.classList.add(options.classes);
        }
    }
    
    // Ajouter les attributs
    if (options.attributes) {
        Object.entries(options.attributes).forEach(([key, value]) => {
            element.setAttribute(key, value);
        });
    }
    
    // Ajouter les événements
    if (options.events) {
        Object.entries(options.events).forEach(([event, handler]) => {
            element.addEventListener(event, handler);
        });
    }
    
    return element;
}

/**
 * Crée une carte de question complète
 * @param {Object} questionData - Données de la question
 * @returns {Element} - Élément de la question
 */
function createQuestionCard(questionData) {
    // Container principal
    const questionCard = createElement('div', {
        classes: 'question-card',
        attributes: {
            'data-question-id': questionData.id
        }
    });
    
    // Titre de la question
    const questionTitle = createElement('h3', {
        textContent: questionData.question,
        classes: 'question-title'
    });
    
    // Container des réponses
    const answersContainer = createElement('div', {
        classes: 'answers'
    });
    
    // Créer chaque option de réponse
    questionData.answers.forEach((answerText, index) => {
        const answerOption = createRadioOption(questionData.id, index, answerText);
        answersContainer.appendChild(answerOption);
    });
    
    // Assembler la carte
    questionCard.appendChild(questionTitle);
    questionCard.appendChild(answersContainer);
    
    return questionCard;
}
```

## 🏋️ Solution Exercice 3 : Gestion des radios

```javascript
/**
 * Crée un input radio avec son label correctement configuré
 * @param {string} questionId - ID unique de la question
 * @param {number} answerIndex - Index de la réponse
 * @param {string} answerText - Texte de la réponse
 * @returns {Element} - Container avec input et label
 */
function createRadioOption(questionId, answerIndex, answerText) {
    const inputId = `${questionId}-answer-${answerIndex}`;
    const inputName = `question-${questionId}`;
    
    // Container de l'option
    const optionContainer = createElement('div', {
        classes: 'answer-option'
    });
    
    // Input radio
    const radioInput = createElement('input', {
        attributes: {
            type: 'radio',
            id: inputId,
            name: inputName,
            value: answerIndex.toString()
        }
    });
    
    // Label associé
    const label = createElement('label', {
        textContent: answerText,
        attributes: {
            htmlFor: inputId
        }
    });
    
    // Assembler l'option
    optionContainer.appendChild(radioInput);
    optionContainer.appendChild(label);
    
    return optionContainer;
}

/**
 * Récupère la réponse sélectionnée pour une question
 * @param {string} questionId - ID de la question
 * @returns {number|null} - Index de la réponse sélectionnée ou null
 */
function getSelectedAnswer(questionId) {
    const inputName = `question-${questionId}`;
    const selectedInput = safeQuerySelector(`input[name="${inputName}"]:checked`);
    
    if (!selectedInput) {
        return null;
    }
    
    return parseInt(selectedInput.value, 10);
}

/**
 * Teste que les groupes de radio buttons sont indépendants
 */
function testRadioGroups() {
    const container = safeQuerySelector('#answers-list');
    if (!container) {
        console.error('Container de test non trouvé');
        return;
    }
    
    // Nettoyer le container
    container.innerHTML = '';
    
    // Créer deux questions
    const question1 = {
        id: 'q1',
        question: 'Question 1: Quelle est la capitale de la France ?',
        answers: ['Londres', 'Paris', 'Berlin', 'Madrid']
    };
    
    const question2 = {
        id: 'q2',
        question: 'Question 2: Quel est le langage de programmation ?',
        answers: ['HTML', 'CSS', 'JavaScript', 'JSON']
    };
    
    // Créer et ajouter les cartes
    const card1 = createQuestionCard(question1);
    const card2 = createQuestionCard(question2);
    
    container.appendChild(card1);
    container.appendChild(card2);
    
    // Test automatique
    setTimeout(() => {
        // Sélectionner une réponse dans chaque groupe
        const radio1 = safeQuerySelector('input[name="question-q1"][value="1"]');
        const radio2 = safeQuerySelector('input[name="question-q2"][value="2"]');
        
        if (radio1) radio1.checked = true;
        if (radio2) radio2.checked = true;
        
        // Vérifier les sélections
        const selected1 = getSelectedAnswer('q1');
        const selected2 = getSelectedAnswer('q2');
        
        console.log('Test des groupes indépendants:');
        console.log('Question 1 sélectionnée:', selected1);
        console.log('Question 2 sélectionnée:', selected2);
        
        if (selected1 === 1 && selected2 === 2) {
            console.log('✅ Test réussi: Les groupes sont indépendants');
        } else {
            console.log('❌ Test échoué: Problème avec les groupes');
        }
    }, 100);
}
```

## 🏋️ Solution Exercice 4 : Intégration complète

```javascript
/**
 * Affiche une question dans l'interface
 * @param {Object} questionData - Données de la question
 */
function displayQuestion(questionData) {
    const elements = getRequiredElements();
    
    try {
        // Vérifier les éléments nécessaires
        if (!elements.questionText || !elements.answersList || !elements.quizSection) {
            throw new Error('Éléments d\'interface manquants');
        }
        
        // Mettre à jour le texte de la question
        elements.questionText.textContent = questionData.question;
        
        // Vider les réponses précédentes
        elements.answersList.innerHTML = '';
        
        // Créer les nouvelles options de réponse
        questionData.answers.forEach((answerText, index) => {
            const answerOption = createRadioOption(questionData.id, index, answerText);
            elements.answersList.appendChild(answerOption);
        });
        
        // Afficher la section quiz et masquer l'accueil
        if (elements.welcomeSection) {
            elements.welcomeSection.classList.add('hidden');
        }
        if (elements.quizSection) {
            elements.quizSection.classList.remove('hidden');
        }
        
        console.log('✅ Question affichée:', questionData.question);
        
    } catch (error) {
        console.error('❌ Erreur lors de l\'affichage de la question:', error);
        showError('Impossible d\'afficher la question');
    }
}

/**
 * Valide et récupère la réponse de l'utilisateur
 * @param {string} questionId - ID de la question
 * @returns {Object} - Résultat de la validation
 */
function validateUserAnswer(questionId) {
    try {
        const answerIndex = getSelectedAnswer(questionId);
        
        if (answerIndex === null) {
            return {
                isValid: false,
                answerIndex: null,
                error: 'Aucune réponse sélectionnée'
            };
        }
        
        return {
            isValid: true,
            answerIndex: answerIndex,
            error: null
        };
        
    } catch (error) {
        console.error('❌ Erreur lors de la validation:', error);
        return {
            isValid: false,
            answerIndex: null,
            error: 'Erreur de validation'
        };
    }
}

/**
 * Fonction de test complète
 */
function runCompleteTest() {
    console.log('🧪 Démarrage du test complet...');
    
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
    
    let currentQuestionIndex = 0;
    
    function showNextQuestion() {
        if (currentQuestionIndex < sampleQuestions.length) {
            const question = sampleQuestions[currentQuestionIndex];
            displayQuestion(question);
            
            // Simuler une sélection après 2 secondes
            setTimeout(() => {
                const radioToSelect = safeQuerySelector(`input[name="question-${question.id}"][value="${question.correctAnswer}"]`);
                if (radioToSelect) {
                    radioToSelect.checked = true;
                    console.log(`✅ Réponse sélectionnée pour ${question.id}: ${question.correctAnswer}`);
                    
                    // Valider la réponse
                    const validation = validateUserAnswer(question.id);
                    console.log('Validation:', validation);
                    
                    // Passer à la question suivante
                    currentQuestionIndex++;
                    setTimeout(showNextQuestion, 1000);
                } else {
                    console.error('❌ Impossible de sélectionner la réponse');
                }
            }, 2000);
        } else {
            console.log('🎉 Test terminé!');
        }
    }
    
    // Démarrer le test
    showNextQuestion();
}

// Utilitaires supplémentaires
/**
 * Réinitialise l'interface à l'état initial
 */
function resetInterface() {
    const elements = getRequiredElements();
    
    if (elements.welcomeSection) {
        elements.welcomeSection.classList.remove('hidden');
    }
    if (elements.quizSection) {
        elements.quizSection.classList.add('hidden');
    }
    if (elements.answersList) {
        elements.answersList.innerHTML = '';
    }
    if (elements.questionText) {
        elements.questionText.textContent = '';
    }
}

/**
 * Initialise l'application
 */
function initializeApp() {
    console.log('🚀 Initialisation de l\'application...');
    
    const elements = getRequiredElements();
    
    if (elements.startBtn) {
        elements.startBtn.addEventListener('click', () => {
            console.log('▶️ Démarrage du quiz');
            runCompleteTest();
        });
    }
    
    console.log('✅ Application initialisée');
}

// Démarrer l'application quand le DOM est prêt
if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', initializeApp);
} else {
    initializeApp();
}
```

## 🧪 Tests de validation

```javascript
/**
 * Suite de tests pour valider toutes les fonctionnalités
 */
function runValidationTests() {
    console.log('🧪 Démarrage des tests de validation...');
    
    // Test 1: Sélection sécurisée
    console.log('Test 1: Sélection sécurisée');
    const existingElement = safeQuerySelector('#app');
    const nonExistingElement = safeQuerySelector('#non-existent');
    console.log('Élément existant:', !!existingElement);
    console.log('Élément inexistant:', !!nonExistingElement);
    
    // Test 2: Création d'éléments
    console.log('Test 2: Création d\'éléments');
    const testButton = createElement('button', {
        textContent: 'Test',
        classes: ['btn', 'test'],
        attributes: { id: 'test-btn' }
    });
    console.log('Bouton créé:', testButton.tagName, testButton.className, testButton.id);
    
    // Test 3: Radio buttons
    console.log('Test 3: Radio buttons');
    testRadioGroups();
    
    // Test 4: Validation
    console.log('Test 4: Validation');
    setTimeout(() => {
        const validation1 = validateUserAnswer('q1');
        const validation2 = validateUserAnswer('non-existent');
        console.log('Validation question existante:', validation1);
        console.log('Validation question inexistante:', validation2);
    }, 500);
    
    console.log('✅ Tests de validation terminés');
}

// Lancer les tests
// runValidationTests();
```

## 🎯 Points clés des solutions

### Approche modulaire
- Chaque fonction a une responsabilité unique
- Fonctions pures sans effets de bord
- Réutilisabilité maximale

### Gestion d'erreurs
- Vérification systématique des éléments
- Messages d'erreur informatifs
- Récupération gracieuse

### Bonnes pratiques DOM
- Sélection sécurisée avec try/catch
- Attributs corrects pour l'accessibilité
- Groupes de radio buttons indépendants

### Code maintenable
- Noms de fonctions explicites
- Documentation JSDoc
- Tests intégrés

---

**Prochaine étape :** Passez au dossier suivant pour apprendre la gestion d'événements !
