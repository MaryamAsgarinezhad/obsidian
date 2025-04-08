[WebSockets](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API) are a broadly supported API for realtime data transfer, and a great choice for connecting to the OpenAI Realtime API in ==server-to-server applications==. For browser and mobile clients, we recommend connecting via [WebRTC](https://platform.openai.com/docs/guides/realtime-webrtc). Follow this guide to connect to the Realtime API via WebSocket and start interacting with a Realtime model.

## Overview

In a server-to-server integration with Realtime, your backend system will connect via WebSocket directly to the Realtime API. You can use a [standard API key](https://platform.openai.com/settings/organization/api-keys) to authenticate this connection, since the token will only be available on your secure backend server.

![connect directly to realtime API](https://openaidevs.retool.com/api/file/464d4334-c467-4862-901b-d0c6847f003a)

```python
# example requires websocket-client library:
# pip install websocket-client

import os
import json
import websocket

OPENAI_API_KEY = os.environ.get("OPENAI_API_KEY")

url = "wss://api.openai.com/v1/realtime?model=gpt-4o-realtime-preview-2024-12-17"
headers = [
    "Authorization: Bearer " + OPENAI_API_KEY,
    "OpenAI-Beta: realtime=v1"
]

def on_open(ws):
    print("Connected to server.")

def on_message(ws, message):
    data = json.loads(message)
    print("Received event:", json.dumps(data, indent=2))

ws = websocket.WebSocketApp(
    url,
    header=headers,
    on_open=on_open,
    on_message=on_message,
)

ws.run_forever()
```

- `wss://`: This is the **WebSocket Secure** protocol, similar to `https://` but for WebSockets.
- `Authorization: Bearer <API_KEY>`: This is how authentication is handled. The API key is required to access OpenAI’s WebSocket API.
- `"OpenAI-Beta: realtime=v1"`: Specifies that the request is using OpenAI’s **real-time beta API**.


- This function executes when the WebSocket **successfully connects**.
- `ws`: Represents the WebSocket connection instance.


- This function is called **whenever a message** is received from the server.
- `message`: The raw JSON response from the OpenAI API.
- `json.loads(message)`: Converts the JSON **string** into a Python dictionary (`data`).
- `json.dumps(data, indent=2)`: Formats the JSON data into a readable string with **2-space indentation**.

**Creating and Running the WebSocket Client**

- `websocket.WebSocketApp(...)`: Creates a WebSocket **client instance**.
- `url`: The WebSocket server address.
- `header=headers`: Attaches the **authentication headers**.
- `on_open=on_open`: Registers the function to run **when the connection is established**.
- `on_message=on_message`: Registers the function to run **when a message is received**.

```python
`ws.run_forever()`
```
- This starts the WebSocket **event loop**, keeping the connection open **indefinitely**.
- The script will keep listening for new messages and handling events accordingly.


---

## A more complex example:

```python
def on_open(ws):
    print("WebSocket connection opened.")

    session_update_event = {
        "type": "session.update",
        "session": {
            "modalities": ["text", "audio"],
            "instructions": instructions,
            "voice": "alloy",
            "input_audio_format": "pcm16",
            "output_audio_format": "pcm16",
            "input_audio_transcription": {
                # "enabled": True,
                "model": "whisper-1"
            },
            "turn_detection": {
                "type": "server_vad",
                "threshold": 0.5,
                "prefix_padding_ms": 300,
                "silence_duration_ms": 200
            },
            "temperature": 0.6,
        }
    }
    ws.send(json.dumps(session_update_event))

    threading.Thread(target=send_audio_stream, args=(ws,)).start()
    threading.Thread(target=receive_messages, args=(ws,)).start()
```

####  Creating a `session.update` Event

This **JSON object** defines how the AI assistant should behave:

- `"type": "session.update"` → Updates the assistant session.
- `"modalities": ["text", "audio"]` → Supports both text and audio interactions.
- `"instructions": instructions` → Uses the **Persian-only** response rule defined earlier.
- `"voice": "alloy"` → Uses OpenAI’s **"Alloy"** voice model for speech responses.
- **Audio format settings**:
    - `"input_audio_format": "pcm16"` → Uses **16-bit PCM audio** as input.
    - `"output_audio_format": "pcm16"` → Outputs **16-bit PCM audio**.
- **Transcription model settings**:
    - `"input_audio_transcription": {"model": "whisper-1"}` → Uses OpenAI's `whisper-1` for **speech-to-text transcription**.
    - ` "enabled": True` → This setting is **commented out**, meaning the feature is disabled.
- **Turn detection settings** (for ==detecting when the user is speaking==):
    - `"type": "server_vad"` → Uses **Voice Activity Detection (VAD)** on the server.
    - `"threshold": 0.5` → Defines sensitivity of speech detection.
    - `"prefix_padding_ms": 300` → Adds **300ms** of audio before detected speech.
    - `"silence_duration_ms": 200` → Waits **200ms** of silence before assuming the user has finished speaking.
- `"temperature": 0.6` → Controls **response randomness** (lower means more predictable responses).

- Converts the `session_update_event` dictionary into a **JSON string**.
- Sends this **configuration** to the WebSocket server to initialize the session.


- **Starts two separate threads**:
    1. `send_audio_stream(ws)`: Likely **captures** audio from the user and sends it to the assistant.
    2. `receive_messages(ws)`: Likely **receives** messages from the assistant and processes them.
- `threading.Thread(...).start()` ensures both **audio input and response handling** run in parallel.

### **What is `session_update_event` for?**

The `session_update_event` is a ==**configuration message**== sent to OpenAI’s WebSocket server to **customize and update** the session settings. It tells the assistant **how to behave** in the current conversation.

### **Purpose of `session_update_event`**

When the WebSocket connection opens, this event:

1. **Defines the communication modes** → Text and Audio.
2. **Sets specific behavior rules** → Only answer in **Persian** and only about **Hamrah Aval (MCI)**.
3. **Configures voice and audio processing** → Specifies the **voice model**, **audio formats**, and **speech-to-text transcription**.
4. **Controls response randomness** → Using the **temperature** parameter.
5. **Enables turn detection** → To manage when the assistant should start and stop listening.

چرا alloy؟ مگه realtime نیس؟

---


```python
def on_message(ws, message):
    event = json.loads(message)
    print("Received event:", json.dumps(event, indent=2))

    event_type = event.get('type')

    if event_type == 'response.audio.delta':
        audio_delta = event.get('delta')
        if audio_delta:
            audio_data = base64.b64decode(audio_delta)
            play_audio(audio_data)
    elif event_type == 'response.text.delta':
        text_delta = event.get('delta', '')
        print(f"Assistant: {text_delta}", end='', flush=True)
    elif event_type == 'response.text.done':
        final_text = event.get('text', '')
        print(f"\nAssistant (final): {final_text}")
    elif event_type == 'error':
        error_message = event.get('error', {}).get('message', 'Unknown error')
        print(f"Error: {error_message}")
    else:
        pass
```

**Triggered whenever** the WebSocket receives a message ==from the assistant==.

- Extracts the event `"type"` from the received message.
- Different event types are handled separately.

**Handling Audio Response (`response.audio.delta`)**

- The assistant **sends** speech responses in chunks (**audio deltas**).
- `event.get('delta')`: Retrieves the **next chunk** of audio.
- `base64.b64decode(audio_delta)`: Decodes the **Base64** audio data.
- `play_audio(audio_data)`: Likely **plays the audio response** in real time.

**Handling Partial Text Responses (`response.text.delta`)**

- `response.text.delta`: The assistant sends partial text responses **as they are generated**.
- `event.get('delta', '')`: Extracts the **new portion** of the text.
- `print(f"Assistant: {text_delta}", end='', flush=True)`: Prints the response **without a newline**, allowing real-time updates.

**Handling Finalized Text Response (`response.text.done`)**

- `response.text.done`: Signals that the assistant has **completed** its text response.
- `event.get('text', '')`: Retrieves the **full** response.
- `print(f"\nAssistant (final): {final_text}")`: Prints the **final assistant response**.


-------

### Threads to interact with assistant:

```python
def audio_callback(indata, frames, time, status):
    if status:
        print(status)
    q.put(indata.copy())

def send_audio_stream(ws):
    """Capture audio from the microphone and send it to the API."""
    with sd.InputStream(samplerate=RATE, channels=CHANNELS, dtype='int16', callback=audio_callback):
        print("Recording... Press Ctrl+C to stop.")
        try:
            while True:
                data = q.get()
                audio_bytes = data.tobytes()
                base64_audio = base64.b64encode(audio_bytes).decode('utf-8')
                audio_event = {
                    "type": "input_audio_buffer.append",
                    "audio": base64_audio
                }
                ws.send(json.dumps(audio_event))
        except KeyboardInterrupt:
            print("Recording stopped.")
            pass

def receive_messages(ws):
    """Keep the WebSocket open to receive messages."""
    while True:
        time.sleep(1)

```
**1. `audio_callback`: Handling Incoming Audio Data**

- This is a **callback function** triggered whenever the microphone captures audio. 
- ==with sd.InputStream(samplerate=RATE, channels=CHcatANNELS, dtype='int16', callback=audio_callback):==
- **Parameters**:
    - `indata`: The audio input data.
    - `frames`: Number of frames in the audio buffer.
    - `time`: Timestamp of the audio frame.
    - `status`: Any status messages related to the audio stream.
- If `status` is not empty, it prints the message (useful for debugging).
- The audio data (`indata.copy()`) is added to the **queue (`q`)**, allowing another function to process it asynchronously.

**2. `send_audio_stream`: Capturing and Sending Audio**

- This function **records audio** from the microphone and **sends it** to OpenAI’s WebSocket API.
-  **Creates an event (`audio_event`)** with:
    - `"type": "input_audio_buffer.append"` → This tells OpenAI that **audio data** is being streamed.
    - `"audio": base64_audio` → Contains the **Base64-encoded** audio data.
- **Sends the event** to the WebSocket server (`ws.send(...)`).

**3. `receive_messages`: Keeping WebSocket Open**

- **Runs an infinite loop** to **keep the WebSocket open**. ==Action taken in this step are defined in on_message.==
- **`time.sleep(1)`** prevents the loop from consuming too much CPU.


### **Final Summary**

1. **`audio_callback`**: Captures microphone audio and **stores it in a queue**.
2. **`send_audio_stream`**:
    - Continuously **fetches audio** from the queue.
    - Converts it to **Base64** and **sends it** to OpenAI via WebSocket.
    - Stops recording when the user **presses Ctrl+C**.
3. **`receive_messages`**: Keeps the WebSocket **active** to receive responses


---

### **What is a Callback Function?**

A **callback function** is a function that is **passed as an argument** to another function and is **executed later**, usually when a specific event occurs.

---

