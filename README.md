# Formulaire Déclaration Préfecture — Conseil d'Administration

Formulaire web mobile-first destiné aux membres du CA pour la déclaration en préfecture.
Les données sont envoyées directement dans un Google Sheet via Google Apps Script.

---

## Structure des fichiers

```
formulaire/
├── index.html    # Formulaire complet
├── style.css     # Design mobile-first (DM Sans, couleurs neutres)
└── README.md     # Ce fichier — instructions de déploiement
```

---

## Étape 1 — Préparer le Google Sheet

1. Ouvrez [Google Sheets](https://sheets.google.com) et créez un nouveau classeur
2. Renommez-le (ex : *Déclarations CA 2024*)
3. La première ligne sera automatiquement remplie avec les en-têtes lors de la première soumission

---

## Étape 2 — Créer le script Apps Script

1. Dans votre Google Sheet, allez dans **Extensions > Apps Script**
2. Supprimez tout le code existant
3. Collez le code suivant :

```javascript
const SHEET_NAME = 'Feuille 1'; // Adaptez si votre feuille a un autre nom

function doPost(e) {
  try {
    const data = JSON.parse(e.postData.contents);
    const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName(SHEET_NAME);

    // En-têtes (créées automatiquement si la feuille est vide)
    const headers = [
      'Horodatage',
      'Fonction',
      'Civilité',
      'Nom',
      'Prénom',
      'Nationalité',
      'Profession',
      'Étage / Appartement',
      'Immeuble / Résidence',
      'N°',
      'Extension',
      'Type de voie',
      'Nom de la voie',
      'Lieu-dit / BP',
      'Code postal',
      'Commune',
    ];

    if (sheet.getLastRow() === 0) {
      sheet.appendRow(headers);
      sheet.getRange(1, 1, 1, headers.length)
        .setFontWeight('bold')
        .setBackground('#2D4A8A')
        .setFontColor('#FFFFFF');
      sheet.setFrozenRows(1);
    }

    // Nouvelle ligne de données
    const row = [
      new Date().toLocaleString('fr-FR'),
      data.fonction        || '',
      data.civilite        || '',
      data.nom             || '',
      data.prenom          || '',
      data.nationalite     || '',
      data.profession      || '',
      data.etage           || '',
      data.immeuble        || '',
      data.numero          || '',
      data.extension       || '',
      data.type_voie       || '',
      data.nom_voie        || '',
      data.lieu_dit        || '',
      data.code_postal     || '',
      data.commune         || '',
    ];

    sheet.appendRow(row);

    return ContentService
      .createTextOutput(JSON.stringify({ status: 'ok' }))
      .setMimeType(ContentService.MimeType.JSON);

  } catch (err) {
    return ContentService
      .createTextOutput(JSON.stringify({ status: 'error', message: err.message }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}

// Fonction de test (optionnel — pour vérifier depuis l'éditeur)
function testDoPost() {
  const fakeEvent = {
    postData: {
      contents: JSON.stringify({
        fonction: 'Trésorier',
        civilite: 'M.',
        nom: 'DUPONT',
        prenom: 'Jean',
        nationalite: 'Française',
        profession: 'Enseignant',
        etage: '',
        immeuble: '',
        numero: '12',
        extension: 'bis',
        type_voie: 'Rue',
        nom_voie: 'Victor Hugo',
        lieu_dit: '',
        code_postal: '75011',
        commune: 'Paris',
      }),
    },
  };
  const result = doPost(fakeEvent);
  Logger.log(result.getContent());
}
```

4. Cliquez sur **Enregistrer** (icône disquette) et nommez le projet (ex : *FormCA*)

---

## Étape 3 — Déployer la Web App

1. Cliquez sur **Déployer > Nouvelle déploiement**
2. Type : **Application Web**
3. Remplissez :
   - Description : `Formulaire CA v1`
   - Exécuter en tant que : **Moi** (votre compte Google)
   - Qui peut accéder : **Tout le monde** *(requis pour que le formulaire puisse envoyer)*
4. Cliquez **Déployer**
5. Autorisez l'accès si demandé (compte Google > Autoriser)
6. **Copiez l'URL** de déploiement — elle ressemble à :
   ```
   https://script.google.com/macros/s/XXXXXXXXXXXXXXXXXXX/exec
   ```

---

## Étape 4 — Connecter le formulaire

Ouvrez `index.html` et remplacez la ligne :

```javascript
const APPS_SCRIPT_URL = 'VOTRE_URL_APPS_SCRIPT_ICI';
```

par :

```javascript
const APPS_SCRIPT_URL = 'https://script.google.com/macros/s/VOTRE_ID/exec';
```

---

## Étape 5 — Diffusion par QR code

1. Hébergez `index.html` + `style.css` sur un hébergeur statique :
   - **GitHub Pages** (gratuit) — [docs.github.com/pages](https://docs.github.com/pages)
   - **Netlify Drop** (gratuit, glisser-déposer) — [app.netlify.com/drop](https://app.netlify.com/drop)
   - **Vercel** (gratuit) — [vercel.com](https://vercel.com)
2. Une fois l'URL publique obtenue (ex : `https://mon-site.netlify.app`), générez un QR code :
   - [qr-code-generator.com](https://www.qr-code-generator.com)
   - ou directement dans Google Chrome : `...` > Partager > Créer un QR code

---

## Mise à jour du script (si changements futurs)

Si vous modifiez le script Apps Script, il faut créer un **nouveau déploiement** :
- Déployer > Gérer les déploiements > Nouvelle version
- L'URL reste la même si vous choisissez "Gérer les déploiements > Modifier > Version : Nouvelle version"

---

## Colonnes dans le Google Sheet

| Colonne | Données |
|---|---|
| A | Horodatage |
| B | Fonction |
| C | Civilité |
| D | Nom |
| E | Prénom |
| F | Nationalité |
| G | Profession |
| H | Étage / Appartement |
| I | Immeuble / Résidence |
| J | N° |
| K | Extension |
| L | Type de voie |
| M | Nom de la voie |
| N | Lieu-dit / BP |
| O | Code postal |
| P | Commune |
