# AI Video Starter Kit - Claude Code Documentation

## Project Overview

This is an AI-powered video creation platform built with Next.js, Remotion, and fal.ai. It enables browser-native video processing with integrated AI models for generating images, videos, music, and voiceovers. The application uses IndexedDB for local storage, requiring no cloud database infrastructure.

## Architecture

### Core Technologies
- **Frontend**: Next.js 14 (React 18) with TypeScript
- **Video Processing**: Remotion 4.0 for browser-native video composition
- **AI Models**: fal.ai integration with multiple model endpoints
- **Storage**: IndexedDB (idb) for client-side data persistence
- **State Management**: Zustand for application state
- **File Upload**: UploadThing for media uploads
- **Sharing**: Vercel KV for share functionality
- **Styling**: Tailwind CSS with Radix UI components
- **Deployment**: Vercel

### Project Structure
```
src/
├── app/              # Next.js app router pages
├── components/       # React components (UI + feature components)
├── data/            # Data layer (IndexedDB schema, queries, mutations)
├── lib/             # Utility libraries (fal.ai client, ffmpeg, utils)
└── hooks/           # Custom React hooks
```

### Key Files
- `src/lib/fal.ts`: AI model endpoints configuration and fal.ai client setup
- `src/data/schema.ts`: TypeScript schemas for projects, tracks, keyframes, and media
- `src/data/db.ts`: IndexedDB database implementation
- `src/data/queries.ts`: Data fetching logic
- `src/data/mutations.ts`: Data modification operations

## Available AI Models

### Image Generation
- **Flux Dev**: General-purpose image generation
- **Flux Schnell**: Fast image generation
- **Flux Pro 1.1 Ultra**: Highest quality image generation
- **Stable Diffusion 3.5 Large**: Typography and complex prompts

### Video Generation
- **Minimax Video 01 Live**: Realistic motion and physics (supports image input)
- **Hunyuan Video**: High visual quality and motion diversity
- **Kling 1.5 Pro**: High-quality video (supports image input)
- **Kling 1.0 Standard**: High-quality video with camera controls
- **Luma Dream Machine 1.5**: High-quality video (supports image input)
- **Veo 2**: Realistic motion, up to 4K output
- **LTX Video v0.95**: Multi-conditioning video generation with frame selection

### Audio & Music
- **Minimax Music**: High-quality musical compositions
- **Stable Audio**: Music creation with Stable Diffusion
- **MMAudio V2**: Synchronized audio for videos

### Voiceover & TTS
- **PlayHT TTS v3**: Natural speech synthesis
- **PlayAI TTS Dialog**: Multi-speaker dialogues
- **F5 TTS**: Flow matching speech synthesis

### Video Enhancement
- **Topaz Video Upscale**: Professional video upscaling
- **sync.so Lipsync**: Realistic lipsync animations

## LLM Models & Providers Comparison

### Recommended LLM Providers for AI Video Applications

#### For Text-to-Video Prompts & Script Generation

| Provider | Model | Cost (per 1M tokens) | Best For | Context Window |
|----------|-------|---------------------|----------|----------------|
| **Anthropic** | Claude 3.5 Sonnet | $3 input / $15 output | Creative writing, detailed scene descriptions, storyboarding | 200K |
| **Anthropic** | Claude 3 Haiku | $0.25 input / $1.25 output | Fast prompt generation, simple descriptions | 200K |
| **OpenAI** | GPT-4o | $2.50 input / $10 output | Balanced performance, good for general prompts | 128K |
| **OpenAI** | GPT-4o mini | $0.15 input / $0.60 output | Budget-friendly, simple prompt generation | 128K |
| **Google** | Gemini 1.5 Pro | $1.25 input / $5 output | Long context, video analysis | 2M |
| **Google** | Gemini 1.5 Flash | $0.075 input / $0.30 output | Fast, cost-effective for batch processing | 1M |
| **Meta** | Llama 3.1 70B | Free (self-hosted) / ~$0.50 (hosted) | Open-source, customizable | 128K |

#### Cost Analysis Examples

**Scenario 1: Generate 100 video prompts (avg 150 tokens/prompt)**
- Claude 3.5 Sonnet: ~$0.05 input + ~$0.23 output = **$0.28**
- GPT-4o mini: ~$0.02 input + ~$0.09 output = **$0.11**
- Gemini 1.5 Flash: ~$0.01 input + ~$0.05 output = **$0.06**

