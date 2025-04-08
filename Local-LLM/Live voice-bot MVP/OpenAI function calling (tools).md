sudo 
![[Pasted image 20250303161345.png]]

It is done via "tools":
![[Pasted image 20250303161423.png]]

---

OpenAI function calling is a feature that allows developers to enable the OpenAI API (e.g., GPT-4-turbo) to call and execute specific functions within their applications based on user queries. This feature enables structured outputs, integration with APIs, and automation of tasks.

### **How It Works**

1. **Defining Functions**: Developers define functions with specific names, parameters, and descriptions.
2. **Model Recognizes Function Calls**: GPT decides when to call a function based on user input.
3. **Executing the Function**: The application executes the function and returns the result to the model.
4. **Model Uses the Response**: The model processes the function's response and continues the conversation.

---

```python
functions = [
    {
        "name": "search",
        "description": "Retrieve the most relevant Q&A pair based on user query from Qdrant.",
        "parameters": {
            "type": "object",
            "properties": {
                "text": {
                    "type": "string",
                    "description": "The user's question"
                }
            },
            "required": ["text"]
        }
    }
]
```

This block of code defines **a function schema** for **OpenAI's function calling** mechanism. It tells **GPT-4o** that it can call a function named `"search"` ==whenever it determines it's necessary based on user input.==

- This **does not execute the function** but rather **tells GPT-4o that this function exists and can be used**.
- When GPT-4o decides to retrieve relevant data, it will issue a function call to `search`.

Even though the user query is a single text input, It is **wrapped in a JSON object** because:

- It **maintains extensibility** (e.g., adding `"history"`, `"metadata"`, or other parameters later).
- GPT-4o can **understand function parameters better** in a structured format.
- It **follows OpenAI’s function calling schema**, where all function inputs must be inside an object.
## **How GPT-4o Uses This?**

1️⃣ **User asks a question**  
2️⃣ **GPT-4o analyzes if retrieval is needed**  
3️⃣ **If needed, GPT-4o calls `search(text="User's question")`**  
4️⃣ **The search function fetches relevant Q&A pairs from Qdrant**  
5️⃣ **GPT-4o uses the retrieved data to generate a response**

---

```python
def chat_with_gpt(user_query):
    response = openai.ChatCompletion.create(
        model="gpt-4o",
        messages=[{"role": "user", "content": user_query}],
        functions=functions,
        function_call="auto"
    )

    # Check if a function call is triggered
    if "function_call" in response["choices"][0]["message"]:
        function_name = response["choices"][0]["message"]["function_call"]["name"]
        arguments = json.loads(response["choices"][0]["message"]["function_call"]["arguments"])
        
        if function_name == "search":
            retrieved_info = search(arguments["text"])
            
            if retrieved_info:
                qa_text = "\n".join([f"Q: {item['question']}\nA: {item['answer']}" for item in retrieved_info])
                return f"Here are the most relevant answers:\n{qa_text}"
            else:
                return "I couldn't find an exact match, but I can try answering it."

    return response["choices"][0]["message"]["content"]
```

This function, **`chat_with_gpt(user_query)`**, is responsible for interacting with **GPT-4o** and dynamically calling the `search()` function when needed.

- **`openai.ChatCompletion.create(...)`** sends a request to GPT-4o to generate a response.
- **`model="gpt-4o"`** specifies that we are using OpenAI’s GPT-4o model.
- **`messages=[{"role": "user", "content": user_query}]`** sends the user’s input as a message.
- **`functions=functions`** tells GPT-4o about available function calls (in this case, `search()`).
- ==**`function_call="auto"`==** allows GPT-4o to **decide if it needs to call a function** (e.g., if retrieval from Qdrant is required).


**Checking if GPT-4o Triggered a Function Call**

```python
`if "function_call" in response["choices"][0]["message"]:`
```

- The response from GPT-4o contains a `function_call` **only if the model decided that an external function should be used**.
- This checks whether GPT-4o has **requested to call a function** instead of generating a direct response.


**Extracting Function Name and Arguments**

```python
function_name = response["choices"][0]["message"]["function_call"]["name"]
arguments = json.loads(response["choices"][0]["message"]["function_call"]["arguments"])
```

