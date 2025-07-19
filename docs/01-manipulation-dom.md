# Exercice 1 : Manipulation du DOM et Sélection d'Éléments

## 🎯 Objectif
Apprendre à sélectionner et manipuler les éléments DOM de manière sécurisée, compétence essentielle pour créer des interfaces interactives.

## 📚 Notions à maîtriser
- `document.querySelector()` et `document.querySelectorAll()`
- Vérification de l'existence des éléments
- Création et modification d'éléments DOM
- Gestion des attributs et classes CSS

## 🏋️ Exercices pratiques

### Exercice 1.1 : Sélection sécurisée d'éléments

Créez un fichier HTML avec cette structure :
```html
<!DOCTYPE html>
<html>
<head>
    <title>DOM Exercise</title>
</head>
<body>
    <div id="container">
        <h1 class="title">Mon Application</h1>
        <button id="btn-start">Commencer</button>
        <div class="content"></div>
    </div>
    <script src="script.js"></script>
</body>
</html>
```

**Mission :** Écrivez un script qui :
1. Sélectionne tous les éléments nécessaires
2. Vérifie leur existence avant de les utiliser
3. Affiche un message d'erreur si un élément manque

```javascript
// ❌ MAUVAISE PRATIQUE
const button = document.querySelector('#btn-start');
button.addEventListener('click', handleClick); // Erreur si button est null

// ✅ BONNE PRATIQUE - À implémenter
// Votre code ici...
```

**Solution attendue :**
```javascript
// Sélection des éléments
const container = document.querySelector('#container');
const title = document.querySelector('.title');
const button = document.querySelector('#btn-start');
const content = document.querySelector('.content');

// Vérification de l'existence
const elements = { container, title, button, content };
const missingElements = [];

for (const [name, element] of Object.entries(elements)) {
    if (!element) {
        missingElements.push(name);
    }
}

if (missingElements.length > 0) {
    console.error(`Éléments manquants: ${missingElements.join(', ')}`);
} else {
    console.log('Tous les éléments sont présents');
    // Votre code peut continuer en sécurité
}
```

### Exercice 1.2 : Création dynamique d'éléments

**Mission :** Créez une fonction qui génère dynamiquement une carte de question :

```javascript
function createQuestionCard(questionData) {
    // Créer la structure :
    // <div class="question-card">
    //   <h2>Question text</h2>
    //   <div class="answers">
    //     <input type="radio" id="answer-0" name="question" value="0">
    //     <label for="answer-0">Réponse 1</label>
    //     <!-- ... autres réponses -->
    //   </div>
    // </div>
    
    // Votre code ici...
}

// Test avec ces données
const testQuestion = {
    question: "Quelle est la capitale de la France ?",
    answers: ["Londres", "Paris", "Berlin", "Madrid"]
};
```

**Points clés à respecter :**
- Utilisez `createElement()` au lieu de `innerHTML` pour la sécurité
- Configurez correctement les attributs `id`, `name`, `for`
- Ajoutez les classes CSS appropriées

### Exercice 1.3 : Gestion des attributs et propriétés

**Mission :** Complétez cette fonction qui configure correctement un input radio :

```javascript
function createRadioInput(questionIndex, answerIndex, answerText) {
    const input = document.createElement('input');
    const label = document.createElement('label');
    
    // ❌ Configuration incorrecte
    // input.setAttribute('name', 'answer');
    // label.setAttribute('htmlFor', 'answer');
    
    // ✅ Configuration correcte - À compléter
    // Votre code ici...
    
    return { input, label };
}
```

**Critères de réussite :**
- Chaque groupe de questions doit avoir un `name` unique
- Les `id` et `htmlFor` doivent correspondre exactement
- La valeur de l'input doit être l'index de la réponse

## 🧪 Tests à effectuer

1. **Test de robustesse :** Supprimez un élément du HTML et vérifiez que votre code gère l'erreur
2. **Test de fonctionnalité :** Créez plusieurs cartes de questions et vérifiez que les radios fonctionnent indépendamment
3. **Test d'accessibilité :** Vérifiez que vous pouvez naviguer au clavier entre les réponses

## 💡 Conseils

1. **Toujours vérifier** l'existence des éléments avant de les utiliser
2. **Utiliser des noms uniques** pour les groupes de radio buttons
3. **Séparer la création** de la logique métier
4. **Tester régulièrement** vos fonctions avec différents cas

## 🎓 Points clés à retenir

- `querySelector()` retourne `null` si l'élément n'existe pas
- Les attributs `id` doivent être uniques dans toute la page
- L'attribut `name` groupe les radio buttons ensemble
- `htmlFor` doit correspondre exactement à l'`id` de l'input associé

## 🚀 Défi bonus

Créez une fonction utilitaire `safeQuerySelector()` qui :
- Prend un sélecteur en paramètre
- Retourne l'élément s'il existe
- Affiche un warning dans la console s'il n'existe pas
- Peut optionnellement créer l'élément manquant

```javascript
function safeQuerySelector(selector, options = {}) {
    // Votre implémentation ici
}

// Usage
const button = safeQuerySelector('#btn-start', { 
    createIfMissing: true, 
    tagName: 'button' 
});
```

---

**Prochaine étape :** Une fois ces exercices maîtrisés, passez à l'exercice 2 sur la gestion des événements !