**Scenario 2: Analyze 10 videos and generate scripts (avg 5K tokens input, 2K output)**
- Claude 3.5 Sonnet: $0.15 input + $0.30 output = **$0.45**
- GPT-4o: $0.13 input + $0.20 output = **$0.33**
- Gemini 1.5 Pro: $0.06 input + $0.10 output = **$0.16**

### Recommended Provider Strategy

1. **Development/Testing**: Use **Gemini 1.5 Flash** or **GPT-4o mini** for cost efficiency
2. **Production (High Quality)**: Use **Claude 3.5 Sonnet** for creative writing and complex scenes
3. **Production (Balanced)**: Use **GPT-4o** for general-purpose video descriptions
4. **Production (Volume)**: Use **Gemini 1.5 Flash** for batch processing many simple prompts
5. **Self-Hosted**: Use **Llama 3.1** for privacy and cost control at scale

### Integration Recommendations

```typescript
// Example: Multi-provider prompt enhancement
async function enhanceVideoPrompt(userPrompt: string) {
  // For creative enhancement: Claude 3.5 Sonnet
  const enhanced = await anthropic.messages.create({
    model: "claude-3-5-sonnet-20241022",
    messages: [{
      role: "user",
      content: `Enhance this video prompt with rich visual details: ${userPrompt}`
    }]
  });

  // For batch generation: Gemini Flash
  const variations = await gemini.generateContent({
    model: "gemini-1.5-flash",
    prompt: `Create 5 variations of this video prompt: ${userPrompt}`
  });
}
```

## AI Video Model Costs (fal.ai)

### Estimated Costs per Generation

| Category | Model | Approx. Cost | Duration/Output |
|----------|-------|--------------|-----------------|
| **Video** | Minimax Video 01 | ~$0.05-0.10 | ~6 seconds |
| **Video** | Hunyuan Video | ~$0.08-0.15 | ~5 seconds |
| **Video** | Kling 1.5 Pro | ~$0.10-0.20 | ~5-10 seconds |
| **Video** | Luma Dream Machine | ~$0.04-0.08 | ~5 seconds |
| **Video** | Veo 2 | ~$0.15-0.30 | ~8 seconds (4K) |
| **Video** | LTX Video | ~$0.03-0.05 | ~5 seconds |
| **Image** | Flux Pro 1.1 Ultra | ~$0.04-0.06 | 1 image |
| **Image** | Flux Dev | ~$0.02-0.03 | 1 image |
| **Image** | Flux Schnell | ~$0.003-0.005 | 1 image |
| **Image** | SD 3.5 Large | ~$0.035 | 1 image |
| **Audio** | Minimax Music | ~$0.02-0.04 | ~30 seconds |
| **Audio** | Stable Audio | ~$0.01-0.02 | ~30 seconds |
| **TTS** | PlayHT TTS v3 | ~$0.002-0.003 | per 100 chars |
| **TTS** | PlayAI Dialog | ~$0.003-0.005 | per 100 chars |

