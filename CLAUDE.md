# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

TwoEyes - A multi-platform news aggregation and analysis system with AI-powered article grouping and real-time thread discussions.

### Repository Structure
```
Nest4/
├── backend/           # Backend services (TypeScript API + Supabase Edge Functions)
│   ├── supabase/     # Edge Functions (Deno runtime)
│   ├── ts-api/       # Node.js/Express API
│   └── scripts/      # Deployment and debug scripts
└── iOS/              # Native iOS application
    └── App/          # SwiftUI app (iOS 18.0+)
```

### Technology Stack
- **Backend**: Node.js/TypeScript, Deno (Supabase Edge Functions), PostgreSQL with pgvector
- **Frontend**: Swift/SwiftUI (iOS native)
- **AI/ML**: OpenAI embeddings for similarity search and content analysis
- **Infrastructure**: Supabase, Render.com, Docker, CloudFlare R2

## Common Development Commands

### Backend Development

**Start local backend services:**
```bash
cd backend

# Terminal 1 - Supabase services
./scripts/debug-supabase.sh

# Terminal 2 - TypeScript API
./scripts/debug-ts-api.sh
```

**Build and test TypeScript API:**
```bash
cd backend/ts-api
npm run build       # Compile TypeScript
npm run dev        # Run development server
docker-compose up  # Full stack with database
```

**Deploy backend services:**
```bash
cd backend

# Deploy Supabase functions (ALWAYS use script for safety)
./scripts/deploy-supabase.sh

# TypeScript API deploys automatically to Render on git push
```

### iOS Development

**Build and run iOS app:**
```bash
cd iOS/App
open App.xcodeproj  # Opens in Xcode
# Build and run using Xcode (Cmd+R)
```

**Note**: No automated tests exist yet. Testing is done manually.

## High-Level Architecture

### System Architecture
```
iOS App → TypeScript API → Supabase Edge Functions → PostgreSQL
   ↓           ↓                    ↓
Firebase    Playwright         OpenAI API
   ↓           ↓                    ↓
Analytics   Web Scraping      Embeddings
```

### Data Flow
1. **Content Discovery**: Web scraping via Playwright API
2. **Article Processing**: Extract content → Generate embeddings → Store in database
3. **Thread Management**: Group similar articles using vector similarity
4. **User Interaction**: Real-time comments and discussions on threads

### Key Features
- Multi-language support (Korean/English)
- AI-powered article similarity matching
- Real-time thread discussions
- Social authentication (Google/Apple)
- Push notifications for thread updates

## Service URLs

### Development
- Supabase Studio: http://localhost:54323
- Edge Functions: http://localhost:54321/functions/v1
- TypeScript API: http://localhost:3000

### Production
- Supabase Project: bgnymsxduwfrauidowxx.supabase.co
- TypeScript API: twoeyes-ts-api.onrender.com

## Database Schema

Key tables:
- `users`: User profiles linked to Supabase Auth
- `articles`: News articles with content and embeddings
- `threads`: Article groups with aggregated embeddings
- `comments`: User discussions on threads
- Vector indexes for similarity search on embedding columns

## API Endpoints

### TypeScript API
- `POST /api/scrape`: Web page scraping
- `POST /api/reader-mode`: Extract readable content
- `POST /api/add-articles`: Batch article processing

### Supabase Edge Functions
- `add-article`: Ingest and process new articles
- `article-to-thread`: Assign articles to threads
- `find-similar-threads`: Vector similarity search
- `generate-embedding`: Create OpenAI embeddings
- `chat-completion`: AI chat responses

## Critical Guidelines

### Deployment Safety
- **NEVER** use `supabase functions deploy` directly
- **ALWAYS** use `./scripts/deploy-supabase.sh` for safety checks
- **VERIFY** debug flags are disabled before production deployment

### Code Standards
- **ALL** code, comments, and documentation must be in English
- Database fields use snake_case, application code uses camelCase
- Strong TypeScript types throughout the codebase
- Minimal comments - only for complex logic

### Environment Management
When modifying environment variables, update:
- Config interfaces in code
- Environment documentation files
- .env.example templates
- Deployment scripts

### iOS Specific
- Swift Package Manager only (no CocoaPods)
- iOS 18.0+ minimum deployment target
- SwiftUI with MVVM architecture
- Automatic snake_case/camelCase conversion in API layer