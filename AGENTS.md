# AGENTS.md

This file provides context and instructions for AI coding agents working on the **AI Video Starter Kit** project.

## Project Overview

An AI-powered video creation platform built with Next.js, Remotion, and fal.ai. Enables browser-native video processing with integrated AI models for generating images, videos, music, and voiceovers.

**Tech Stack**: Next.js 14, TypeScript, Remotion 4.0, fal.ai, IndexedDB, Zustand, Tailwind CSS, Radix UI

## Dev Environment Tips

### Initial Setup

```bash
# Install dependencies
npm install

# Copy environment variables
cp .env.example .env.local

# Configure required API keys in .env.local:
# - FAL_KEY (required): Get from https://fal.ai/dashboard/keys
# - UPLOADTHING_TOKEN (required for uploads): Get from https://uploadthing.com
# - KV_* tokens (optional, for share feature): Get from https://upstash.com

# Start development server
npm run dev
```

### Project Structure Navigation

```bash
# Main application code
src/
├── app/              # Next.js app router (pages, API routes)
├── components/       # React components (UI + features)
├── data/            # Data layer (IndexedDB: schema, queries, mutations)
├── lib/             # Utilities (fal.ai client, ffmpeg, utils)
└── hooks/           # Custom React hooks

# Key files for AI model configuration
src/lib/fal.ts       # AI model endpoints and fal.ai client
src/data/schema.ts   # TypeScript schemas (projects, tracks, keyframes, media)
src/data/db.ts       # IndexedDB implementation
```

### Common Development Commands

```bash
# Development
npm run dev          # Start dev server at http://localhost:3000

# Code Quality
npm run format       # Format code with Biome
npm run lint         # Lint code with Next.js ESLint

# Production
npm run build        # Build for production
npm run start        # Start production server
```

### Adding New AI Models

1. Open `src/lib/fal.ts`
2. Add configuration to `AVAILABLE_ENDPOINTS` array:
   ```typescript
   {
     endpointId: "fal-ai/model-name",
     label: "Display Name",
     description: "Model description",
     cost: "Approximate cost",
     category: "image" | "video" | "music" | "voiceover",
     inputAsset?: ["image" | "video" | "audio"],  // Optional
     initialInput?: { /* default params */ }      // Optional
   }
   ```

### Database Schema Changes

When modifying data structures:
1. Update types in `src/data/schema.ts`
2. Update operations in `src/data/db.ts`
3. Update queries in `src/data/queries.ts`
4. Update mutations in `src/data/mutations.ts`

### Environment Variables

Required:
- `FAL_KEY`: fal.ai API authentication
- `UPLOADTHING_TOKEN`: File upload service

Optional (for sharing feature):
- `KV_URL`, `KV_REST_API_READ_ONLY_TOKEN`, `KV_REST_API_TOKEN`, `KV_REST_API_URL`

## Testing Instructions

### Pre-commit Checks

```bash
# Format check and auto-fix
npm run format

# Lint check
npm run lint

# Type checking
npx tsc --noEmit

# Build verification
npm run build
```

### Husky Hooks

The project uses Husky for pre-commit hooks:
- Code formatting is automatically applied
- Ensure all checks pass before committing

### Manual Testing Checklist

- [ ] Video generation works with at least one AI model
- [ ] Timeline functionality (add/remove/move keyframes)
- [ ] Media upload works correctly
- [ ] Audio tracks play and sync properly
- [ ] Export/preview functionality works
- [ ] Share feature (if KV configured)

### CI/CD

Currently no automated CI/CD. When adding:
- Use GitHub Actions workflows in `.github/workflows/`
- Run: `npm run build`, `npm run lint`, `npx tsc --noEmit`
- Test on Node.js 18+ and 20+

## PR Instructions

### Before Creating a PR

1. **Run all checks**:
   ```bash
   npm run format && npm run lint && npm run build
   ```

2. **Test functionality**: Verify changes work locally

3. **Update documentation**: If adding features, update CLAUDE.md and this file

4. **Check ROADMAP TASKLIST**: Update the roadmap section below with your changes

### PR Title Format

Use conventional commits format:
```
<type>(<scope>): <description>

Types:
- feat: New feature
- fix: Bug fix
- docs: Documentation changes
- style: Code style changes (formatting)
- refactor: Code refactoring
- perf: Performance improvements
- test: Adding tests
- chore: Maintenance tasks
- build: Build system changes
- ci: CI/CD changes

Examples:
feat(video): add Runway Gen-3 model support
fix(timeline): resolve keyframe overlap issue
docs(readme): update installation instructions
refactor(db): optimize IndexedDB queries
```

