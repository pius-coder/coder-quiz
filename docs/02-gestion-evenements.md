# Exercice 2 : Gestion des Événements et Interactions Utilisateur

## 🎯 Objectif
Maîtriser la gestion des événements JavaScript pour créer des interfaces interactives et réactives, en évitant les pièges courants.

## 📚 Notions à maîtriser
- `addEventListener()` vs attributs HTML
- Gestion des événements de formulaire
- Validation des entrées utilisateur
- Propagation et délégation d'événements
- Gestion asynchrone des interactions

## 🏋️ Exercices pratiques

### Exercice 2.1 : Gestion basique des événements

Créez cette structure HTML :
```html
<div id="quiz-container">
    <button id="start-btn">Commencer</button>
    <div id="question-area" style="display: none;">
        <h2 id="question-text"></h2>
        <form id="answer-form">
            <div class="answers"></div>
            <button type="submit">Valider</button>
        </form>
    </div>
    <div id="result-area" style="display: none;"></div>
</div>
```

**Mission :** Implémentez la logique d'événements :

```javascript
// ❌ MAUVAISE PRATIQUE
// document.getElementById('start-btn').onclick = function() { ... }

// ✅ BONNE PRATIQUE - À implémenter
function initializeEventListeners() {
    // Votre code ici...
}

function handleStartClick() {
    // Afficher la zone de question
    // Cacher le bouton start
    // Charger la première question
}

function handleFormSubmit(event) {
    // Empêcher le rechargement de la page
    // Valider qu'une réponse est sélectionnée
    // Traiter la réponse
}
```

**Points clés :**
- Utilisez `addEventListener()` au lieu des attributs HTML
- Gérez `event.preventDefault()` pour les formulaires
- Séparez la logique d'événement de la logique métier

### Exercice 2.2 : Validation des entrées utilisateur

**Mission :** Créez une fonction robuste de validation :

```javascript
function validateUserInput(formElement) {
    // ❌ Code fragile
    // const selected = document.querySelector('input:checked');
    // return selected.value; // Erreur si rien n'est sélectionné
    
    // ✅ Code robuste - À implémenter
    // Votre code ici...
}

// Test cases à gérer :
// 1. Aucune réponse sélectionnée
// 2. Formulaire inexistant
// 3. Inputs radio mal configurés
```

**Solution attendue :**
```javascript
function validateUserInput(formElement) {
    // Vérifier que le formulaire existe
    if (!formElement) {
        return { isValid: false, error: 'Formulaire non trouvé' };
    }
    
    // Chercher la réponse sélectionnée
    const selectedInput = formElement.querySelector('input[type="radio"]:checked');
    
    if (!selectedInput) {
        return { 
            isValid: false, 
            error: 'Veuillez sélectionner une réponse' 
        };
    }
    
    return { 
        isValid: true, 
        value: parseInt(selectedInput.value),
        text: selectedInput.nextElementSibling?.textContent || ''
    };
}
```

### Exercice 2.3 : Gestion des événements dynamiques

**Mission :** Gérez les événements sur des éléments créés dynamiquement :

```javascript
// Problème : Les éléments créés après addEventListener ne reçoivent pas l'événement
function createDynamicAnswers(answers) {
    const container = document.querySelector('.answers');
    container.innerHTML = ''; // Nettoyer
    
    answers.forEach((answer, index) => {
        const div = document.createElement('div');
        div.innerHTML = `
            <input type="radio" id="answer-${index}" name="question" value="${index}">
            <label for="answer-${index}">${answer}</label>
        `;
        container.appendChild(div);
    });
    
    // ❌ PROBLÈME : Comment gérer les événements sur ces nouveaux éléments ?
    // ✅ SOLUTION : Délégation d'événements - À implémenter
}
```

