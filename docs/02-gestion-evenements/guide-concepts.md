# Guide des Concepts - Gestion d'Événements

## 🎯 Vue d'ensemble
Ce guide explique comment gérer les événements JavaScript de manière robuste et efficace, en évitant les pièges courants.

## 📚 Concepts fondamentaux

### 1. Les événements en JavaScript

Les événements sont des actions qui se produisent dans le navigateur : clics, saisie clavier, chargement de page, etc.

```javascript
// L'utilisateur clique → Événement 'click' → Votre fonction s'exécute
button.addEventListener('click', function() {
    console.log('Bouton cliqué !');
});
```

### 2. Méthodes d'ajout d'événements

#### ❌ Attributs HTML (à éviter)
```html
<!-- Mélange HTML et JavaScript -->
<button onclick="handleClick()">Cliquez</button>
```

#### ❌ Propriétés DOM (limitées)
```javascript
// Un seul événement possible
button.onclick = function() { console.log('Click 1'); };
button.onclick = function() { console.log('Click 2'); }; // Écrase le précédent
```

#### ✅ addEventListener() (recommandé)
```javascript
// Plusieurs événements possibles
button.addEventListener('click', function() { console.log('Click 1'); });
button.addEventListener('click', function() { console.log('Click 2'); });

// Contrôle fin avec options
button.addEventListener('click', handler, {
    once: true,      // Une seule fois
    passive: true,   // N'appelle pas preventDefault
    capture: true    // Phase de capture
});
```

### 3. L'objet Event

Chaque événement fournit un objet `event` avec des informations utiles :

```javascript
function handleClick(event) {
    console.log('Type:', event.type);           // 'click'
    console.log('Target:', event.target);       // Élément cliqué
    console.log('Timestamp:', event.timeStamp); // Moment du clic
    
    // Empêcher le comportement par défaut
    event.preventDefault();
    
    // Empêcher la propagation
    event.stopPropagation();
}

button.addEventListener('click', handleClick);
```

### 4. Gestion des formulaires

#### Événement submit
```javascript
form.addEventListener('submit', function(event) {
    // TOUJOURS empêcher le rechargement de page
    event.preventDefault();
    
    // Traiter les données du formulaire
    const formData = new FormData(event.target);
    console.log('Données:', Object.fromEntries(formData));
});
```

#### Validation en temps réel
```javascript
input.addEventListener('input', function(event) {
    const value = event.target.value;
    validateInput(value);
});

input.addEventListener('blur', function(event) {
    // Validation quand l'utilisateur quitte le champ
    finalValidation(event.target.value);
});
```

### 5. Délégation d'événements

**Problème :** Les éléments créés dynamiquement n'ont pas d'événements.

```javascript
// ❌ Ne fonctionne pas pour les éléments créés après
document.querySelectorAll('.dynamic-button').forEach(button => {
    button.addEventListener('click', handleClick);
});
```

**Solution :** Écouter sur un parent qui existe toujours.

```javascript
// ✅ Fonctionne pour tous les éléments, même futurs
document.addEventListener('click', function(event) {
    // Vérifier si l'élément cliqué correspond
    if (event.target.matches('.dynamic-button')) {
        handleClick(event);
    }
});

// Ou plus spécifique
container.addEventListener('click', function(event) {
    if (event.target.classList.contains('answer-option')) {
        handleAnswerClick(event);
    }
});
```

### 6. Gestion des événements asynchrones

#### ❌ Problème courant
```javascript
button.addEventListener('click', async function(event) {
    const result = await longOperation();
    // L'utilisateur peut cliquer plusieurs fois pendant l'attente
    updateUI(result);
});
```

#### ✅ Solution robuste
```javascript
let isProcessing = false;

button.addEventListener('click', async function(event) {
    // Éviter les clics multiples
    if (isProcessing) return;
    
    try {
        isProcessing = true;
        button.disabled = true;
        button.textContent = 'Traitement...';
        
        const result = await longOperation();
        updateUI(result);
        
    } catch (error) {
        handleError(error);
    } finally {
        isProcessing = false;
        button.disabled = false;
        button.textContent = 'Valider';
    }
});
```

### 7. Validation des entrées utilisateur

#### Validation basique
```javascript
function validateInput(value) {
    if (!value || value.trim() === '') {
        return { isValid: false, error: 'Champ requis' };
    }
    
    if (value.length < 3) {
        return { isValid: false, error: 'Minimum 3 caractères' };
    }
    
    return { isValid: true, error: null };
}
```