*Note: Costs are approximate and may vary. Check [fal.ai pricing](https://fal.ai/pricing) for current rates.*

## Use Cases

### 1. Content Creation
- **Social Media Videos**: Generate short-form videos for TikTok, Instagram Reels, YouTube Shorts
- **Marketing Content**: Create product demos, explainer videos, ads
- **Educational Content**: Lecture videos, tutorial content, course materials

### 2. Creative Projects
- **Music Videos**: Combine AI-generated visuals with music
- **Storytelling**: Create visual narratives from scripts
- **Art Projects**: Experimental video art and installations

### 3. Business Applications
- **Product Demos**: Automated product showcase videos
- **Real Estate**: Property tour videos from images
- **E-commerce**: Product videos with voiceovers
- **Corporate Training**: Automated training video creation

### 4. Prototyping
- **Film Pre-visualization**: Storyboard animations
- **Concept Presentations**: Quick concept videos for pitches
- **Animation Previews**: Test scenes before full production

### 5. Personalization
- **Personalized Messages**: Custom video greetings
- **User-Generated Content**: Enable users to create custom videos
- **Dynamic Content**: Generate videos based on user data

## Potential Expansions & Features

### High-Priority Enhancements

#### 1. Advanced Timeline Features
```typescript
// Features to add:
- Multi-layer timeline with video compositing
- Transitions between clips (fade, wipe, dissolve)
- Keyframe animation for effects
- Speed controls (slow-mo, time-lapse)
- Trim and split functionality
```

#### 2. AI-Powered Script-to-Video
```typescript
// Workflow:
1. User provides script or outline
2. LLM (Claude/GPT-4) breaks down into scenes
3. Generate prompts for each scene
4. Auto-generate images/videos for scenes
5. Add voiceover narration
6. Add background music
7. Compile into final video
```

#### 3. Template System
```typescript
// Pre-built templates:
- Social media templates (TikTok, Instagram, YouTube)
- Marketing templates (product launch, testimonial)
- Educational templates (tutorial, course intro)
- Event templates (wedding, birthday, corporate)
```

#### 4. Text & Captions
```typescript
// Add support for:
- Animated text overlays
- Auto-generated captions (speech-to-text)
- Subtitle tracks
- Typography presets
- Text animations (kinetic typography)
```

#### 5. Video Effects & Filters
```typescript
// Effect categories:
- Color grading presets
- Visual effects (blur, glow, vignette)
- Particle effects
- Green screen / chroma key
- Image stabilization
```

### AI Model Expansions

#### 6. Add More AI Models
```typescript
// Recommended additions:
- Runway Gen-3 Alpha (high-quality video)
- Pika Labs (creative effects)
- Stable Video Diffusion (open-source)
- AnimateDiff (character animation)
- CogVideo (Chinese open-source model)
- Sora (when available via API)
```

#### 7. Image-to-Video Enhancement
```typescript
// Features:
- Multiple images to video (slideshow with AI transitions)
- Face animation (animate portraits)
- Object animation (make static objects move)
- 3D parallax effects
```

#### 8. Voice Cloning
```typescript
// Add providers:
- ElevenLabs (premium voice cloning)
- Resemble.ai (custom voices)
- Descript Overdub (voice editing)
```

### Collaboration Features

#### 9. Multi-User Collaboration
```typescript
// Features needed:
- Real-time collaboration (WebSocket/Supabase Realtime)
- User authentication (Clerk, Auth0, Supabase Auth)
- Project sharing and permissions
- Comment system on timeline
- Version history
```

#### 10. Cloud Storage Integration
```typescript
// Add support for:
- AWS S3 for long-term storage
- Cloudflare R2 (cheaper alternative)
- Google Drive integration
- Dropbox integration
- Asset library management
```

### Export & Publishing

#### 11. Advanced Export Options
```typescript
// Export features:
- Multiple resolution presets (4K, 1080p, 720p, mobile)
- Format options (MP4, WebM, MOV, GIF)
- Compression settings
- Watermark support
- Batch export
```

#### 12. Direct Publishing
```typescript
// Integrate with:
- YouTube (direct upload via API)
- TikTok (via API)
- Instagram (via Meta Graph API)
- Twitter/X (video upload)
- LinkedIn (video posts)
```

### Analytics & Optimization

#### 13. Video Analytics
```typescript
// Track:
- Generation costs per project
- Model performance comparison
- User engagement metrics
- Export statistics
- Popular templates
```

#### 14. Cost Optimization
```typescript
// Features:
- Cost calculator before generation
- Budget limits per project
- Model recommendation based on requirements
- Caching of generated assets
- Batch processing discounts
```

### Advanced AI Features

#### 15. Video Understanding
```typescript
// Add capabilities:
- Video-to-text (scene description)
- Object detection in videos
- Face detection and tracking
- Action recognition
- Content moderation
```

#### 16. Style Transfer
```typescript
// Enable:
- Apply artistic styles to videos
- Maintain consistency across scenes
- Brand color palette application
- Reference image style matching
```

#### 17. Prompt Engineering Tools
```typescript
// Build:
- Prompt templates library
- Prompt enhancement (using LLM)
- Negative prompt suggestions
- Style keyword database
- Prompt version history
```

### Mobile & Accessibility

#### 18. Mobile App
```typescript
// Build native apps:
- React Native / Expo app
- Mobile-optimized timeline
- Offline editing capabilities
- Mobile-first templates
```

#### 19. Accessibility Features
```typescript
// Add:
- Screen reader support
- Keyboard shortcuts
- High contrast mode
- Auto-generated alt text for visuals
- Closed captions support
```

### Monetization Features

#### 20. Premium Features
```typescript
// Implement:
- Subscription tiers (free/pro/enterprise)
- Credit-based system for AI generations
- White-label options
- API access for developers
- Commercial use licensing
```

## Development Best Practices

### Local Development Setup
```bash
# Install dependencies
npm install

# Copy environment template
cp .env.example .env.local

# Add your API keys:
# - FAL_KEY from https://fal.ai/dashboard/keys
# - UPLOADTHING_TOKEN from https://uploadthing.com
# - KV_* tokens from https://upstash.com (optional, for sharing)

# Run development server
npm run dev
```

### Environment Variables
```bash
FAL_KEY=""                          # Required: fal.ai API key
UPLOADTHING_TOKEN=""                # Required for file uploads
KV_URL=""                           # Optional: for share functionality
KV_REST_API_READ_ONLY_TOKEN=""
KV_REST_API_TOKEN=""
KV_REST_API_URL=""
```

### Code Quality
```bash
# Format code
npm run format

# Lint code
npm run lint

# Build for production
npm run build
```

### Working with AI Models

#### Adding New Models
1. Add model configuration to `AVAILABLE_ENDPOINTS` in `src/lib/fal.ts`
2. Define the endpoint properties:
   - `endpointId`: fal.ai endpoint identifier
   - `label`: Display name
   - `description`: User-facing description
   - `category`: "image" | "video" | "music" | "voiceover"
   - `inputAsset`: Optional input requirements
   - `initialInput`: Default parameters

#### Example: Adding a New Video Model
```typescript
{
  endpointId: "fal-ai/new-video-model",
  label: "New Video Model",
  description: "Description of capabilities",
  cost: "$0.10 per generation",
  category: "video",
  inputAsset: ["image"],  // If it accepts image input
  initialInput: {
    // Default parameters
    duration: 5,
    fps: 24
  }
}
```

### Database Schema Modifications

The app uses IndexedDB with schemas defined in `src/data/schema.ts`:
- `VideoProject`: Project metadata
- `VideoTrack`: Timeline tracks
- `VideoKeyFrame`: Timeline keyframes
- `MediaItem`: Generated or uploaded media

To modify schemas:
1. Update types in `src/data/schema.ts`
2. Update database operations in `src/data/db.ts`
3. Update queries in `src/data/queries.ts`
4. Update mutations in `src/data/mutations.ts`

### Common Development Tasks

#### Adding a New UI Component
```bash
# Components use Radix UI primitives
# Located in: src/components/ui/
# Feature components: src/components/
```

#### Modifying the Timeline
- Timeline logic: `src/components/timeline/`
- Track management: `src/data/mutations.ts`
- Keyframe operations: `src/data/queries.ts`

#### Video Composition (Remotion)
- Remotion components: `src/app/remotion/`
- Video rendering: Uses `@remotion/player` for preview
- Export: Server-side rendering via Remotion Lambda (optional)

## Deployment

### Vercel Deployment (Recommended)
```bash
# Install Vercel CLI
npm i -g vercel

# Deploy
vercel

# Set environment variables in Vercel dashboard
# - FAL_KEY
# - UPLOADTHING_TOKEN
# - KV_* variables (if using share feature)
```

### Environment-Specific Configuration
- **Development**: Uses local IndexedDB, localStorage for fal.ai key
- **Production**: Same storage, but consider:
  - Implementing user authentication
  - Adding backend API for key management
  - Using Vercel KV for sharing

### Performance Optimization
1. **Code Splitting**: Already implemented via Next.js
2. **Image Optimization**: Use Next.js Image component
3. **Video Optimization**: Pre-process videos for web (H.264, MP4)
4. **Caching**: Implement service worker for offline support

## Security Considerations

### API Key Management
- **Current**: Client-side storage (localStorage)
- **Recommended for Production**:
  - Store keys server-side
  - Use environment variables
  - Implement user authentication
  - Proxy all AI requests through your backend

### Content Moderation
- Consider implementing content filtering
- Add NSFW detection for user uploads
- Implement rate limiting
- Monitor usage patterns

### User Data
- IndexedDB data stays local (no server sync by default)
- Implement data export functionality
- Add privacy policy for any user data collection
- Consider GDPR compliance if serving EU users

## Community & Support

- **Documentation**: [fal.ai docs](https://fal.ai/docs)
- **Discord**: Join fal.ai community
- **GitHub**: Report issues and contribute
- **Examples**: Check fal.ai example gallery

## License

MIT License - see LICENSE file for details

## Additional Resources

### Learning Resources
- [Next.js Documentation](https://nextjs.org/docs)
- [Remotion Documentation](https://remotion.dev/docs)
- [fal.ai Documentation](https://fal.ai/docs)
- [AI Video Generation Guide](https://fal.ai/models)

### Related Tools
- [Runway](https://runwayml.com) - Creative AI tools
- [Pika Labs](https://pika.art) - Video generation
- [Stability AI](https://stability.ai) - Open-source models
- [Replicate](https://replicate.com) - AI model hosting

---

## ROADMAP TASKLIST

> **🚨 CRITICAL RULE FOR ALL AI AGENTS:**
>
> **BEFORE starting any task:**
> 1. Read this entire ROADMAP TASKLIST section carefully
> 2. Understand what has been completed and what's in progress
> 3. Check for any related tasks or dependencies
> 4. Review the AGENTS.md file for development guidelines
>
> **AFTER completing any task:**
> 1. Update this section with comprehensive details
> 2. Mark your task status (Planned → In Progress → Completed)
> 3. Document all changes made (files, features, UI, functionality)
> 4. Link related PRs, issues, branches, and workflows
> 5. Update timestamps and agent information
> 6. Keep AGENTS.md roadmap in sync with this section
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
- `CLAUDE.md` (new, 574+ lines)

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
- Added same ROADMAP TASKLIST to CLAUDE.md for consistency

**Files Modified**:
- `AGENTS.md` (new, 600+ lines)
- `CLAUDE.md` (updated with ROADMAP section)

**Functional Changes**:
- No code changes, documentation only
- Established systematic approach for tracking all AI agent work
- Created mandatory workflow for agents to read/update roadmap
- Both files now have synchronized roadmap tracking

**Related Workflows**: None yet

---

#### ✅ DevContainer Setup with direnv and 1Password
- **Status**: 🟩 Completed
- **Branch**: `claude/init-repo-018MtUJYv6Cf9mrMZmqDVZNr`
- **PR**: _Pending creation_
- **Issue**: N/A
- **Completed**: 2025-11-19
- **Agent**: Claude (Sonnet 4.5)

**Changes Made**:
- Created complete DevContainer configuration for VS Code
- Implemented dual secrets management: direnv (local) + 1Password Connect (production)
- Integrated DigitalOcean CLI (doctl) for cloud operations
- Added Claude Code persistent authentication mount
- Configured Node.js 20 LTS with TypeScript, Biome, ESLint
- Created comprehensive README with setup instructions

**Files Modified**:
- `.devcontainer/devcontainer.json` (new, 90+ lines)
- `.devcontainer/docker-compose.yml` (new, 50+ lines)
- `.devcontainer/Dockerfile` (new, Node.js 20 + direnv + doctl)
- `.devcontainer/.env.devcontainer` (new, environment template)
- `.devcontainer/README.md` (new, 500+ lines documentation)
- `.envrc.example` (new, direnv template with 1Password integration)
- `.gitignore` (updated, added direnv and DevContainer secrets)

**Functional Changes**:
- Developers can now use containerized environment with VS Code
- Secrets managed securely via direnv (no hardcoded keys)
- Ready for 1Password Connect integration for team/production
- Auto-load environment variables on container start
- Persistent bash history and Claude authentication
- Pre-configured VS Code extensions for Next.js/TypeScript
- Port 3000 forwarded for Next.js dev server

**UI Changes**: None (infrastructure only)

**Related Workflows**:
- Future GitHub Actions can use 1Password Connect for CI/CD
- DevContainer enables consistent development environment across team

---

#### ✅ Claude Code Installation in DevContainer
- **Status**: 🟩 Completed
- **Branch**: `claude/init-repo-018MtUJYv6Cf9mrMZmqDVZNr`
- **PR**: _Pending creation_
- **Issue**: N/A
- **Completed**: 2025-11-19
- **Agent**: Claude (Sonnet 4.5)

**Changes Made**:
- Added Claude Code CLI installation to Dockerfile with fallback methods
- Updated README documentation with setup, usage, and troubleshooting
- Configured automatic authentication via mounted ~/.claude directory
- Added manual installation instructions for edge cases

**Files Modified**:
- `.devcontainer/Dockerfile` (updated, added Claude Code installation section)
- `.devcontainer/README.md` (updated, enhanced Claude Code Integration section)

**Functional Changes**:
- Claude Code CLI now available inside DevContainer
- Developers can use `claude` commands directly in container terminal
- Persistent authentication across container rebuilds
- Three installation methods with fallbacks (script, binary, manual)
- Full troubleshooting guide for common issues

**UI Changes**: None (infrastructure only)

**Related Workflows**:
- Claude Code can be used in CI/CD pipelines if needed
- Enables AI-assisted development directly from container

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
- Synchronized roadmap tracking between AGENTS.md and CLAUDE.md

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

**Last Updated**: 2025-11-17
**Version**: 1.1.0
