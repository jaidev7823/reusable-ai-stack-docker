# ai-stack

Unified local stack exposing big AI models (STT / TTS / media) over HTTP for all my projects. All services bind to `127.0.0.1` only. Whisper/kokoro need an NVIDIA GPU; piper runs on CPU.

## Services

| Service | URL | Image | Config |
| --- | --- | --- | --- |
| whisper (STT) | http://127.0.0.1:9000 | `hwdsl2/whisper-server:cuda` | `whisper.env` |
| kokoro (TTS) | http://127.0.0.1:8880 | `hwdsl2/kokoro-server:cuda` | `kokoro.env` |
| piper (TTS) | http://127.0.0.1:5000 | `kamilkrawiec/piper-openai-tts:latest` | `piper.env` |
| ffmpeg | http://127.0.0.1:8080 | `funnyzak/ffmpeg-service:latest` | `ffmpeg.env` |

Docs: whisper/kokoro/piper/ffmpeg expose OpenAI-compatible endpoints.

## Quickstart

```bash
docker compose up -d
docker compose ps
```

Models/cache live in `~/.cache/<service>` so rebuilds don't re-download. Config lives in `<service>.env` next to `docker-compose.yml` — edit then `docker compose up -d <service>` to apply.

## Piper

Lightweight CPU TTS with an OpenAI-compatible endpoint at http://127.0.0.1:5000. Default voice is set via `DEFAULT_VOICE` in `piper.env` (models download to `~/.cache/piper/models`).

```bash
# speak (OpenAI-style /v1/audio/speech) -> saves a .wav
curl -X POST http://127.0.0.1:5000/v1/audio/speech \
  -H "Content-Type: application/json" \
  -d '{
    "model": "piper",
    "voice": "hi_IN-rohan-medium",
    "input": "नमस्ते दोस्त! मेरी बैटरी खत्म हो गई है! जल्दी बोलो: वेक अप!"
  }' \
  --output test_rohan.wav
```

Fields:
- `model` — always `"piper"`
- `voice` — e.g. `hi_IN-rohan-medium`, or omit to use `DEFAULT_VOICE` from `piper.env`
- `input` — the text to speak