**Solution avec délégation d'événements :**
```javascript
function setupEventDelegation() {
    // Écouter sur le parent qui existe toujours
    document.querySelector('.answers').addEventListener('change', function(event) {
        if (event.target.type === 'radio') {
            handleAnswerSelection(event.target);
        }
    });
}

function handleAnswerSelection(radioInput) {
    // Logique quand une réponse est sélectionnée
    console.log('Réponse sélectionnée:', radioInput.value);
    
    // Optionnel : feedback visuel
    highlightSelectedAnswer(radioInput);
}
```

### Exercice 2.4 : Gestion d'événements asynchrones

**Mission :** Gérez correctement les opérations asynchrones dans les événements :

```javascript
// ❌ PROBLÈME COURANT
async function handleSubmitBad(event) {
    event.preventDefault();
    
    const result = await processAnswer(); // Opération async
    
    if (result.correct) {
        // L'utilisateur peut cliquer plusieurs fois pendant l'attente
        showNextQuestion();
    }
}

// ✅ SOLUTION ROBUSTE - À implémenter
async function handleSubmitGood(event) {
    // Votre code ici...
    // Indices :
    // - Désactiver le bouton pendant le traitement
    // - Gérer les erreurs
    // - Réactiver l'interface après traitement
}
```

**Solution attendue :**
```javascript
async function handleSubmitGood(event) {
    event.preventDefault();
    
    const submitButton = event.target.querySelector('button[type="submit"]');
    const originalText = submitButton.textContent;
    
    try {
        // Désactiver l'interface
        submitButton.disabled = true;
        submitButton.textContent = 'Traitement...';
        
        // Valider les entrées
        const validation = validateUserInput(event.target);
        if (!validation.isValid) {
            showError(validation.error);
            return;
        }
        
        // Traitement asynchrone
        const result = await processAnswer(validation.value);
        
        // Traiter le résultat
        if (result.correct) {
            showSuccess('Bonne réponse !');
            setTimeout(() => showNextQuestion(), 1500);
        } else {
            showError('Mauvaise réponse !');
        }
        
    } catch (error) {
        console.error('Erreur lors du traitement:', error);
        showError('Une erreur est survenue');
    } finally {
        // Réactiver l'interface
        submitButton.disabled = false;
        submitButton.textContent = originalText;
    }
}
```

## 🧪 Tests à effectuer

### Test 1 : Validation robuste
```javascript
// Testez ces cas :
// 1. Soumettre sans sélectionner de réponse
// 2. Supprimer le formulaire du DOM puis soumettre
// 3. Cliquer rapidement plusieurs fois sur submit
```

### Test 2 : Événements multiples
```javascript
// Créez plusieurs questions dynamiquement
// Vérifiez que chaque groupe de radio fonctionne indépendamment
```

### Test 3 : Gestion d'erreurs
```javascript
// Simulez des erreurs réseau
// Vérifiez que l'interface reste utilisable
```

## 💡 Conseils pratiques

1. **Toujours utiliser `event.preventDefault()`** pour les formulaires
2. **Valider les entrées** avant tout traitement
3. **Désactiver les contrôles** pendant les opérations async
4. **Utiliser la délégation** pour les éléments dynamiques
5. **Gérer les erreurs** avec try/catch

## 🎓 Points clés à retenir

- `addEventListener()` est plus flexible que les attributs HTML
- La délégation d'événements résout les problèmes d'éléments dynamiques
- Toujours valider les entrées utilisateur
- Gérer l'état de l'interface pendant les opérations asynchrones
- Séparer la gestion d'événements de la logique métier

## 🚀 Défi bonus

Créez un système de "debouncing" pour éviter les clics multiples :

```javascript
function createDebouncer(func, delay) {
    // Votre implémentation ici
    // Doit empêcher l'exécution multiple de func
    // pendant la période de delay
}

// Usage
const debouncedSubmit = createDebouncer(handleSubmit, 1000);
submitButton.addEventListener('click', debouncedSubmit);
```

---

**Prochaine étape :** Passez à l'exercice 3 sur la gestion d'état et le flux de données !
