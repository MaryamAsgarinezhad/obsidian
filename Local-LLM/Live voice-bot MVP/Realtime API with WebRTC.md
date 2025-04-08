[WebRTC](https://webrtc.org/) is a powerful set of standard interfaces for building real-time applications. The OpenAI Realtime API supports connecting to realtime models through a WebRTC peer connection. Follow this guide to learn how to configure a WebRTC connection to the Realtime API.

- WebRTC is a free and open-source project providing web browsers and mobile applications with real-time communication via application programming interfaces.

## Overview

In scenarios where you would like to connect to a Realtime model from an ==insecure client== over the network (like a web browser), we recommend using the WebRTC connection method. WebRTC is better equipped to handle ==variable connection states==, and provides a number of convenient APIs for capturing user audio inputs and playing remote audio streams from the model.

Connecting to the **Realtime API from the browser should be done with an ephemeral API key**, [generated via the OpenAI REST API](https://platform.openai.com/docs/api-reference/realtime-sessions). The process for initializing a WebRTC connection is as follows (assuming a web browser client):

![[Pasted image 20250310155505.png]]

----

The following example shows how to initialize a [WebRTC session](https://webrtc.org/getting-started/overview) (including the data channel to send and receive Realtime API events). It assumes you have already fetched an ephemeral API token (example server code for this can be found in the [next section](https://platform.openai.com/docs/guides/realtime-webrtc#creating-an-ephemeral-token)).

```javascript
async function init() {
  // Get an ephemeral key from your server - see server code below
  const tokenResponse = await fetch("/session");
  const data = await tokenResponse.json();
  const EPHEMERAL_KEY = data.client_secret.value;

  // Create a peer connection
  const pc = new RTCPeerConnection();

  // Set up to play remote audio from the model
  const audioEl = document.createElement("audio");
  audioEl.autoplay = true;
  pc.ontrack = e => audioEl.srcObject = e.streams[0];

  // Add local audio track for microphone input in the browser
  const ms = await navigator.mediaDevices.getUserMedia({
    audio: true
  });
  pc.addTrack(ms.getTracks()[0]);

  // Set up data channel for sending and receiving events
  const dc = pc.createDataChannel("oai-events");
  dc.addEventListener("message", (e) => {
    // Realtime server events appear here!
    console.log(e);
  });

  // Start the session using the Session Description Protocol (SDP)
  const offer = await pc.createOffer();
  await pc.setLocalDescription(offer);

  const baseUrl = "https://api.openai.com/v1/realtime";
  const model = "gpt-4o-realtime-preview-2024-12-17";
  const sdpResponse = await fetch(`${baseUrl}?model=${model}`, {
    method: "POST",
    body: offer.sdp,
    headers: {
      Authorization: `Bearer ${EPHEMERAL_KEY}`,
      "Content-Type": "application/sdp"
    },
  });

  const answer = {
    type: "answer",
    sdp: await sdpResponse.text(),
  };
  await pc.setRemoteDescription(answer);
}

init();
```

- Sends a `GET` request to `"/session"` (a backend API endpoint) to **retrieve an ephemeral key**.
- Parses the response as **JSON**.
- Extracts `client_secret.value` from the response and stores it in `EPHEMERAL_KEY`.
- The **ephemeral key** is a **temporary authentication token** used for secure API access.

- Creates a **new WebRTC peer connection** using `RTCPeerConnection()`.
- This object manages **real-time audio/video communication**.

- Creates an **`<audio>` HTML element** for playing **remote audio** received via WebRTC.
- Enables `autoplay` so the audio plays as soon as it’s received.
- **`pc.ontrack` event**:
    - Triggered when a remote media track (e.g., AI-generated audio) is received.
    - The received audio stream (`e.streams[0]`) is set as the **audio element’s source**.

- Uses **WebRTC’s `getUserMedia()`** API to **request access to the user’s microphone**.
- `ms` stores the user's **microphone stream**.
- `addTrack()` adds the microphone’s **audio track** to the peer connection (`pc`), enabling **two-way audio**.

- Creates a **WebRTC data channel** (`"oai-events"`) for sending and receiving **non-media data** (e.g., messages, events).
- Listens for incoming messages and logs them.

- Creates an **SDP (Session Description Protocol) offer**, which describes this peer’s media capabilities (audio, data channels).
- Sets the **local SDP** for this WebRTC peer using `setLocalDescription(offer)`.
- This initiates a WebRTC connection by sending the offer to the remote peer.

- Defines the OpenAI **real-time API URL** and specifies the model (`gpt-4o-realtime-preview-2024-12-17`).
- Sends a **POST request** containing the **SDP offer** (`offer.sdp`) to OpenAI.
- Includes the **ephemeral key** in the `Authorization` header for authentication.
- The API responds with an **SDP answer** from the AI.

- Extracts the **SDP answer** from OpenAI’s response.
- Sets it as the **remote description** (`setRemoteDescription(answer)`) to establish a WebRTC connection.

Calls `init()`, starting the whole process.

---

### **What This Code Does**

1. **Gets an ephemeral key** from the server.
2. **Creates a WebRTC peer connection** (`pc`).
3. **Captures local microphone audio**.
4. **Sets up an audio player for AI-generated voice**.
5. **Establishes a WebRTC data channel** for event messages.
6. **Generates an SDP offer** for a WebRTC session.
7. **Sends the SDP offer to OpenAI’s real-time API**.
8. **Receives an SDP answer from OpenAI** and finalizes the connection.
9. **Starts exchanging real-time audio** between the user and the AI.

---

## Creating an ephemeral token

To create an ephemeral token to use on the client-side, you will need to build a small server-side application (or integrate with an existing one) to make an [OpenAI REST API](https://platform.openai.com/docs/api-reference/realtime-sessions) request for an ephemeral key. You will use a [standard API key](https://platform.openai.com/settings/organization/api-keys) to authenticate this request on your backend server.

Below is an example of a simple Node.js [express](https://expressjs.com/) server which mints an ephemeral API key using the REST API:

```javascript
import express from "express";

const app = express();

// An endpoint which would work with the client code above - it returns
// the contents of a REST API request to this protected endpoint
app.get("/session", async (req, res) => {
  const r = await fetch("https://api.openai.com/v1/realtime/sessions", {
    method: "POST",
    headers: {
      "Authorization": `Bearer ${process.env.OPENAI_API_KEY}`,
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      model: "gpt-4o-realtime-preview-2024-12-17",
      voice: "verse",
    }),
  });
  const data = await r.json();

  // Send back the JSON we received from the OpenAI REST API
  res.send(data);
});

app.listen(3000);
```

---

## Sending and receiving events

To interact with the Realtime models, you will send and receive messages over the WebRTC data channel, and send and receive audio over media streams with the Realtime API as a connected peer. The full list of messages that clients can send, and that will be sent from the server, are found in the [API reference](https://platform.openai.com/docs/api-reference/realtime-client-events). Once connected, you'll send and receive events which represent text, audio, function calls, interruptions, configuration updates, and more.

Here is how you can send and receive events over the data channel:

```javascript
// Create a data channel from a peer connection
const dc = pc.createDataChannel("oai-events");

// Listen for server-sent events on the data channel - event data 
// will need to be parsed from a JSON string
dc.addEventListener("message", (e) => {
  const realtimeEvent = JSON.parse(e.data);
  console.log(realtimeEvent);
});

// Send client events by serializing a valid client event to
// JSON, and sending it over the data channel
const responseCreate = {
  type: "response.create",
  response: {
    modalities: ["text"],
    instructions: "Write a haiku about code",
  },
};
dc.send(JSON.stringify(responseCreate));
```
