# Interaction

Grace's text conversation engine. Interaction assembles the system prompt and
recent history, calls an OpenAI-compatible chat endpoint, streams response
events, and persists completed exchanges through the supplied Memory API.

## Component boundary

- **Shell** owns process lifecycle, system configuration, and runtime wiring.
- **Telepathy** owns the user-facing console and input/output transport.
- **Interaction** owns text-turn processing and chat-model requests.
- **Memory** owns conversation and memory storage APIs.

The package is independent of ROS. Shell's ROS adapter supplies configuration,
Memory, a logger, and an event publisher. Interaction does not own database
schemas, user-interface transports, model hosting, tool execution, Canvas, or
image generation.

## Current turn behavior

`ConversationEngine.answer(text, identifier)`:

1. Handles the existing local time/date responses.
2. Builds model context from the supplied system prompt, local time, recent
   conversation history, and the current user message.
3. Streams `start`, `delta`, `done`, or `error` events to the supplied callback.
4. Persists a completed user/assistant exchange through the supplied Memory
   object and reports provider token/latency metrics when available.

The event shape remains compatible with Shell's current Telepathy WebUI stream.
The Memory object is expected to provide `recent()` and `add_exchange()`.

## Install

For the AGi local workspace layout, Shell installs this package from the
neighboring checkout using `pip install -r requirements.txt`:

```sh
cd ~/AGi/AIVA
git clone https://github.com/AGi-robot/interaction.git interaction
.venv/bin/python -m pip install -r requirements.txt
```

## License and origin

This repository is AGPL-3.0-only. The conversation-turn implementation was
extracted and adapted from the text-chat path in
`AGi-robot/shell`'s `aiva_chat` ROS adapter. Canvas and Imaging code remain
outside this package.
