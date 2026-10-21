# Consultation Nourrisson — projet Android (Capacitor)

Ce dossier contient l'application web empaquetée en application Android avec Capacitor.
Il fonctionne **hors connexion** : tout est dans `www/index.html`, les données restent sur l'appareil.

## Ce qu'il faut avoir
- Un ordinateur (Windows, Mac ou Linux) avec **Node.js (version LTS)** et **Android Studio** (qui installe le JDK et le SDK Android).
- Un **compte développeur Google Play** (paiement unique à l'inscription, à vérifier sur la Play Console).

## 1. Préparer le projet
Dans un terminal, depuis ce dossier :

```
npm install @capacitor/core @capacitor/android @capacitor/filesystem @capacitor/share
npm install -D @capacitor/cli @capacitor/assets
```

Ouvrez `capacitor.config.json` et **changez `appId`** (ex. `ne.votreorganisation.consultationnourrisson`).
Cet identifiant est définitif une fois l'application publiée.

## 2. Générer le projet Android et les icônes
```
npx cap add android
npx capacitor-assets generate --android
npx cap sync android
```

Dans `android/app/src/main/AndroidManifest.xml`, ajoutez `android:allowBackup="false"` à la balise `<application>`
(pour éviter que les données de santé soient sauvegardées dans le cloud Google à votre insu).

## 3. Tester
```
npx cap open android
```
Dans Android Studio, branchez un téléphone (mode développeur activé) ou lancez un émulateur, puis cliquez sur « Run ».
Vérifiez : saisie d'un enfant, courbes, synthèse mensuelle, export de sauvegarde (le partage Android doit s'ouvrir).

## 4. Fabriquer le fichier à publier (.aab)
1. Dans `android/app/build.gradle` : `versionCode 1` et `versionName "1.0.0"` (à augmenter à chaque mise à jour).
2. Android Studio › **Build › Generate Signed App Bundle / APK › Android App Bundle**.
3. Créez une clé de signature (keystore) et **conservez-la, avec ses mots de passe, en lieu sûr** : sans elle, impossible de mettre à jour l'application.
   (Vous pouvez aussi activer « Play App Signing » dans la console.)
4. Le fichier `app-release.aab` est dans `android/app/release/`.

## 5. Publier sur la Play Console
1. **Créer une application** : nom « Consultation Nourrisson », langue français, application gratuite.
2. **Fiche du Play Store** : textes dans `play-store/listing-fr.md`, icône `play-store/icon-512.png`,
   image de présentation `play-store/feature-graphic-1024x500.png`, et **au moins 2 captures d'écran de téléphone** (à prendre sur votre téléphone).
3. **Politique de confidentialité** : publiez le texte de `play-store/privacy-policy-fr.md` sur une page web accessible
   (site de votre organisation, page GitHub, etc.) après l'avoir complété, puis collez son adresse dans la console.
4. **Contenu de l'application** : remplissez la déclaration de sécurité des données (voir `play-store/data-safety-fr.md`),
   la classification du contenu, le public cible (**adultes / professionnels de santé**, pas les enfants) et la
   déclaration relative aux applications de santé.
5. **Tests** : créez une piste de test (interne ou fermée), importez le `.aab`, faites-le tester.
   Selon le type de compte (personnel ou organisation) et la date de création, Google peut exiger un test fermé
   avec un nombre minimal de testeurs pendant une durée minimale avant la production : vérifiez les règles en vigueur dans la console.
6. **Production** : importez le `.aab`, renseignez les notes de version, envoyez pour examen.
   Google exige un niveau d'API cible récent : si la console le refuse, mettez à jour Capacitor (`npm install @capacitor/android@latest`) puis `npx cap sync`.

## Mettre à jour l'application
Remplacez `www/index.html` par la nouvelle version, puis `npx cap sync android`, augmentez `versionCode`, générez un nouveau `.aab`.

## Points d'attention avant de publier
- Les seuils cliniques, le calendrier vaccinal et les critères de prise en charge de la malnutrition doivent être **validés par le ministère de la Santé ou votre responsable technique**. L'application est un outil d'aide, pas un dispositif médical.
- Les données sont stockées dans le téléphone : prévoyez des sauvegardes régulières (bouton « Exporter la sauvegarde » dans la liste des enfants).
- L'envoi vers DHIS2 se fait par fichier à importer (pas d'envoi direct) : voir la section « Synchronisation DHIS2 » de la synthèse mensuelle.

## Sécurité des données (intégrée à l'application)
- Au premier lancement, l'agent crée un **code d'accès** (6 caractères minimum). Toutes les données sont alors **chiffrées (AES-256)** sur le téléphone.
- Le code est demandé à chaque ouverture, après 5 minutes d'inactivité ou si l'application reste en arrière-plan plus d'une minute. Après 5 essais ratés, un délai d'attente s'applique et s'allonge.
- Le code **ne peut pas être récupéré** : formez les agents à faire des sauvegardes (elles sont chiffrées avec le même code).
- Sur Android, les captures d'écran et l'aperçu dans les applications récentes sont bloqués par le workflow de construction, et la sauvegarde automatique Android est désactivée.
- Limites : le chiffrement protège un téléphone perdu ou volé, pas un téléphone déverrouillé et utilisé par une autre personne ; un code faible reste devinable. Faites réaliser un audit de sécurité avant un déploiement à grande échelle.
