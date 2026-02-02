# Changelog

All notable changes to **Little-Listener** will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.0.0] - 2025-01-24

### 🎉 **Little-Listener V1.0 - Official Release**

The first production-ready release featuring a unified V3 architecture with WebUI-first design, video pipeline support, and cross-platform compatibility.

### Added

#### Core Architecture
- **Unified V3 Engine**: Complete rewrite with `config.py` as central configuration source
- **Self-Contained Data Structure**: All user data organized in `data/` directory for portability
- **Smart Adaptive Transcription**: Language detection + dynamic prompt injection
  - 30-second optimized detection for English, Mandarin, Cantonese, Japanese
  - Anti-hallucination settings (VAD filtering, `condition_on_previous_text=False`)
- **Contact Intelligence System**: E.164 phone number normalization with SQLite database
  - Handles Chinese mobiles, landlines, international formats
  - Smart phone/timestamp collision detection

#### WebUI Interface (Streamlit)
- **Life OS (Page 1)**: Call recording management with contact intelligence
  - Upload and process call recordings
  - Automatic phone number extraction
  - Contact database integration
  - In-place AI summarization
- **Knowledge Base (Page 2)**: Video/learning content transcription
  - Video upload and processing
  - YouTube link support
  - Organized video transcript storage
- **Batch Tools (Page 3)**: Batch AI summarization for multiple files
- **Settings (Page 4)**: System configuration and management
  - Ollama integration setup
  - Custom prompt management
  - HuggingFace token configuration

#### Processing Pipeline
- **Multi-Format Support**: MP3, WAV, M4A, MP4, MKV, and more
- **Optional Speaker Diarization**: pyannote.audio integration with HuggingFace token
- **Video Pipeline**: FFmpeg-based audio extraction and processing
- **Error Isolation**: Skip logic prevents reprocessing on failure
- **Batch Processing**: Inbox-based drag-and-drop for bulk operations

#### AI Integration
- **Ollama Integration**: Local LLM for AI summaries
- **Multiple Prompt Templates**: Call_Summary, Tech_Video, Emotional_Analysis
- **Custom Prompt Management**: User-configurable prompt templates

### Changed
- Migrated from JSON to SQLite for contact database
- Consolidated 40+ legacy files into organized `archive/` directory
- Updated `.gitignore` with comprehensive privacy and security rules
- Streamlined documentation from 45+ files to essential guides

### Technical Details
- **Python Version**: 3.8+
- **Key Dependencies**: faster-whisper, streamlit, pyannote.audio, ollama
- **Database**: SQLite (contacts.db)
- **Configuration**: YAML + Environment variables
- **Logging**: Structured logging with rotation

---

## [0.5.0] - 2024 (Phase 3.1-3.2)

### 🌐 **V3 Architecture - Modular Redesign**

Transitional phase introducing modular architecture and WebUI components.

### Added
- Modular `core/` directory structure
  - `transcribe_engine.py`: Smart transcription with language detection
  - `contact_intelligence.py`: Phone parsing and contact management
- Multi-page Streamlit WebUI (`pages/` directory)
- `scripts/process_v3.py`: Clean processing pipeline
- YAML-based configuration management
- Vault concept (Inbox → Processing → Output)

### Changed
- Separated concerns into `core/`, `pages/`, `scripts/`
- Migrated from two-phase CLI to unified WebUI workflow
- Replaced JSON config with YAML + `config.py`

### Known Issues
- Legacy files still present in root directory (resolved in v1.0.0)
- Documentation sprawl with Phase 3.1/3.2 specific guides

---

## [0.3.0] - 2024 (Phase 2.1)

### 🔊 **V2.1 - Speaker Diarization**

Enhanced V2 with speaker identification capabilities.

### Added
- **Speaker Diarization**: pyannote.audio integration
  - Speaker segmentation and labeling
  - HuggingFace token-based authentication
- **Bulldozer Mode**: Aggressive batch processing (9_V2.1推土机模式.bat)
- Enhanced error logging and recovery

### Changed
- Improved memory management for large audio files
- Added GPU detection and optimization scripts

### Known Issues
- Memory overhead when running Whisper + Ollama simultaneously
- Complex setup process for diarization (HF tokens, GPU requirements)

---

## [0.2.0] - 2024 (Phase 2.0)

### 🎨 **V2.0 - WebUI Introduction**

First release with graphical user interface and AI summarization.

### Added
- **Streamlit WebUI**: Basic web interface (`app.py`, `app_settings.py`)
- **AI Summarization**: Ollama integration for intelligent summaries
  - Call summary templates
  - Emotional analysis
- **Contact Management**: JSON-based contact tracking
- **Two-Phase Pipeline**:
  - Phase 1: Batch transcription (`transcribe_v2.py`)
  - Phase 2: Batch summarization (`summarize_main.py`)
- Windows batch launchers (`.bat` files) for easier access

### Changed
- Separated transcription and summarization into distinct phases
- Added configuration management (`config_manager.py`)

### Technical Details
- Dependencies: whisper, streamlit, ollama
- Contact storage: JSON format
- Configuration: Python-based config files

