# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

MowItNow Frontend is a modern Next.js application that provides an interactive web interface for controlling lawn mowers. This is the frontend component of the MowItNow system, separated from the backend Java Spring Boot application for independent development and deployment within a DevSecOps environment.

**Core Features:**
- Interactive lawn grid visualization with clickable cells
- Real-time mower position tracking with directional indicators
- Manual control interface (Turn Left/Right, Move Forward)
- Command sequence execution (G/D/A protocol)
- Batch processing for multiple mowers
- Path visualization and movement history
- Responsive design for desktop and mobile

## Architecture

**Technology Stack:**
- Next.js 15.4.5 with App Router and standalone output
- React 19.1.0
- TypeScript 5
- TailwindCSS 4
- Turbopack for development builds

**Key Components:**
- `LawnGrid.tsx` - Interactive lawn visualization with directional arrows (↑→↓←)
  - Uses `DIRECTION_SYMBOLS` constant mapping (N/E/S/W → ↑→↓←)
  - Props: `dimensions`, `mowers`, `onCellClick`, `paths` for movement tracking
  - Renders clickable grid cells with mower positioning and path visualization
- `LawnSettings.tsx` - Lawn dimension configuration (1x1 to 20x20)
- `MowerControls.tsx` - Mower control interface and command execution
- `api.ts` - Backend API communication layer with static `MowerAPI` class
- `mower.ts` - TypeScript interfaces: `MowerPosition`, `LawnDimensions`, `MowerCommandRequest`, `BatchMowerRequest`

**Next.js Configuration:**
- Standalone output mode for Docker deployment
- Environment-based API URL configuration
- Static asset optimization

## Development Commands

**Install Dependencies:**
```bash
npm install
```

**Development Server (with Turbopack):**
```bash
npm run dev
# Uses Turbopack for faster builds and hot module replacement
# Automatically opens http://localhost:3000 with live reload
```

**Build and Production:**
```bash
npm run build
npm run start
```

**Linting:**
```bash
npm run lint
# ESLint 9 with Next.js Core Web Vitals and TypeScript rules
# Configuration: extends "next/core-web-vitals" and "next/typescript"
```

**Testing:**
```bash
npm test
# Note: No test scripts currently configured. Run `npm run lint` for code quality checks.
```

## Docker Deployment

**Multi-stage Build Architecture:**
- Build stage: Node.js 18 Alpine with production dependencies and Next.js build
- Production stage: Node.js 18 Alpine with non-root user (nextjs:1001)  
- Standalone output with optimized static assets and automatic output tracing
- Optimized layer caching with separate dependency and source copy steps

**Build Docker Image:**
```bash
docker build -t stfgit/mower-frontend .
```

**Run Container:**
```bash
docker run -p 3000:3000 stfgit/mower-frontend
```

**Security Context:**
- Runs as non-root user `nextjs` (UID 1001)
- Minimal Alpine Linux base image
- Optimized layer caching with multi-stage build

## Backend Integration

The frontend communicates with the MowItNow Java Spring Boot backend via REST API:

**API Base URL:** Configurable via environment variables
**Development:** `http://localhost:8080/api/mower`
**Production:** `http://mower-backend-service/api/mower`

**Key Endpoints:**
- `GET /api/mower/health` - Backend health check
- `POST /api/mower/execute` - Single mower command execution  
- `POST /api/mower/batch` - Multiple mower batch execution

**Mower Command Protocol:**
- `G` - Turn left (90° counterclockwise)
- `D` - Turn right (90° clockwise)
- `A` - Move forward one cell

**CORS Configuration:**
Backend accepts requests from:
- `http://localhost:3000` (Next.js dev server)
- `http://127.0.0.1:3000`

## Environment Configuration

**Environment Variables:**
- `NEXT_PUBLIC_API_BASE_URL` - Backend API base URL for client-side calls (exposed to browser)
- `API_BASE_URL` - Backend API base URL for server-side calls (default: http://localhost:8080)
- `NODE_ENV` - Environment mode (development/production)
- `PORT` - Server port (default: 3000)
- `HOSTNAME` - Server hostname (default: 0.0.0.0)

**API Configuration Details:**
- `MowerAPI` class uses static methods for API calls
- Error handling with HTTP status codes and descriptive messages  
- Environment-based URL resolution: `process.env.NEXT_PUBLIC_API_BASE_URL || 'http://localhost:8080'`
- Content-Type: application/json for all POST requests

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

1. **Start Backend First:** Ensure the MowItNow Java Spring Boot backend is running on port 8080
2. **Install Dependencies:** `npm install`
3. **Start Frontend:** `npm run dev` (uses Turbopack for faster builds than webpack)
4. **Access Application:** `http://localhost:3000` with automatic browser refresh
5. **Live Updates:** Hot module replacement via Turbopack for instant feedback
6. **Code Quality:** Run `npm run lint` during development for immediate feedback
7. **Pre-deployment:** Run `npm run build` to verify production build succeeds

## Harness DevSecOps Integration

**Pipeline Configuration:** `mower_frontend_cicd`
- **Template:** `account.npm_build` v0.2 with flexible workspace support
- **Input Set:** `dev.yaml` for development deployment
- **Repository:** `stfgit/mower-frontend` on GitHub
- **Project:** `Mower` (organization: `stf`)
- **Service:** `mower_frontend_service` with GitHub-based manifest fetching

**Pipeline Stages:**
1. **Source Code Scan** (`account.source_code_scan` v0.1)
   - OWASP dependency check
   - Gitleaks secret scanning
   - AquaTrivy vulnerability scan

2. **Build and Push** (`account.npm_build` v0.2)
   - Workspace validation with `WORKING_DIRECTORY: "."`
   - npm dependency installation with cache optimization
   - ESLint code quality checks
   - Next.js build with standalone output
   - npm security audit
   - Docker multi-stage build and push to `stfgit/mower-frontend`

3. **Image Security Scan** (`account.images_scan` v0.1)
   - Container vulnerability scanning with AquaTrivy

4. **Development Deployment** (`account.deploy_k8s` v0.1)
   - Kubernetes deployment to development environment
   - Service: `mower_frontend_service`
   - Artifact: Docker image with pipeline execution ID tag

5. **Production Approval** (`account.manual_approval` v0.1)
   - Manual approval by DevOps team
   - Minimum 1 approver required

6. **Production Deployment**
   - Kubernetes deployment to production environment

**Kubernetes Configuration:**
- **Namespace:** Configurable via `<+infra.namespace>`
- **Replicas:** 2 for high availability
- **Resources:** 256Mi-512Mi memory, 250m-500m CPU
- **Service:** ClusterIP with port 80→3000 mapping
- **Image:** `stfgit/mower-frontend` with pipeline execution ID tag