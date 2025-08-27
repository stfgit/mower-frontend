# MowItNow Frontend

A modern Next.js web interface for the MowItNow lawn mower simulation system.

## Overview

MowItNow Frontend provides an interactive web interface for controlling and visualizing lawn mowers on a rectangular grid. This project has been separated from the backend Java application to enable independent development and deployment cycles.

## Features

- **Interactive Lawn Grid** - Visual representation with clickable cells
- **Real-time Mower Control** - Click-to-place mowers with directional indicators
- **Manual Controls** - Individual command buttons (Turn Left/Right, Move Forward)
- **Sequence Mode** - Execute command strings like "GAGAGAGAA"
- **Batch Processing** - Control multiple mowers simultaneously
- **Path Visualization** - Track mower movement history on the grid
- **Responsive Design** - Works seamlessly on desktop and mobile devices

## Prérequis

- Node.js 18+ 
- npm ou yarn
- Backend Spring Boot en cours d'exécution sur le port 8080

## Installation

```bash
cd mower-frontend
npm install
```

## Développement

```bash
npm run dev
```

L'application sera accessible à l'adresse http://localhost:3000

## Structure du projet

```
src/
├── app/
│   ├── layout.tsx          # Layout principal
│   └── page.tsx            # Page d'accueil avec l'interface complète
├── components/
│   ├── LawnGrid.tsx        # Grille interactive de la pelouse
│   ├── LawnSettings.tsx    # Configuration des dimensions
│   └── MowerControls.tsx   # Contrôles de la tondeuse
├── lib/
│   └── api.ts              # Client API pour communiquer avec le backend
└── types/
    └── mower.ts            # Types TypeScript pour les données
```

## API Backend

Le frontend communique avec le backend Spring Boot via les endpoints suivants :

- `GET /api/mower/health` - Vérification de l'état du service
- `POST /api/mower/execute` - Exécution d'une commande simple
- `POST /api/mower/batch` - Exécution de commandes multiples

## Configuration CORS

Le backend est configuré pour accepter les requêtes depuis :
- http://localhost:3000 (serveur de développement Next.js)
- http://127.0.0.1:3000

## Commandes de la tondeuse

- **G** : Tourner à gauche (90°)
- **D** : Tourner à droite (90°)  
- **A** : Avancer d'une case

## Utilisation

1. **Configuration** : Ajustez les dimensions de la pelouse (1x1 à 20x20)
2. **Placement** : Cliquez sur une case ou utilisez les contrôles pour positionner la tondeuse
3. **Contrôle manuel** : Utilisez les boutons pour des mouvements individuels
4. **Mode séquence** : Saisissez une chaîne de commandes (ex: "GAGAGAGAA")
5. **Visualisation** : Observez le chemin parcouru en temps réel

## Technologies utilisées

- **Next.js 15** avec App Router
- **TypeScript** pour la sécurité des types
- **Tailwind CSS** pour les styles
- **React Hooks** pour la gestion d'état
- **Fetch API** pour les appels HTTP

## Build de production

```bash
npm run build
npm start
```

L'application sera servie sur http://localhost:3000