### PR Description Template

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] New feature
- [ ] Bug fix
- [ ] Breaking change
- [ ] Documentation update

## Changes Made
- List key changes
- Include file paths when relevant

## Testing
- How was this tested?
- Which AI models were tested?

## Screenshots (if UI changes)
[Add screenshots]

## Roadmap Update
- [ ] Updated ROADMAP TASKLIST section
```

### Code Quality Requirements

- All code must be TypeScript
- Follow existing code style (enforced by Biome)
- No TypeScript errors (`npx tsc --noEmit` must pass)
- Build must succeed (`npm run build`)
- Preferably add JSDoc comments for complex functions

### Review Checklist

- [ ] Code follows project conventions
- [ ] No console errors in development
- [ ] Responsive design (if UI changes)
- [ ] Accessibility considerations (ARIA labels, keyboard navigation)
- [ ] No API keys or secrets in code
- [ ] Performance impact considered (especially for video processing)

## Branch Naming Convention

```
<type>/<description>

Examples:
feature/add-runway-gen3
fix/timeline-keyframe-overlap
docs/update-agents-md
refactor/optimize-db-queries
```

## Project-Specific Guidelines

### AI Model Integration

When adding new AI models:
- Check fal.ai documentation for API specifications
- Add to `AVAILABLE_ENDPOINTS` in `src/lib/fal.ts`
- Consider cost implications (document in CLAUDE.md)
- Test with various inputs
- Handle errors gracefully

### Video Processing

- Use Remotion for all video composition
- Test with different aspect ratios (16:9, 9:16, 1:1)
- Consider memory constraints for long videos
- Optimize for browser performance

### UI/UX Considerations

- Use Radix UI components for consistency
- Follow Tailwind CSS utility-first approach
- Ensure dark mode compatibility
- Mobile-responsive design required

### Performance

- Minimize bundle size (check with `npm run build`)
- Lazy load heavy components
- Use React.memo for expensive renders
- IndexedDB queries should be optimized

---

## ROADMAP TASKLIST

> **🚨 CRITICAL RULE FOR ALL AI AGENTS:**
>
> **BEFORE starting any task:**
> 1. Read this entire ROADMAP TASKLIST section carefully
> 2. Understand what has been completed and what's in progress
> 3. Check for any related tasks or dependencies
>
> **AFTER completing any task:**
> 1. Update this section with comprehensive details
> 2. Mark your task status (Planned → In Progress → Completed)
> 3. Document all changes made (files, features, UI, functionality)
> 4. Link related PRs, issues, branches, and workflows
> 5. Update timestamps and agent information
>
> This ensures all agents have complete context and prevents duplicate work.

### Task Status Legend
- 🟦 **Planned**: Task identified, not started
- 🟨 **In Progress**: Currently being worked on
- 🟩 **Completed**: Finished and merged
- 🟥 **Blocked**: Waiting on dependencies or decisions

---

### Completed Tasks

#### ✅ Initial Repository Setup
- **Status**: 🟩 Completed
- **Branch**: `claude/init-repo-018MtUJYv6Cf9mrMZmqDVZNr`
- **PR**: _Pending creation_
- **Issue**: N/A
- **Completed**: 2025-11-17
- **Agent**: Claude (Sonnet 4.5)

**Changes Made**:
- Created `CLAUDE.md` with comprehensive project documentation
- Added LLM providers comparison (Claude, GPT-4, Gemini) with cost analysis
- Documented 15+ AI models available in the platform
- Listed 5 major use case categories
- Detailed 20+ potential expansion features
- Included development best practices and security considerations

**Files Modified**:
- `CLAUDE.md` (new, 574 lines)

**Functional Changes**:
- No code changes, documentation only
- Established foundation for AI agent collaboration

**Related Workflows**: None yet

---

#### ✅ AGENTS.md Standard Implementation
- **Status**: 🟩 Completed
- **Branch**: `claude/init-repo-018MtUJYv6Cf9mrMZmqDVZNr`
- **PR**: _Pending creation_
- **Issue**: N/A
- **Completed**: 2025-11-17
- **Agent**: Claude (Sonnet 4.5)

**Changes Made**:
- Created `AGENTS.md` following OpenAI agents.md standard
- Added Dev Environment Tips (setup, navigation, common commands)
- Documented Testing Instructions (pre-commit, Husky, manual testing)
- Defined PR Instructions (title format, description template, review checklist)
- Established Branch Naming Convention
- Created ROADMAP TASKLIST system for AI agent coordination

**Files Modified**:
- `AGENTS.md` (new)
- `CLAUDE.md` (will add ROADMAP section)

**Functional Changes**:
- No code changes, documentation only
- Established systematic approach for tracking all AI agent work
- Created mandatory workflow for agents to read/update roadmap

**Related Workflows**: None yet

---

### In Progress Tasks

_No tasks currently in progress_

---

### Planned Tasks

#### 🟦 Add GitHub Actions CI/CD Pipeline
- **Status**: 🟦 Planned
- **Priority**: High
- **Estimated Effort**: Medium

**Scope**:
- Create `.github/workflows/ci.yml`
- Run tests on PR and push to main
- Include: build, lint, type-check
- Test on Node 18 and 20
- Add build status badge to README

**Files to Create/Modify**:
- `.github/workflows/ci.yml` (new)
- `README.md` (add badge)

---

#### 🟦 Implement Advanced Timeline Features
- **Status**: 🟦 Planned
- **Priority**: High
- **Estimated Effort**: Large

**Scope**:
- Multi-layer timeline with video compositing
- Transitions between clips (fade, wipe, dissolve)
- Keyframe animation for effects
- Speed controls (slow-mo, time-lapse)
- Trim and split functionality

**Files to Create/Modify**:
- `src/components/timeline/*` (multiple files)
- `src/data/schema.ts` (add transition types)
- `src/data/mutations.ts` (add transition operations)

**UI Changes**: Major timeline UI overhaul

---

#### 🟦 Add AI-Powered Script-to-Video
- **Status**: 🟦 Planned
- **Priority**: High
- **Estimated Effort**: Large

**Scope**:
1. User provides script or outline
2. LLM (Claude/GPT-4) breaks down into scenes
3. Generate prompts for each scene
4. Auto-generate images/videos for scenes
5. Add voiceover narration
6. Add background music
7. Compile into final video

**Files to Create/Modify**:
- `src/lib/script-to-video.ts` (new)
- `src/components/script-editor.tsx` (new)
- `src/app/api/script-analyze/route.ts` (new)
- Add LLM API integration (Anthropic/OpenAI)

**New Dependencies**:
- `@anthropic-ai/sdk` or `openai`

**Functional Changes**: Major new feature - automated video generation from scripts

---

#### 🟦 Template System Implementation
- **Status**: 🟦 Planned
- **Priority**: Medium
- **Estimated Effort**: Medium

**Scope**:
- Pre-built templates for common use cases
- Social media templates (TikTok, Instagram, YouTube)
- Marketing templates (product launch, testimonial)
- Educational templates (tutorial, course intro)
- Template gallery UI

**Files to Create/Modify**:
- `src/data/templates.ts` (new)
- `src/components/template-gallery.tsx` (new)
- `src/data/schema.ts` (add template type)

---

#### 🟦 Add Text & Captions Support
- **Status**: 🟦 Planned
- **Priority**: Medium
- **Estimated Effort**: Medium

**Scope**:
- Animated text overlays
- Auto-generated captions (speech-to-text)
- Subtitle tracks
- Typography presets
- Text animations (kinetic typography)

**Files to Create/Modify**:
- `src/components/text-overlay.tsx` (new)
- `src/lib/speech-to-text.ts` (new)
- `src/app/remotion/TextOverlay.tsx` (new)
- `src/data/schema.ts` (add text track type)

**New Dependencies**:
- Speech-to-text API integration

---

#### 🟦 Video Effects & Filters
- **Status**: 🟦 Planned
- **Priority**: Medium
- **Estimated Effort**: Medium

**Scope**:
- Color grading presets
- Visual effects (blur, glow, vignette)
- Particle effects
- Green screen / chroma key
- Image stabilization

**Files to Create/Modify**:
- `src/components/effects-panel.tsx` (new)
- `src/lib/video-effects.ts` (new)
- `src/app/remotion/Effects.tsx` (new)

---

#### 🟦 Add More AI Models
- **Status**: 🟦 Planned
- **Priority**: Medium
- **Estimated Effort**: Small per model

**Models to Add**:
- Runway Gen-3 Alpha (high-quality video)
- Pika Labs (creative effects)
- Stable Video Diffusion (open-source)
- AnimateDiff (character animation)
- Sora (when available via API)

**Files to Modify**:
- `src/lib/fal.ts` (add to AVAILABLE_ENDPOINTS)
- `CLAUDE.md` (document new models)

---

#### 🟦 Voice Cloning Integration
- **Status**: 🟦 Planned
- **Priority**: Low
- **Estimated Effort**: Medium

**Scope**:
- ElevenLabs integration
- Resemble.ai integration
- Descript Overdub integration
- Voice sample upload UI

**Files to Create/Modify**:
- `src/lib/voice-cloning.ts` (new)
- `src/components/voice-clone-panel.tsx` (new)

**New Dependencies**:
- Provider SDKs

---

#### 🟦 Multi-User Collaboration
- **Status**: 🟦 Planned
- **Priority**: Low
- **Estimated Effort**: Very Large

**Scope**:
- Real-time collaboration (WebSocket/Supabase Realtime)
- User authentication (Clerk, Auth0, Supabase Auth)
- Project sharing and permissions
- Comment system on timeline
- Version history

**Files to Create/Modify**:
- Major architectural changes
- Database migration from IndexedDB to cloud DB
- Auth system implementation
- WebSocket server setup

**Breaking Changes**: Yes - requires backend infrastructure

---

#### 🟦 Advanced Export Options
- **Status**: 🟦 Planned
- **Priority**: Medium
- **Estimated Effort**: Medium

**Scope**:
- Multiple resolution presets (4K, 1080p, 720p, mobile)
- Format options (MP4, WebM, MOV, GIF)
- Compression settings
- Watermark support
- Batch export

**Files to Create/Modify**:
- `src/components/export-panel.tsx` (enhance)
- `src/lib/video-export.ts` (new)
- `src/app/api/export/route.ts` (new)

---

#### 🟦 Direct Social Media Publishing
- **Status**: 🟦 Planned
- **Priority**: Low
- **Estimated Effort**: Large

**Scope**:
- YouTube (direct upload via API)
- TikTok (via API)
- Instagram (via Meta Graph API)
- Twitter/X (video upload)
- LinkedIn (video posts)

**Files to Create/Modify**:
- `src/lib/social-publishers/*.ts` (new)
- `src/components/publish-panel.tsx` (new)
- API integrations for each platform

**New Dependencies**:
- Platform SDKs
- OAuth handling

---

### Blocked Tasks

_No blocked tasks currently_

---

### Notes & Decisions

**2025-11-17**:
- Decided to follow OpenAI agents.md standard for AGENTS.md
- Established ROADMAP TASKLIST as mandatory workflow for all AI agents
- Using conventional commits format for PR titles

**Architecture Decisions**:
- Stick with IndexedDB for now (multi-user would require DB migration)
- Prioritize single-user experience before collaboration features
- Focus on AI model variety before advanced editing features

**Cost Considerations**:
- Document all AI model costs in CLAUDE.md
- Consider cost calculator feature (Planned task above)
- Monitor usage to recommend cost-effective models

---

### How to Update This Roadmap

**For Completed Tasks**:
```markdown
#### ✅ Task Name
- **Status**: 🟩 Completed
- **Branch**: branch-name
- **PR**: #123 or URL
- **Issue**: #456 or URL
- **Completed**: YYYY-MM-DD
- **Agent**: Agent name/type

**Changes Made**:
- Detailed list of changes

**Files Modified**:
- List all files changed (with line count if significant)

**Functional Changes**:
- How does this change the app?
- New features, refactors, bug fixes, etc.

**UI Changes** (if applicable):
- Screenshot or description

**Related Workflows**:
- Link to GitHub Actions, scripts, etc.
```

**For New Planned Tasks**:
```markdown
#### 🟦 Task Name
- **Status**: 🟦 Planned
- **Priority**: High/Medium/Low
- **Estimated Effort**: Small/Medium/Large/Very Large

**Scope**:
- What needs to be done

**Files to Create/Modify**:
- List expected files

**Dependencies** (if any):
- What's needed first

**Breaking Changes**: Yes/No
```

---

**Last Updated**: 2025-11-17 by Claude (Sonnet 4.5)
