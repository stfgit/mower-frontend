# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

MowItNow Frontend is a modern Next.js application that provides an interactive web interface for controlling lawn mowers. This is the frontend component of the MowItNow system, separated from the backend Java application for independent development and deployment.

**Core Features:**
- Interactive lawn grid visualization
- Real-time mower position tracking
- Manual control interface (Left/Right/Forward)
- Batch command execution
- Responsive design for desktop and mobile

## Architecture

**Technology Stack:**
- Next.js 15.4.5 with App Router
- React 19.1.0
- TypeScript 5
- TailwindCSS 4
- Turbopack for development builds

**Key Components:**
- `LawnGrid.tsx` - Interactive lawn visualization with clickable cells
- `LawnSettings.tsx` - Lawn dimension configuration
- `MowerControls.tsx` - Mower control interface and command execution
- `api.ts` - Backend API communication layer
- `mower.ts` - TypeScript type definitions

## Development Commands

**Install Dependencies:**
```bash
npm install
```

**Development Server (with Turbopack):**
```bash
npm run dev
```

**Build and Production:**
```bash
npm run build
npm run start
```

**Linting:**
```bash
npm run lint
```

**Testing:**
```bash
npm test
```

## Docker Deployment

**Build Docker Image:**
```bash
docker build -t stfgit/mower-frontend .
```

**Run Container:**
```bash
docker run -p 3000:3000 stfgit/mower-frontend
```

## Backend Integration

The frontend communicates with the MowItNow Java backend via REST API:

**API Base URL:** Configurable via environment variables
**Default:** `http://localhost:8080/api/mower`

**Key Endpoints:**
- `POST /execute` - Single mower command execution
- `POST /batch` - Multiple mower batch execution
- `GET /health` - Backend health check

## Environment Configuration

**Environment Variables:**
- `NEXT_PUBLIC_API_URL` - Backend API base URL
- `NODE_ENV` - Environment mode (development/production)

## Deployment

The application is deployed as a containerized Next.js application with:
- Static asset optimization
- Server-side rendering for initial load
- Client-side routing for navigation
- API proxy configuration for backend communication

## File Structure

```
src/
├── app/                 # Next.js App Router pages
│   ├── layout.tsx      # Root layout component
│   └── page.tsx        # Home page component
├── components/         # Reusable React components
│   ├── LawnGrid.tsx    # Interactive lawn visualization
│   ├── LawnSettings.tsx # Configuration controls
│   └── MowerControls.tsx # Mower operation interface
├── lib/
│   └── api.ts          # Backend API client
└── types/
    └── mower.ts        # TypeScript definitions
```

## UI Components

**LawnGrid:**
- Visual representation of the lawn with grid cells
- Clickable cells for mower positioning
- Real-time mower position updates with directional arrows
- Path tracing for mower movement history

**MowerControls:**
- Individual command buttons (Left/Right/Forward)
- Command sequence input field
- Batch execution for multiple mowers
- Results panel with execution history

**LawnSettings:**
- Lawn dimension configuration (1x1 to 20x20)
- Reset functionality
- Validation for valid lawn sizes

## Styling

- **TailwindCSS 4** for utility-first styling
- **Responsive design** with mobile-first approach
- **Dark mode support** via CSS variables
- **Component-scoped styling** for maintainability

## Development Workflow

1. Start the backend Java application on port 8080
2. Run the frontend development server: `npm run dev`
3. Access the application at `http://localhost:3000`
4. Make changes and see live updates via Turbopack
5. Build and test before deployment: `npm run build`

## Harness CI/CD Integration

The frontend has its own CI/CD pipeline with:
- npm dependency installation and caching
- ESLint code quality checks
- Next.js build optimization
- Docker image creation and registry push
- Kubernetes deployment with health checks

**Pipeline:** Uses the `account.npm_build` template v0.2 with `workingDirectory: "."` (root level)
**Docker Registry:** `stfgit/mower-frontend`
**Deployment:** Kubernetes with service exposure on port 80→3000