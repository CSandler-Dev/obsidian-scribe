# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Obsidian Scribe is a plugin for Obsidian that records voice notes, transcribes audio using either OpenAI Whisper or AssemblyAI, and generates AI-powered summaries and insights. The plugin features an interactive modal interface, custom note templates, and multi-language support.

## Development Commands

### Build & Development
- `npm run dev` - Development build with file watching and hot reload support (uses esbuild with watch mode)
- `npm run build:prod` - Production build with minification and type checking
- `npm run version` - Bump version and update manifest files
- `npm run update-all-deps` - Update all dependencies to latest versions

### Code Quality
- **Linting/Formatting**: `npx biome check` (check issues) and `npx biome format` (format code)
- **TypeScript**: `tsc -noEmit -skipLibCheck` (type checking, included in prod build)
- Code style: 2-space indentation, single quotes, recommended Biome rules

### Development Setup
- Set `OBSIDIAN_PLUGINS_PATH` in `.env` for automatic plugin copying during development
- Hot reload supported via `.hotreload` file (requires hot-reload community plugin)

## Architecture Overview

### Plugin Structure
The plugin follows Obsidian's plugin architecture with the main entry point in `main.ts` that delegates to `src/index.ts`. The core plugin class (`ScribePlugin`) manages:

- **State Management**: Recording state, OpenAI client, and modal controls
- **Settings Management**: Comprehensive settings with React-based UI components
- **Recording Workflow**: Audio recording → Transcription → AI processing → Note creation

### Key Components

#### Audio Recording (`src/audioRecord/`)
- `AudioRecord` class handles browser MediaRecorder API
- Supports WebM and MP3 formats with client-side conversion
- Manages recording state (IDLE, RECORDING, PAUSED)

#### Modal Interface (`src/modal/`)
- `ScribeControlsModal` - Main recording control interface
- React components for recording buttons, timer, and options
- Modular option components for AI models, languages, and recording settings

#### Settings System (`src/settings/`)
- React-based settings interface with debounced auto-save
- Supports custom note templates with configurable sections
- Audio device selection and path configuration

#### Utility Functions (`src/util/`)
- `openAiUtils.ts` - OpenAI API integration for transcription and summarization
- `assemblyAiUtil.ts` - AssemblyAI integration for enhanced transcription
- `fileUtils.ts` - Obsidian vault operations (create notes, save audio, frontmatter)
- `textUtil.ts` - Text processing including Mermaid chart extraction

### Data Flow
1. User initiates recording via ribbon button or command
2. `AudioRecord` captures audio using MediaRecorder API
3. Audio blob converted to ArrayBuffer and saved to vault
4. Audio sent to transcription service (OpenAI/AssemblyAI)
5. Transcript processed by LLM for summarization and insights
6. Note created/updated with structured content using templates

### Key Patterns
- **Settings**: Use `ScribePluginSettings` interface with debounced saves via `saveSettings()`
- **File Operations**: Always use plugin's vault methods, handle path normalization with `normalizePath()`
- **Error Handling**: Show user notices for feedback, log errors to console
- **Templates**: Notes use configurable template sections with JSON key mapping
- **State Management**: Plugin maintains `ScribeState` with recording state, OpenAI client, and modal controls
- **React Components**: UI components use TypeScript with React JSX, following consistent prop patterns

## Important Notes

### API Dependencies
- **OpenAI API Key**: Required for transcription (Whisper) and summarization
- **AssemblyAI Key**: Optional but recommended for enhanced transcription accuracy

### File Naming
- Uses moment.js for date formatting in filenames
- Template patterns: `{{date}}` replaced with formatted dates
- Safe filename generation handles special characters

### Mobile Considerations
- Plugin designed for mobile use with interruption handling
- iOS requires screen to stay on during recording (Obsidian limitation)
- Progressive saving prevents data loss on connectivity issues

### Build System
- **esbuild**: Fast bundling with development watch mode and production minification
- **Target**: ES2018, CommonJS format for Obsidian compatibility  
- **External Dependencies**: Obsidian API, CodeMirror, and Node.js builtins are externalized
- **Assets**: CSS and manifest files copied to build directory automatically

### Key Dependencies
- **Audio Processing**: `@ffmpeg/ffmpeg` for format conversion, `@fix-webm-duration/fix` for WebM duration fixing
- **AI/ML**: `openai` client, `assemblyai` SDK, `langchain` for LLM operations
- **UI**: React 19+ with TypeScript, `mini-debounce` for settings auto-save
- **Validation**: `zod` for schema validation

## Development Resources

### Obsidian Developer Documentation
- Full Obsidian developer documentation is available locally at: `/Users/calebsandler/Code Repos/Utilities/Obsidian_Dev/obsidian-developer-docs/`
- Includes comprehensive API reference, plugin guides, UI components, and TypeScript definitions
- Use this resource for detailed API information, best practices, and implementation examples