---

## [0.1.0] - 2024 (V1.0 - CLI Era)

### ⚡ **V1.0 - Fast CLI Mode**

Initial release with pure command-line interface and simple batch processing.

### Added
- **Core Transcription**: Basic Whisper-based audio-to-text
  - `transcribe_main.py`: Batch transcription script
  - Support for MP3, WAV formats
- **Windows Batch Scripts**:
  - `1_暴力转写.bat`: Brute force transcription
  - `0_守夜监控.bat`: Night monitoring mode
  - `5_检测GPU.bat`: GPU detection
- Direct output to markdown files
- Simple directory structure (Input → Output)

### Technical Details
- **Strengths**: Fast, simple, no complex dependencies
- **Limitations**: No speaker diarization, no AI summaries, no WebUI
- **Target Users**: CLI-comfortable users, batch processing workflows

---

## Version Comparison

| Feature | V1.0 (CLI) | V2.0 (WebUI) | V2.1 (Diarization) | V3.0 (Unified) | **V1.0.0 (Release)** |
|---------|------------|--------------|---------------------|----------------|----------------------|
| **Transcription** | ✅ Basic | ✅ Enhanced | ✅ Enhanced | ✅ Smart Adaptive | ✅ **Production** |
| **Speaker ID** | ❌ | ❌ | ✅ Optional | ✅ Optional | ✅ **Optional** |
| **AI Summaries** | ❌ | ✅ Basic | ✅ Enhanced | ✅ Advanced | ✅ **Custom Templates** |
| **WebUI** | ❌ | ✅ Basic | ✅ Basic | ✅ Multi-page | ✅ **4-Page System** |
| **Contact Intelligence** | ❌ | ✅ JSON | ✅ JSON | ✅ SQLite | ✅ **E.164 + SQLite** |
| **Video Support** | ❌ | ❌ | ❌ | ✅ Experimental | ✅ **Production** |
| **Language Detection** | ❌ | ❌ | ❌ | ✅ Basic | ✅ **30s Optimized** |
| **Architecture** | Single Script | Two-Phase | Two-Phase | Modular | **Unified & Clean** |
| **Configuration** | Hardcoded | Python Config | Python Config | YAML + config.py | **YAML + Env Vars** |
| **Database** | None | JSON | JSON | SQLite | **SQLite** |

---

## Migration Guide

### From V2.x to V1.0.0

1. **Update Configuration**:
   ```bash
   # Old: config.json
   # New: config.yaml + environment variables
   cp config.example.yaml config.yaml
   ```

2. **Migrate Contacts Database**:
   ```bash
   # Automatic migration from JSON to SQLite
   python contacts_manager.py --migrate
   ```

3. **Update Directory Structure**:
   ```
   Old:                    New:
   input/       →          data/inbox/
   output/      →          data/transcripts/
   calls/       →          data/raw_media/calls/
   videos/      →          data/raw_media/videos/
   ```

4. **Replace Batch Scripts**:
   ```bash
   # Old: 1_暴力转写.bat, 2_智能总结.bat
   # New: Single WebUI launcher
   streamlit run app.py
   ```

5. **Update Dependencies**:
   ```bash
   pip install -r requirements.txt --upgrade
   ```

### From V1.0 (CLI) to V1.0.0

1. **Install Dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

2. **Setup Configuration**:
   ```bash
   # Create config.yaml from example
   cp config.example.yaml config.yaml

   # Set HuggingFace token (optional, for diarization)
   export HF_TOKEN="your_token_here"
   ```

3. **Launch WebUI**:
   ```bash
   streamlit run app.py
   ```

---

## Known Issues & Limitations

### Current (V1.0.0)
- **Speaker Diarization**: Requires HuggingFace token and significant GPU memory
- **Video Processing**: Large video files (>2GB) may require extended processing time
- **Ollama Dependency**: Local Ollama installation required for AI summaries
- **Windows Focus**: Batch scripts and some paths optimized for Windows (Mac/Linux support via Python)

### Planned Improvements
- [ ] Mac/Linux native launchers (.sh scripts)
- [ ] Cloud deployment option (Docker container)
- [ ] Multi-language UI (Chinese/English toggle)
- [ ] Real-time transcription mode
- [ ] Advanced speaker identification (voice profiles)
- [ ] Export to multiple formats (PDF, DOCX)

---

## Credits

**Little-Listener** is developed with assistance from:
- **Whisper** (OpenAI): Speech recognition engine
- **faster-whisper** (Guillaume Klein): Optimized Whisper implementation
- **pyannote.audio** (Hervé Bredin): Speaker diarization
- **Ollama**: Local LLM integration
- **Streamlit**: WebUI framework
- **Claude Code** (Anthropic): AI-assisted development

---

## License

This project is released under the MIT License. See `LICENSE` file for details.

---

**For detailed architecture information**, see:
- `DIRECTORY_STRUCTURE.md` - Self-contained architecture guide
- `PROJECT_MAP.md` - Code reference
- `README.md` - Quick start guide