- **`function_name`** stores the function that GPT-4o wants to call (e.g., `"search"`).
- **`arguments`** extracts the parameters GPT-4o passed to the function (e.g., `{"text": "User's question"}`).
- The parameters are JSON-encoded, so we use `json.loads(...)` to convert them into a Python dictionary.


**Calling the `search()` Function**

```python
if function_name == "search":
    retrieved_info = search(arguments["text"])
```

- If GPT-4o requested **the `search()` function**, we **execute it manually** with the extracted `"text"` parameter.


**NOTE:**
- The expression **`response["choices"][0]["message"]["content"]`** is used to extract the **text response** generated by **GPT-4o** from the API response.

---

# Function calling

Enable models to fetch data and take actions.

**Function calling** provides a powerful and flexible way for OpenAI models to interface with your code or external services, and has two primary use cases:

|**Fetching Data**|Retrieve up-to-date information to incorporate into the model's response (RAG). Useful for searching knowledge bases and retrieving specific data from APIs (e.g. current weather data). ==This is our usecase for now.==

|**Taking Action**|Perform actions like submitting a form, calling APIs, modifying application state (UI/frontend or backend), or taking agentic workflow actions (like [handing off](https://cookbook.openai.com/examples/orchestrating_agents) the conversation).|

```python
tools = [{
    "type": "function",
    "function": {...}
}]
```
- **`tools`** is a list of external tools GPT-4o can use.
- Each tool is an **object** with:
    - **`"type": "function"`** → Specifies that this tool is a function (not an API, plugin, or retrieval tool).
    - **`"function": {...}`** → Defines the actual function.

```python
"function": {
    "name": "get_weather",
    "description": "Get current temperature for provided coordinates in Celsius.",
    "parameters": {...},
    "strict": True
}
```

- **`"name": "get_weather"`** → The function name that GPT-4o will use when calling it.
    
- **`"description": "Get current temperature for provided coordinates in Celsius."`**
    
    - ==Tells GPT-4o **when to use this function**.==
    - If a user asks: _"What's the temperature in Paris?"_, GPT-4o may call `get_weather`.
- **`"strict": True`**
    
    - Ensures **only the defined parameters** are used (no extra properties).


```python
"parameters": {
    "type": "object",
    "properties": {
        "latitude": {"type": "number"},
        "longitude": {"type": "number"}
    },
    "required": ["latitude", "longitude"],
    "additionalProperties": False
}
```

- The function expects an **object** as input.
- **`properties`** defines what inputs are expected:
    - `"latitude"` → A **number** (e.g., `48.8566` for Paris).
    - `"longitude"` → A **number** (e.g., `2.3522` for Paris).
- **`"required": ["latitude", "longitude"]`**
    - Both `latitude` and `longitude` **must be provided**; otherwise, the function won’t be called.
- **`"additionalProperties": False`**
    - Ensures **no extra parameters** are accepted.


### Send request to model:

```python
completion = client.chat.completions.create(
    model="gpt-4o",
    messages=messages,
    tools=tools,
)
```

==**Model decides to call function(s)** – model returns the **name** and **input arguments**.==

```python
print(completion.choices[0].message.tool_calls)
```
```json
[{
    "id": "call_12345xyz",
    "type": "function",
    "function": {
        "name": "get_weather",
        "arguments": "{\"location\":\"Paris, France\"}"
    }
}]
```

---

This guide will cover how you can give the model access to your own functions through **function calling**. Based on the ==system prompt and messages==, the model may decide to call these functions — **==instead of (or in addition to) generating text or audio**.==

Execute get_weather function (yourself)

```python
tool_call = completion.choices[0].message.tool_calls[0]
args = json.loads(tool_call.function.arguments)

result = get_weather(args["latitude"], args["longitude"])
```
  

Step 4: Supply result and call model again (so it can ==incorporate them into its final response==.)

```python
messages.append(completion.choices[0].message)  # append model's function call message
messages.append({                               # append result message
    "role": "tool",
    "tool_call_id": tool_call.id,
    "content": str(result)
})

completion_2 = client.chat.completions.create(
    model="gpt-4o",
    messages=messages,
    tools=tools,
)
```


/home/maryam/Music/audio_mvp.ogg
/home/maryam/Music/audio_english.ogg

`gpt-4o-mini-realtime-preview-2024-12-17`??
general flow of the code is as intended ?