#### Validation de formulaire complet
```javascript
function validateForm(formElement) {
    const errors = [];
    
    // Vérifier que le formulaire existe
    if (!formElement) {
        return { isValid: false, errors: ['Formulaire non trouvé'] };
    }
    
    // Vérifier les champs requis
    const requiredFields = formElement.querySelectorAll('[required]');
    requiredFields.forEach(field => {
        if (!field.value.trim()) {
            errors.push(`Le champ ${field.name} est requis`);
        }
    });
    
    // Vérifier les radios (au moins un sélectionné)
    const radioGroups = new Set();
    formElement.querySelectorAll('input[type="radio"]').forEach(radio => {
        radioGroups.add(radio.name);
    });
    
    radioGroups.forEach(groupName => {
        const checked = formElement.querySelector(`input[name="${groupName}"]:checked`);
        if (!checked) {
            errors.push(`Veuillez sélectionner une option pour ${groupName}`);
        }
    });
    
    return {
        isValid: errors.length === 0,
        errors: errors
    };
}
```

### 8. Patterns utiles

#### Debouncing (éviter les appels trop fréquents)
```javascript
function debounce(func, delay) {
    let timeoutId;
    return function(...args) {
        clearTimeout(timeoutId);
        timeoutId = setTimeout(() => func.apply(this, args), delay);
    };
}

// Usage
const debouncedSearch = debounce(function(query) {
    performSearch(query);
}, 300);

searchInput.addEventListener('input', function(event) {
    debouncedSearch(event.target.value);
});
```

#### Throttling (limiter la fréquence)
```javascript
function throttle(func, delay) {
    let lastCall = 0;
    return function(...args) {
        const now = Date.now();
        if (now - lastCall >= delay) {
            lastCall = now;
            func.apply(this, args);
        }
    };
}

// Usage pour scroll
const throttledScroll = throttle(function() {
    updateScrollPosition();
}, 100);

window.addEventListener('scroll', throttledScroll);
```

### 9. Gestion d'erreurs dans les événements

```javascript
function safeEventHandler(handler) {
    return function(event) {
        try {
            handler(event);
        } catch (error) {
            console.error('Erreur dans l\'événement:', error);
            showUserError('Une erreur est survenue');
        }
    };
}

// Usage
button.addEventListener('click', safeEventHandler(function(event) {
    // Code qui peut lever une erreur
    riskyOperation();
}));
```

### 10. Nettoyage des événements

```javascript
// Garder une référence pour pouvoir supprimer
function handleClick(event) {
    console.log('Cliqué');
}

// Ajouter
button.addEventListener('click', handleClick);

// Supprimer quand nécessaire
button.removeEventListener('click', handleClick);

// Ou utiliser AbortController (moderne)
const controller = new AbortController();

button.addEventListener('click', handleClick, {
    signal: controller.signal
});

// Supprimer tous les événements liés
controller.abort();
```

## 🔧 Outils de débogage

### Console du navigateur
```javascript
// Logger tous les événements sur un élément
function logAllEvents(element) {
    const events = ['click', 'mousedown', 'mouseup', 'keydown', 'keyup', 'focus', 'blur'];
    events.forEach(eventType => {
        element.addEventListener(eventType, function(event) {
            console.log(`${eventType}:`, event);
        });
    });
}

logAllEvents(document.querySelector('#my-element'));
```

### Inspection des événements
```javascript
// Dans la console du navigateur
getEventListeners(document.querySelector('#my-element'));
```

## ⚠️ Pièges courants à éviter

1. **Oublier `preventDefault()`** sur les formulaires
2. **Ne pas gérer les clics multiples** sur les boutons async
3. **Fuites mémoire** en n'enlevant pas les événements
4. **Délégation incorrecte** qui capture trop d'événements
5. **Validation côté client uniquement** (toujours valider côté serveur aussi)

## 🎯 Bonnes pratiques

1. **Toujours utiliser `addEventListener()`**
2. **Valider les entrées** avant traitement
3. **Gérer les états de chargement** pour les opérations async
4. **Utiliser la délégation** pour les éléments dynamiques
5. **Nettoyer les événements** quand ils ne sont plus nécessaires
6. **Séparer la logique** de la gestion d'événements
7. **Tester les cas d'erreur** et les interactions utilisateur

---

**Prochaine étape :** Passez aux exercices pratiques pour appliquer ces concepts !
