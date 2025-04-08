
Once you have connected to the Realtime API through either [WebRTC](https://platform.openai.com/docs/guides/realtime-webrtc) or [WebSocket](https://platform.openai.com/docs/guides/realtime-websocket), you can build applications with a Realtime AI model. Doing so will require you to **send client events** to initiate actions, and **listen for server events** to respond to actions taken by the Realtime API. This guide will walk through the event flows required to use model capabilities like audio and text generation, and how to think about the state of a Realtime session.

### Handling audio with WebSockets

When sending and receiving audio over a WebSocket, you will have a bit more work to do in order to send media from the client, and receive media from the server. Below, you'll find a table describing the flow of events during a WebSocket session that are necessary to send and receive audio over the WebSocket.

The events below are given in lifecycle order, though some events (like the `delta` events) may happen concurrently.

| Lifecycle stage        | Client events                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | Server events                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Session initialization | [`session.update`](https://platform.openai.com/docs/api-reference/realtime-client-events/session/update)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | [`session.created`](https://platform.openai.com/docs/api-reference/realtime-server-events/session/created)<br><br>[`session.updated`](https://platform.openai.com/docs/api-reference/realtime-server-events/session/updated)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| User audio input       | [`conversation.item.create`](https://platform.openai.com/docs/api-reference/realtime-client-events/conversation/item/create)  <br>  (send whole audio message)<br><br>[`input_audio_buffer.append`](https://platform.openai.com/docs/api-reference/realtime-client-events/input_audio_buffer/append)  <br>  (stream audio in chunks)<br><br>[`input_audio_buffer.commit`](https://platform.openai.com/docs/api-reference/realtime-client-events/input_audio_buffer/commit)  <br>  (used when VAD is disabled)<br><br>[`response.create`](https://platform.openai.com/docs/api-reference/realtime-client-events/response/create)  <br>  (used when VAD is disabled) | [`input_audio_buffer.speech_started`](https://platform.openai.com/docs/api-reference/realtime-server-events/input_audio_buffer/speech_started)<br><br>[`input_audio_buffer.speech_stopped`](https://platform.openai.com/docs/api-reference/realtime-server-events/input_audio_buffer/speech_stopped)<br><br>[`input_audio_buffer.committed`](https://platform.openai.com/docs/api-reference/realtime-server-events/input_audio_buffer/committed)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| Server audio output    | [`input_audio_buffer.clear`](https://platform.openai.com/docs/api-reference/realtime-client-events/input_audio_buffer/clear)  <br>  (used when VAD is disabled)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | [`conversation.item.created`](https://platform.openai.com/docs/api-reference/realtime-server-events/conversation/item/created)<br><br>[`response.created`](https://platform.openai.com/docs/api-reference/realtime-server-events/response/created)<br><br>[`response.output_item.created`](https://platform.openai.com/docs/api-reference/realtime-server-events/response/output_item/created)<br><br>[`response.content_part.added`](https://platform.openai.com/docs/api-reference/realtime-server-events/response/content_part/added)<br><br>[`response.audio.delta`](https://platform.openai.com/docs/api-reference/realtime-server-events/response/audio/delta)<br><br>[`response.audio_transcript.delta`](https://platform.openai.com/docs/api-reference/realtime-server-events/response/audio_transcript/delta)<br><br>[`response.text.delta`](https://platform.openai.com/docs/api-reference/realtime-server-events/response/text/delta)<br><br>[`response.audio.done`](https://platform.openai.com/docs/api-reference/realtime-server-events/response/audio/done)<br><br>[`response.audio_transcript.done`](https://platform.openai.com/docs/api-reference/realtime-server-events/response/audio_transcript/done)<br><br>[`response.text.done`](https://platform.openai.com/docs/api-reference/realtime-server-events/response/text/done)<br><br>[`response.content_part.done`](https://platform.openai.com/docs/api-reference/realtime-server-events/response/content_part/done)<br><br>[`response.output_item.done`](https://platform.openai.com/docs/api-reference/realtime-server-events/response/output_item/done)<br><br>[`response.done`](https://platform.openai.com/docs/api-reference/realtime-server-events/response/done)<br><br>[`rate_limits.updated`](https://platform.openai.com/docs/api-reference/realtime-server-events/rate_limits/updated) |

----


The Realtime models also support **function calling**, which enables you to execute custom code to extend the capabilities of the model. Here's how it works at a high level:

1. When [updating the session](https://platform.openai.com/docs/api-reference/realtime-client-events/session/update) or [creating a response](https://platform.openai.com/docs/api-reference/realtime-client-events/response/create), you can specify a list of available functions for the model to call.
2. If when processing input, the model determines it should make a function call, it will add items to the conversation representing arguments to a function call.
3. When the client detects conversation items that contain function call arguments, it will execute custom code using those arguments
4. When the custom code has been executed, ==the client will **create new conversation items** that contain the output of the function call,== and ask the model to respond.

Let's see how this would work in practice by adding a callable function that will provide today's horoscope to users of the model. We'll show the shape of the client event objects that need to be sent, and what the server will emit in turn.

### Configure callable functions

First, we must give the model a selection of functions it can call based on user input. **Available functions can be configured either at the session level, or the individual response level.**

- Session: `session.tools` property in [`session.update`](https://platform.openai.com/docs/api-reference/realtime-client-events/session/update)
- Response: `response.tools` property in [`response.create`](https://platform.openai.com/docs/api-reference/realtime-client-events/response/create)

Here's an example client event payload for a `session.update` that configures a horoscope generation function, that takes a single argument (the astrological sign for which the horoscope should be generated):

```python
{
  "type": "session.update",
  "session": {
    "tools": [
      {
        "type": "function",
        "name": "generate_horoscope",
        "description": "Give today's horoscope for an astrological sign.",
        "parameters": {
          "type": "object",
          "properties": {
            "sign": {
              "type": "string",
              "description": "The sign for the horoscope.",
              "enum": [
                "Aries",
                "Taurus",
                "Gemini",
                "Cancer",
                "Leo",
                "Virgo",
                "Libra",
                "Scorpio",
                "Sagittarius",
                "Capricorn",
                "Aquarius",
                "Pisces"
              ]
            }
          },
          "required": ["sign"]
        }
      }
    ],
    "tool_choice": "auto",
  }
}
```
The `description` fields for the function and the parameters help the model choose whether or not to call the function, and what data to include in each parameter. If the model receives input that indicates the user wants their horoscope, it will call this function with a `sign` parameter.


### Detect when the model wants to call a function

Let's say our application adds the following conversation item and attempts to generate a response:

[`conversation.item.create`](https://platform.openai.com/docs/api-reference/realtime-client-events/conversation/item/create)
```python
{
  "type": "conversation.item.create",
  "item": {
    "type": "message",
    "role": "user",
    "content": [
      {
        "type": "input_text",
        "text": "What is my horoscope? I am an aquarius."
      }
    ]
  }
}
```

Followed by a **client event to generate a response:**

[`response.create`](https://platform.openai.com/docs/api-reference/realtime-client-events/response/create)
```python
{
  "type": "response.create"
}
```

Instead of immediately returning a text or audio response, the model will instead generate a response that contains the arguments that should be passed to a function in the developer's application. You can listen for realtime updates to function call arguments using the [`response.function_call_arguments.delta`](https://platform.openai.com/docs/api-reference/realtime-server-events/response/function_call_arguments/delta) server event, but `response.done` will also have the complete data we need to call our function.

[`response.done`](https://platform.openai.com/docs/api-reference/realtime-server-events/response/done)
```python
{
  "type": "response.done",
  "event_id": "event_AeqLA8iR6FK20L4XZs2P6",
  "response": {
    "object": "realtime.response",
    "id": "resp_AeqL8XwMUOri9OhcQJIu9",
    "status": "completed",
    "status_details": null,
    "output": [
      {
        "object": "realtime.item",
        "id": "item_AeqL8gmRWDn9bIsUM2T35",
        "type": "function_call",
        "status": "completed",
        "name": "generate_horoscope",
        "call_id": "call_sHlR7iaFwQ2YQOqm",
        "arguments": "{\"sign\":\"Aquarius\"}"
      }
    ],
    "usage": {
      "total_tokens": 541,
      "input_tokens": 521,
      "output_tokens": 20,
      "input_token_details": {
        "text_tokens": 292,
        "audio_tokens": 229,
        "cached_tokens": 0,
        "cached_tokens_details": { "text_tokens": 0, "audio_tokens": 0 }
      },
      "output_token_details": {
        "text_tokens": 20,
        "audio_tokens": 0
      }
    },
    "metadata": null
  }
}
```
In the JSON emitted by the server, we can detect that the model wants to call a custom function:

|Property|Function calling purpose|
|---|---|
|`response.output[0].type`|When set to `function_call`, indicates this response contains arguments for a named function call.|
|`response.output[0].name`|The name of the configured function to call, in this case `generate_horoscope`|
|`response.output[0].arguments`|A JSON string containing arguments to the function. In our case, `"{\"sign\":\"Aquarius\"}"`.|
|`response.output[0].call_id`|A system-generated ID for this function call - **you will need this ID to pass a function call result back to the model**.|

---

### Provide the results of a function call to the model

Upon receiving a response from the model with arguments to a function call, your application can execute code that satisfies the function call. This could be anything you want, like talking to external APIs or accessing databases.

Once you are ready to give the model the results of your custom code, you can create a new conversation item containing the result via the **`conversation.item.create`** client event.

[`conversation.item.create`](https://platform.openai.com/docs/api-reference/realtime-client-events/conversation/item/create)
```python
{
  "type": "conversation.item.create",
  "item": {
    "type": "function_call_output",
    "call_id": "call_sHlR7iaFwQ2YQOqm",
    "output": "{\"horoscope\": \"You will soon meet a new friend.\"}"
  }
}
```

- The conversation item type is `function_call_output`
- `item.call_id` is the same ID we got back in the `response.done` event above
- `item.output` is a JSON string containing the results of our function call

Once we have added the conversation item containing our function call results, we again emit the `response.create` event from the client. This will trigger a model response using the data from the function call.

[`response.create`](https://platform.openai.com/docs/api-reference/realtime-client-events/response/create)
```python
{
  "type": "response.create"
}
```

---

# Some response types

## input_audio_buffer.speech_started

Sent by the server when in `server_vad` mode to indicate that s==peech has been detected in the audio buffer==. This can happen any time audio is added to the buffer (unless speech is already detected). The client may want to use this event to interrupt audio playback or provide visual feedback to the user.

The client should expect to receive a `input_audio_buffer.speech_stopped` event when speech stops. The `item_id` property is the ID of the user message item that will be created when speech stops and will also be included in the **`input_audio_buffer.speech_stopped`** event (unless the client manually commits the audio buffer during VAD activation).

```json
Sending audio file: converted_audio.wav
Received event: {
  "type": "input_audio_buffer.speech_started",
  "event_id": "event_BAE7i4DPPrpyQxvFdqwY7",
  "audio_start_ms": 96,
  "item_id": "item_BAE7iY9LDU7dvX7GTRZ0S"
}
```

```json
Audio file transmission completed.
Received event: {
  "type": "input_audio_buffer.speech_stopped",
  "event_id": "event_BAE7lY4RfGzkccBpD0Eyd",
  "audio_end_ms": 2080,
  "item_id": "item_BAE7iY9LDU7dvX7GTRZ0S"
}
```

## input_audio_buffer.committed

Returned when an input audio buffer is committed, either by the client or automatically in server VAD mode. The `item_id` property is the ID of the user message item that will be created, thus a **`conversation.item.created`** event will also be sent to the client.

``` json
Received event: {
  "type": "input_audio_buffer.committed",
  "event_id": "event_BAE7lhm4LkvzBvrK4ASBk",
  "previous_item_id": null,
  "item_id": "item_BAE7iY9LDU7dvX7GTRZ0S"
}
```

```json
Received event: {
  "type": "conversation.item.created",
  "event_id": "event_BAE7lXm7usa4E1ybBmn80",
  "previous_item_id": null,
  "item": {
    "id": "item_BAE7iY9LDU7dvX7GTRZ0S",
    "object": "realtime.item",
    "type": "message",
    "status": "completed",
    "role": "user",
    "content": [
      {
        "type": "input_audio",
        "transcript": null
      }
    ]
  }
}
```

## response.created

Returned when a new Response is created. **The first event of response creation**, where the response is in an initial state of `in_progress`.

```json
Received event: {
  "type": "response.created",
  "event_id": "event_BAE7l2BnrLYU8Y4uJtd7B",
  "response": {
    "object": "realtime.response",
    "id": "resp_BAE7l9FVzr3eZe1lqBv36",
    "status": "in_progress",
    "status_details": null,
    "output": [],
    "conversation_id": "conv_BAE7gAKnP8T7ITzekSW5G",
    "modalities": [
      "text",
      "audio"
    ],
    "voice": "alloy",
    "output_audio_format": "pcm16",
    "temperature": 0.6,
    "max_output_tokens": "inf",
    "usage": null,
    "metadata": null
  }
}
```

## response.output_item.added

Returned when a new Item is created during Response generation.

```json
Received event: {
  "type": "response.output_item.added",
  "event_id": "event_BAE7ml0AOvNCowFKQIkow",
  "response_id": "resp_BAE7l9FVzr3eZe1lqBv36",
  "output_index": 0,
  "item": {
    "id": "item_BAE7lFS0QSolIEzXCMtj6",
    "object": "realtime.item",
    "type": "function_call",
    "status": "in_progress",
    "name": "search",
    "call_id": "call_fTuC8D7u141uOgDt",
    "arguments": ""
  }
}
```

## conversation.item.created

Returned when a conversation item is created. There are several scenarios that produce this event:

- The server is generating a Response, which if successful will produce either one or two Items, which will be of type `message` (role `assistant`) or type `function_call`.
- The input audio buffer has been committed, either by the client or the server (in `server_vad` mode). The server will take the content of the input audio buffer and add it to a new user message Item.
- The client has sent a `conversation.item.create` event to add a new Item to the Conversation.
```json
Received event: {
  "type": "conversation.item.created",
  "event_id": "event_BAE7mJbVZZQbcfTfFOVog",
  "previous_item_id": "item_BAE7iY9LDU7dvX7GTRZ0S",
  "item": {
    "id": "item_BAE7lFS0QSolIEzXCMtj6",
    "object": "realtime.item",
    "type": "function_call",
    "status": "in_progress",
    "name": "search",
    "call_id": "call_fTuC8D7u141uOgDt",
    "arguments": ""
  }
}
```

## response.function_call_arguments.delta

Returned when the model-generated function call arguments are updated.

```json
Received event: {
  "type": "response.function_call_arguments.delta",
  "event_id": "event_BAE7mfUeQX3DeqcAo5iQT",
  "response_id": "resp_BAE7l9FVzr3eZe1lqBv36",
  "item_id": "item_BAE7lFS0QSolIEzXCMtj6",
  "output_index": 0,
  "call_id": "call_fTuC8D7u141uOgDt",
  "delta": "{\""
}
```
## response.function_call_arguments.done

Returned when the model-generated function call arguments are done streaming. Also emitted when a Response is interrupted, incomplete, or cancelled.

```json
Received event: {
  "type": "response.function_call_arguments.done",
  "event_id": "event_BAE7mdsfeJYzHWkfLEZQ9",
  "response_id": "resp_BAE7l9FVzr3eZe1lqBv36",
  "item_id": "item_BAE7lFS0QSolIEzXCMtj6",
  "output_index": 0,
  "call_id": "call_fTuC8D7u141uOgDt",
  "name": "search",
  "arguments": "{\"text\":\"Hamrah Aval (MCI) energy consumption\"}"
}
```

نکته: مدل تصمیم میگیره که از ورودی که ما بهش دادیم اون جمله argument رو برای retrieval استفاده کنیم (پس روی جمله اولیه semantic نمیزنیم). پس اصن transcription نمیخاد.
## response.output_item.done

Emitted when an output item is marked done.
```json
Received event: {
  "type": "response.output_item.done",
  "event_id": "event_BAE7mUBlerenCTxZeoDbl",
  "response_id": "resp_BAE7l9FVzr3eZe1lqBv36",
  "output_index": 0,
  "item": {
    "id": "item_BAE7lFS0QSolIEzXCMtj6",
    "object": "realtime.item",
    "type": "function_call",
    "status": "completed",
    "name": "search",
    "call_id": "call_fTuC8D7u141uOgDt",
    "arguments": "{\"text\":\"Hamrah Aval (MCI) energy consumption\"}"
  }
}
```
نکته: برای یه event که ما فرستادیم ممکنه چندین response مختلف برگرده.
## response.done

Returned when a Response is done streaming. Always emitted, no matter the final state. The Response object included in the `response.done` event will include all output Items in the Response but will omit the raw audio data.
```json
Received event: {
  "type": "response.done",
  "event_id": "event_BAE7mJXkcEyyXtyzj1Yw2",
  "response": {
    "object": "realtime.response",
    "id": "resp_BAE7l9FVzr3eZe1lqBv36",
    "status": "completed",
    "status_details": null,
    "output": [
      {
        "id": "item_BAE7lFS0QSolIEzXCMtj6",
        "object": "realtime.item",
        "type": "function_call",
        "status": "completed",
        "name": "search",
        "call_id": "call_fTuC8D7u141uOgDt",
        "arguments": "{\"text\":\"Hamrah Aval (MCI) energy consumption\"}"
      }
    ],
    "conversation_id": "conv_BAE7gAKnP8T7ITzekSW5G",
    "modalities": [
      "text",
      "audio"
    ],
    "voice": "alloy",
    "output_audio_format": "pcm16",
    "temperature": 0.6,
    "max_output_tokens": "inf",
    "usage": {
      "total_tokens": 140,
      "input_tokens": 118,
      "output_tokens": 22,
      "input_token_details": {
        "text_tokens": 102,
        "audio_tokens": 16,
        "cached_tokens": 64,
        "cached_tokens_details": {
          "text_tokens": 64,
          "audio_tokens": 0
        }
      },
      "output_token_details": {
        "text_tokens": 22,
        "audio_tokens": 0
      }
    },
    "metadata": null
  }
}
```

## rate_limits.updated

Emitted at the beginning of a Response to indicate the updated rate limits. When a Response is created **some tokens will be "reserved" for the output tokens, the rate limits shown here reflect that reservation**, which is then adjusted accordingly once the Response is completed.
```json
Received event: {
  "type": "rate_limits.updated",
  "event_id": "event_BAE7laBsZDgA5B9oTuS48",
  "rate_limits": [
    {
      "name": "requests",
      "limit": 10000,
      "remaining": 9999,
      "reset_seconds": 0.006
    },
    {
      "name": "tokens",
      "limit": 4000000,
      "remaining": 3995388,
      "reset_seconds": 0.069
    }
  ]
}
```


---

```json
Received event: {
  "type": "response.audio.delta",
  "event_id": "event_BAE7reXWeRKvsNt3p7d1Q",
  "response_id": "resp_BAE7ogSqjxKU4vhzXJ6yq",
  "item_id": "item_BAE7pFaHEVmYjzpy4XI96",
  "output_index": 0,
  "content_index": 0,
  "delta": "3v/h/9z/4//W/97/2P/Z/9j/1v/e}
```

```json
Received event: {
  "type": "response.audio_transcript.delta",
  "event_id": "event_BAE7qCvjKqsQoTyjX3TLF",
  "response_id": "resp_BAE7ogSqjxKU4vhzXJ6yq",
  "item_id": "item_BAE7pFaHEVmYjzpy4XI96",
  "output_index": 0,
  "content_index": 0,
  "delta": " company"
}
```

```json
Received event: {
  "type": "response.audio.done",
  "event_id": "event_BAE7rTdAHPY8Pu10Gdhij",
  "response_id": "resp_BAE7ogSqjxKU4vhzXJ6yq",
  "item_id": "item_BAE7pFaHEVmYjzpy4XI96",
  "output_index": 0,
  "content_index": 0
}
```

```json
Received event: {
  "type": "response.audio_transcript.done",
  "event_id": "event_BAE7rAype0jSXENv9u85R",
  "response_id": "resp_BAE7ogSqjxKU4vhzXJ6yq",
  "item_id": "item_BAE7pFaHEVmYjzpy4XI96",
  "output_index": 0,
  "content_index": 0,
  "transcript": "I'm sorry, but I couldn't find specific information about Hamrah Aval (MCI)'s energy consumption. Would you like to know more about another aspect of the company?"
}
```
