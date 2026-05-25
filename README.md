# MacDeck iOS

App native SwiftUI pour iPhone et iPad — transforme ton téléphone en StreamDeck pour contrôler ton Mac.

![Platform](https://img.shields.io/badge/iOS-16%2B-blue) ![Platform](https://img.shields.io/badge/iPadOS-16%2B-blue) ![Swift](https://img.shields.io/badge/Swift-5.9-orange) ![License](https://img.shields.io/badge/license-MIT-green)

---

## Écosystème MacDeck

| Repo | Description | Plateforme |
|------|-------------|------------|
| [MacDeck](https://github.com/Andre27aj/MacDeck) | Serveur Mac + interface web | macOS |
| **MacDeck-iOS** *(ce repo)* | App native Swift | iPhone / iPad |
| [MacDeck-Windows](https://github.com/Andre27aj/MacDeck-Windows) | Serveur Windows + interface web | Windows |

---

## Fonctionnalités

- **Découverte automatique** — trouve le Mac sur le réseau via Bonjour, sans entrer d'IP
- **Volume & luminosité** — sliders verticaux tactiles, précis
- **Mute** son et micro indépendamment avec feedback visuel
- **Médias** — play/pause, piste suivante/précédente, titre et artiste en cours
- **Grille d'apps personnalisable** — lance n'importe quelle app macOS en un tap, bordure verte si l'app est déjà ouverte
- **Page Système** — 11 actions : verrouiller, veille écran, capture d'écran, mode sombre, Mission Control, Ne pas déranger, veille Mac, corbeille, Spotlight, Cmd+Tab, Cmd+Q
- **Profils automatiques** — les raccourcis changent automatiquement selon l'app active sur le Mac (Figma, Xcode, Safari…)
- **Batterie Mac** — affichée en haut à gauche en temps réel
- **Feedback haptique** — vibration à chaque action
- **Portrait & paysage** — layout adaptatif pour iPhone et iPad
- **Thème sombre** — interface indigo/violet sur fond noir

---

## Prérequis

| Côté Mac | Côté iPhone/iPad |
|----------|-----------------|
| macOS 12+ | iOS / iPadOS 16+ |
| [MacDeck serveur](https://github.com/Andre27aj/MacDeck) lancé | Xcode 15+ pour installer |
| Node.js 18+ | Même réseau Wi-Fi que le Mac |

---

## Installation

### 1 — Lancer le serveur Mac

```bash
git clone https://github.com/Andre27aj/MacDeck.git
cd MacDeck
npm install
npm start
```

### 2 — Installer l'app iOS

```bash
git clone https://github.com/Andre27aj/MacDeck-iOS.git
cd MacDeck-iOS

# Générer le projet Xcode (nécessite xcodegen)
brew install xcodegen
xcodegen generate
```

Ouvre `MacDeck.xcodeproj` dans Xcode :

1. Connecte ton iPhone ou iPad au Mac via USB
2. Sélectionne ton appareil comme destination en haut de Xcode
3. Dans *Signing & Capabilities*, change le **Team** pour ton compte Apple (gratuit suffit)
4. Appuie sur **⌘R** pour compiler et installer

> Pas besoin d'un compte Apple Developer payant. Un compte gratuit permet d'installer sur tes propres appareils.

### Alternative — Xcode en ligne de commande

```bash
xcodegen generate
xcodebuild -scheme MacDeck -destination 'id=<UDID-de-ton-iphone>' build
```

---

## Utilisation

1. Lance le serveur Mac (`npm start` dans le repo MacDeck)
2. Ouvre **MacDeck** sur ton iPhone ou iPad
3. L'app trouve le Mac automatiquement via Bonjour
4. Si la connexion échoue (réseaux séparés, VPN…), tape l'icône ⚙️ et entre l'IP manuellement

### Personnaliser les apps

Tape l'icône ✏️ dans la grille pour modifier la liste d'apps :
- **Nom affiché** — ce qui apparaît sous le bouton
- **Nom de lancement** — le nom macOS exact de l'app (ex: `Figma`, `Slack`, `Google Chrome`)
- **Icône SF Symbol** — fallback si l'icône de l'app n'est pas trouvée

### Changer de page

Swipe gauche/droite ou utilise les onglets **Apps** / **Système** en haut de la grille.

---

## Structure du projet

```
MacDeck-iOS/
├── project.yml                    # Config xcodegen (génère le .xcodeproj)
└── MacDeck/
    ├── ContentView.swift          # Layout principal, détection orientation
    ├── LeftPanel.swift            # Volume, luminosité, médias, mute
    ├── DeckGrid.swift             # Grille d'apps + boutons fixes
    ├── PagedDeckView.swift        # Onglets Apps / Système avec swipe
    ├── SystemPage.swift           # Page des 11 actions système
    ├── AppEditorView.swift        # Éditeur de la grille d'apps
    ├── MutePanel.swift            # Boutons mute micro/son
    ├── ViewModel.swift            # État global & appels API
    ├── APIClient.swift            # Client HTTP + modèles de données
    ├── AppProfiles.swift          # Profils par app (Figma, Xcode, Safari)
    ├── AppProfilePage.swift       # Page des raccourcis du profil actif
    ├── ServiceDiscovery.swift     # Bonjour / mDNS auto-discovery
    └── Assets.xcassets/           # Icônes et couleurs
```

---

## Comment ça fonctionne

```
iPhone (SwiftUI)  ──HTTP──▶  Mac (Node.js serveur)  ──osascript/shell──▶  macOS
```

1. Le serveur Mac expose une API REST sur le port 3000
2. L'app iOS interroge `/system/status` toutes les 5 secondes pour synchroniser l'état
3. Chaque bouton envoie une requête POST à l'endpoint correspondant
4. Le serveur exécute la commande macOS (osascript, AppleScript, pmset…)

---

## Différence avec la PWA

L'app iOS native offre :
- **Bonjour auto-discovery** — pas besoin d'entrer l'IP manuellement
- **Feedback haptique** — vibration à chaque tap
- **Gestures natives** — swipe, scroll SwiftUI
- **Meilleure performance** — pas de délai navigateur

La PWA ([incluse dans le repo Mac](https://github.com/Andre27aj/MacDeck)) fonctionne sur Android, Windows, et tout navigateur — pratique si tu n'as pas Xcode.

---

## Limitations

- iPhone/iPad et Mac doivent être sur le **même réseau local**
- L'app doit être compilée avec Xcode (pas sur l'App Store)
- iOS 16+ requis
