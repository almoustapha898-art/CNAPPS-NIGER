# Obtenir l'application sans rien installer sur votre ordinateur

Tout se fait dans un navigateur, avec un compte GitHub gratuit (github.com). GitHub construit l'application pour vous, sur ses serveurs.

## Étape 1 — Mettre le projet sur GitHub
1. Créez un compte sur github.com, puis cliquez sur **New repository**. Nom : `consultation-nourrisson`. Choisissez **Private**. Cliquez **Create repository**.
2. Sur votre ordinateur, décompressez le fichier zip.
3. Dans le dépôt, cliquez sur **uploading an existing file**, puis glissez **tout le contenu** du dossier décompressé
   (dossiers `www`, `assets`, `play-store`, `.github` et les fichiers). Cliquez **Commit changes**.
   - Si le dossier caché `.github` ne se téléverse pas : cliquez **Add file › Create new file**, tapez le nom
     `.github/workflows/android.yml`, collez le contenu du fichier du même nom, puis **Commit**. Faites de même pour `creer-cle.yml`.

## Étape 2 — Fabriquer l'APK de test (à installer sur votre téléphone)
1. Onglet **Actions** › « Construire l'application Android » › **Run workflow**. Attendez 10 à 15 minutes.
2. Ouvrez l'exécution terminée, en bas **Artifacts** › téléchargez **apk-test** (un zip contenant `app-debug.apk`).
3. Envoyez `app-debug.apk` sur votre téléphone (WhatsApp, e-mail, câble), ouvrez-le et autorisez « Installer des applications inconnues » pour l'application utilisée.

Cet APK sert à tester. Il ne peut pas être envoyé sur le Play Store.

## Étape 3 — Fabriquer le fichier pour le Play Store (.aab)
1. Onglet **Actions** › « Créer la clé de signature » › **Run workflow**. Téléchargez l'artefact **cle-de-signature**.
   **Conservez ce dossier en lieu sûr** : sans cette clé, vous ne pourrez plus mettre l'application à jour.
2. Dans le dépôt : **Settings › Secrets and variables › Actions › New repository secret**. Créez 4 secrets :
   `KEYSTORE_B64` (contenu de `keystore.b64`), `KS_PASS`, `KEY_PASS` et `KEY_ALIAS` (valeurs du fichier `infos.txt`).
3. Relancez « Construire l'application Android ». L'artefact **bundle-play-store** contient `app-release.aab`, signé.
4. Suivez ensuite les étapes 5 de `README-publication.md` (Play Console).
5. Avant la publication, changez `appId` dans `capacitor.config.json` (ex. `ne.votreorganisation.consultationnourrisson`).

## Si une étape échoue
Ouvrez l'exécution en rouge dans l'onglet Actions, copiez les dernières lignes du journal d'erreur et envoyez-les-moi.
