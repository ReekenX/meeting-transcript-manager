# Audio Transcription Suite for macOS

Real-time audio transcription tools for capturing and transcribing system audio (Zoom meetings, etc.) and microphone input on macOS.

## Features

- 🎙️ **Real-time transcription** using OpenAI Whisper
- 🔊 **System audio capture** (Zoom, Teams, Discord, etc.)
- 📝 **Multiple output formats** (console, text file)
- 🚀 **Auto-device detection** for virtual audio devices
- 💻 **No cloud dependencies** - runs entirely locally

## Quick Start

### Prerequisites

1. **Install Homebrew** (if not already installed):
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

2. **Install Python 3.8+**:
```bash
brew install python@3.11
```

3. **Install uv** (Python package manager):
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### System Audio Setup

To capture system audio (required for Zoom/Teams/etc.), you need a virtual audio device:

#### Option 1: BlackHole (Recommended - Free)

```bash
# Install BlackHole
brew install blackhole-2ch

# Configure audio routing
# 1. Open Audio MIDI Setup (/Applications/Utilities/)
# 2. Click '+' → 'Create Multi-Output Device'
# 3. Check both:
#    ✓ Built-in Output (to hear audio)
#    ✓ BlackHole 2ch (to capture audio)
# 4. Set Multi-Output Device as system output
```

#### Option 2: Background Music (Free with Volume Control)

```bash
# Install Background Music
brew install --cask background-music

# It automatically configures audio routing
# Bonus: Per-app volume control
```

## Usage

### 1. Basic Microphone Transcription

```bash
# Using uv (recommended)
uv run voice.py

# Or with Python directly
python3 voice.py
```

### 2. System Audio Transcription (Zoom/Teams)

```bash
# Auto-detect BlackHole device
uv run voice.py --auto-device

# Or manually specify device
uv run voice.py --list-devices  # Find BlackHole device number
uv run voice.py --device <number>
```

### 3. System Audio + Microphone

```bash
# Captures both system audio and your microphone
uv run voice_system_audio.py --auto-device
```

### 4. File Output

```bash
# Transcribe to file instead of console
uv run voice_alternative.py --output transcript.txt
```

## Available Scripts

| Script | Purpose | Best For |
|--------|---------|----------|
| `voice.py` | Main transcription tool with auto-device | General use, Zoom meetings |
| `voice_alternative.py` | File output focused | Long meetings, logs |
| `voice_system_audio.py` | System + Mic capture | Presentations, interviews |
| `simple_transcriber.py` | Minimal dependencies | Quick setup |
| `background_music_transcriber.py` | Background Music integration | Per-app volume control |
| `sox_transcriber.py` | Command-line based | When Python audio fails |

## Advanced Options

### Model Selection

```bash
# Larger models = better accuracy (but slower)
uv run voice.py --model large --auto-device

# Available models: tiny, base, small, medium, large
# Recommended: base (balanced) or small (quality)
```

### Performance Tuning

```bash
# Faster processing (lower quality)
uv run voice.py --beam-size 1 --best-of 1

# Higher quality (slower)
uv run voice.py --beam-size 10 --best-of 10

# Adjust chunk duration
uv run voice.py --duration 3  # Process every 3 seconds
```

### Output Configuration

```bash
# Custom output file
uv run voice_alternative.py --output meeting_notes.txt

# Different models for different scenarios
uv run voice.py --model tiny     # Quick notes
uv run voice.py --model medium   # Important meetings
```

## Troubleshooting

### Common Issues

#### 1. "Killed" or Immediate Crash

**Cause**: macOS security blocking audio access

**Solution**:
```bash
# Grant microphone permission
python3 audio_transcriber_fixed.py --request-permission

# Check System Settings > Privacy & Security > Microphone
# Add Terminal/iTerm to allowed apps
```

#### 2. No Audio Captured

**Cause**: Wrong audio device selected

**Solution**:
```bash
# List all devices
uv run voice.py --list-devices

# Use auto-detection
uv run voice.py --auto-device

# Check Audio MIDI Setup configuration
open /Applications/Utilities/Audio\ MIDI\ Setup.app
```

#### 3. Poor Transcription Quality

**Cause**: Model too small or noisy audio

**Solution**:
```bash
# Use larger model
uv run voice.py --model medium --auto-device

# Adjust audio processing
uv run voice.py --beam-size 10 --temperature 0.0
```

#### 4. Installation Issues

```bash
# If pip fails, try:
pip3 install --upgrade pip
pip3 install openai-whisper sounddevice numpy

# If pyaudio fails on Apple Silicon:
brew install portaudio
pip3 install --global-option='build_ext' --global-option='-I/opt/homebrew/include' --global-option='-L/opt/homebrew/lib' pyaudio
```

### Platform-Specific Notes

#### Apple Silicon (M1/M2/M3)

```bash
# Ensure using ARM64 Python
which python3  # Should show /opt/homebrew/bin/python3

# Install Rosetta if needed for some dependencies
softwareupdate --install-rosetta
```

#### Intel Macs

```bash
# Standard installation works
brew install python@3.11
pip3 install openai-whisper pyaudio numpy
```

## Best Practices

### For Zoom Meetings

1. **Setup before meeting**:
```bash
# Test audio capture
uv run voice.py --list-devices
uv run voice.py --auto-device --model base
```

2. **Configure Zoom audio**:
   - Zoom Settings → Audio → Speaker → Multi-Output Device
   - Keep "Original Sound" OFF for better quality

3. **Start transcription**:
```bash
uv run voice.py --auto-device --output zoom_transcript.txt
```

### For Long Sessions

1. **Use file output**:
```bash
uv run voice_alternative.py --output meeting_$(date +%Y%m%d_%H%M%S).txt
```

2. **Optimize for stability**:
```bash
# Smaller chunks, stable model
uv run voice.py --duration 3 --model base --temperature 0.0
```

### For Privacy

- All processing is **local** - no data sent to cloud
- Transcripts saved locally only
- Delete transcripts after use if sensitive

## Performance Benchmarks

| Model | Quality | Speed | RAM Usage | Recommended For |
|-------|---------|-------|-----------|-----------------|
| tiny | ★★☆☆☆ | 10x realtime | 1GB | Quick notes |
| base | ★★★☆☆ | 5x realtime | 1.5GB | General use |
| small | ★★★★☆ | 3x realtime | 2.5GB | Important meetings |
| medium | ★★★★★ | 2x realtime | 5GB | Critical accuracy |
| large | ★★★★★ | 1x realtime | 10GB | Post-processing |

## Contributing

Feel free to submit issues and enhancement requests!

## License

MIT License - Use freely for personal and commercial purposes.

## Acknowledgments

- [OpenAI Whisper](https://github.com/openai/whisper) for transcription models
- [BlackHole](https://github.com/ExistentialAudio/BlackHole) for virtual audio
- [Background Music](https://github.com/kyleneideck/BackgroundMusic) for audio routing

---

**Note**: For professional transcription needs, consider dedicated hardware solutions or cloud services for better accuracy and features.