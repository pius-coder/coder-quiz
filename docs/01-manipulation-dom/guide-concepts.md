# Guide des Concepts - Manipulation DOM

## 🎯 Vue d'ensemble
Ce guide explique tous les concepts nécessaires pour maîtriser la manipulation du DOM de manière sécurisée et efficace.

## 📚 Concepts fondamentaux

### 1. Le DOM (Document Object Model)

Le DOM est une représentation en arbre de votre document HTML que JavaScript peut manipuler.

```html
<!-- HTML -->
<div id="container">
    <h1 class="title">Mon titre</h1>
    <button id="btn">Cliquez-moi</button>
</div>
```

```javascript
// JavaScript voit cela comme un arbre d'objets
document
  └── html
      └── body
          └── div#container
              ├── h1.title
              └── button#btn
```

### 2. Sélection d'éléments

#### `document.querySelector()`
Sélectionne le **premier** élément qui correspond au sélecteur CSS.

```javascript
// Sélection par ID
const button = document.querySelector('#btn');

// Sélection par classe
const title = document.querySelector('.title');

// Sélection par balise
const firstDiv = document.querySelector('div');

// Sélecteurs complexes
const firstInput = document.querySelector('form input[type="radio"]');
```

#### `document.querySelectorAll()`
Sélectionne **tous** les éléments qui correspondent au sélecteur.

```javascript
// Récupère tous les boutons
const allButtons = document.querySelectorAll('button');

// Récupère tous les inputs radio
const radioInputs = document.querySelectorAll('input[type="radio"]');

// Conversion en array pour utiliser les méthodes d'array
const buttonsArray = Array.from(allButtons);
```

### 3. Vérification d'existence

**PROBLÈME :** `querySelector()` retourne `null` si l'élément n'existe pas.

```javascript
// ❌ DANGEREUX - Peut causer une erreur
const button = document.querySelector('#inexistant');
button.addEventListener('click', handleClick); // TypeError: Cannot read property 'addEventListener' of null
```

**SOLUTION :** Toujours vérifier avant d'utiliser.

```javascript
// ✅ SÉCURISÉ
const button = document.querySelector('#btn');
if (button) {
    button.addEventListener('click', handleClick);
} else {
    console.error('Bouton non trouvé');
}
```

### 4. Création d'éléments

#### `document.createElement()`
Crée un nouvel élément HTML en mémoire.

```javascript
// Créer un élément
const newDiv = document.createElement('div');

// Ajouter du contenu
newDiv.textContent = 'Mon contenu';

// Ajouter des classes
newDiv.classList.add('ma-classe');

// Ajouter des attributs
newDiv.setAttribute('id', 'mon-id');
newDiv.setAttribute('data-value', '123');
```

#### Ajout au DOM
```javascript
// Ajouter à la fin d'un parent
parentElement.appendChild(newDiv);

// Insérer avant un élément
parentElement.insertBefore(newDiv, referenceElement);

// Méthodes modernes
parentElement.append(newDiv); // Plus flexible
parentElement.prepend(newDiv); // Au début
```

### 5. Gestion des attributs

#### Attributs HTML standards
```javascript
const input = document.createElement('input');

// Méthode setAttribute/getAttribute
input.setAttribute('type', 'radio');
input.setAttribute('name', 'question1');
input.setAttribute('id', 'answer-1');
input.setAttribute('value', '1');

// Propriétés directes (plus rapide)
input.type = 'radio';
input.name = 'question1';
input.id = 'answer-1';
input.value = '1';
```

#### Attributs spéciaux pour les labels
```javascript
const label = document.createElement('label');
const input = document.createElement('input');

input.id = 'answer-1';
label.setAttribute('htmlFor', 'answer-1'); // Relie le label à l'input
// ou
label.htmlFor = 'answer-1';
```

### 6. Structure correcte des inputs radio

#### ❌ Structure incorrecte
```javascript
// PROBLÈME : Input dans le label
const label = document.createElement('label');
const input = document.createElement('input');

label.textContent = 'Ma réponse';
label.appendChild(input); // Input à l'intérieur du label
```

#### ✅ Structure correcte
```javascript
// SOLUTION : Input et label séparés mais liés
const container = document.createElement('div');
const input = document.createElement('input');
const label = document.createElement('label');

input.type = 'radio';
input.id = 'answer-1';
input.name = 'question';
input.value = '1';

label.htmlFor = 'answer-1'; // Lie le label à l'input
label.textContent = 'Ma réponse';

container.appendChild(input);
container.appendChild(label);
```

### 7. Gestion des groupes de radio buttons

Les radio buttons avec le même `name` forment un groupe - un seul peut être sélectionné.

```javascript
// Groupe 1 - Question 1
input1.name = 'question-1';
input2.name = 'question-1';
input3.name = 'question-1';

// Groupe 2 - Question 2 (indépendant du groupe 1)
input4.name = 'question-2';
input5.name = 'question-2';
```

### 8. Fonctions utilitaires recommandées

#### Sélection sécurisée
```javascript
function safeQuerySelector(selector, parent = document) {
    try {
        const element = parent.querySelector(selector);
        if (!element) {
            console.warn(`Élément non trouvé: ${selector}`);
        }
        return element;
    } catch (error) {
        console.error(`Erreur de sélection: ${selector}`, error);
        return null;
    }
}
```

#### Création d'élément avec options
```javascript
function createElement(tagName, options = {}) {
    const element = document.createElement(tagName);
    
    // Texte
    if (options.textContent) {
        element.textContent = options.textContent;
    }
    
    // Classes
    if (options.classes) {
        if (Array.isArray(options.classes)) {
            element.classList.add(...options.classes);
        } else {
            element.classList.add(options.classes);
        }
    }
    
    // Attributs
    if (options.attributes) {
        Object.entries(options.attributes).forEach(([key, value]) => {
            element.setAttribute(key, value);
        });
    }
    
    return element;
}

// Usage
const button = createElement('button', {
    textContent: 'Cliquez-moi',
    classes: ['btn', 'btn-primary'],
    attributes: {
        id: 'submit-btn',
        type: 'submit'
    }
});
```

## 🔧 Outils de débogage

### Console du navigateur
```javascript
// Inspecter un élément
console.log(element);

// Vérifier les propriétés
console.log('ID:', element.id);
console.log('Classes:', element.classList);
console.log('Attributs:', element.attributes);

// Vérifier la structure
console.log('Parent:', element.parentNode);
console.log('Enfants:', element.children);
```

### Sélecteur dans la console
```javascript
// Tester des sélecteurs directement dans la console
$('#mon-id') // Équivalent à document.querySelector('#mon-id')
$$('.ma-classe') // Équivalent à document.querySelectorAll('.ma-classe')
```

## ⚠️ Pièges courants à éviter

1. **Oublier de vérifier l'existence** des éléments
2. **Utiliser innerHTML** avec des données utilisateur (risque XSS)
3. **Mauvais attributs htmlFor** qui ne correspondent pas aux IDs
4. **Même name** pour des groupes de questions différents
5. **Modifier le DOM** pendant qu'on l'itère

## 🎯 Bonnes pratiques

1. **Toujours vérifier** l'existence avant utilisation
2. **Utiliser textContent** au lieu d'innerHTML quand possible
3. **Créer des fonctions utilitaires** pour les opérations répétitives
4. **Nommer clairement** les IDs et classes
5. **Grouper les opérations DOM** pour éviter les reflows multiples

---

**Prochaine étape :** Passez aux exercices pratiques pour appliquer ces concepts !
