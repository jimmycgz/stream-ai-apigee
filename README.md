# stream-ai-apigee

# gpt-bot

## Architecture
* Run Chatbot as a containerized API server on Ubuntu 20 VM using GPT 3.5 
* Expose the service via public ip
* Stream by Apigee X proxy


## Step 1: Running in Docker

This project folder `gpt-bot` includes a Dockerfile that allows you to easily build and host your LangServe app.

### Building the Image

To build the image, you simply:

```shell
git clone https://github.com/jimmycgz/stream-ai-apigee.git
cd stream-ai-apigee/gpt-bot
docker build . -t gpt-bot
```

### Running the Image Locally

To run the image, you'll need to include any environment variables
necessary for your application.

In the below example, we inject the `OPENAI_API_KEY` environment
variable with the value set in my local environment
(`$OPENAI_API_KEY`)

We also expose port 8000 with the `-p port:port` option.

```shell
echo $OPENAI_API_KEY 
docker run --name my-gpt-bot1 -e OPENAI_API_KEY=$OPENAI_API_KEY -p 8000:8000 gpt-bot
```

## Step 2: Test local bot 
Refer to `stream-ai-apigee/test-local-bot.ipynb`

## Step 3: Deploy Stream Proxy on Apigee X

## Step 4: Test GPT bot via Apigee Proxy

### Study api spec
open in browser: http://pub-ip-vm:8000/docs

### Test curl stream

ssh to the VM

```
curl -X POST "https://${APIGEE_HOST}/openai/stream" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer ${OPENAI_API_KEY}" \
  -d '{
  "input": [
    {
      "role": "system",
      "content": "You are a highly educated person who loves to use big words. You are also concise. Never answer in more than three sentences."
    },
    {
      "role": "human",
      "content": "how to use python to call openai api?"
    }
  ],
  "config": {},
  "kwargs": {}
}'


# output
event: metadata
data: {"run_id": "e0f47494-e94b-489d-933e-68450fea6ae1"}

event: data
data: {"content":"","additional_kwargs":{},"response_metadata":{},"type":"AIMessageChunk","name":null,"id":"run-e0f47494-e94b-489d-933e-68450fea6ae1","example":false,"tool_calls":[],"invalid_tool_calls":[],"usage_metadata":null,"tool_call_chunks":[]}

event: data
data: {"content":"To","additional_kwargs":{},"response_metadata":{},"type":"AIMessageChunk","name":null,"id":"run-e0f47494-e94b-489d-933e-68450fea6ae1","example":false,"tool_calls":[],"invalid_tool_calls":[],"usage_metadata":null,"tool_call_chunks":[]}

event: data
data: {"content":" call","additional_kwargs":{},"response_metadata":{},"type":"AIMessageChunk","name":null,"id":"run-e0f47494-e94b-489d-933e-68450fea6ae1","example":false,"tool_calls":[],"invalid_tool_calls":[],"usage_metadata":null,"tool_call_chunks":[]}

event: data
data: {"content":" the","additional_kwargs":{},"response_metadata":{},"type":"AIMessageChunk","name":null,"id":"run-e0f47494-e94b-489d-933e-68450fea6ae1","example":false,"tool_calls":[],"invalid_tool_calls":[],"usage_metadata":null,"tool_call_chunks":[]}

event: data
data: {"content":" Open","additional_kwargs":{},"response_metadata":{},"type":"AIMessageChunk","name":null,"id":"run-e0f47494-e94b-489d-933e-68450fea6ae1","example":false,"tool_calls":[],"invalid_tool_calls":[],"usage_metadata":null,"tool_call_chunks":[]}

event: data
data: {"content":"AI","additional_kwargs":{},"response_metadata":{},"type":"AIMessageChunk","name":null,"id":"run-e0f47494-e94b-489d-933e-68450fea6ae1","example":false,"tool_calls":[],"invalid_tool_calls":[],"usage_metadata":null,"tool_call_chunks":[]}
```
### Test Python stream with chatbot

Refer to `stream-ai-apigee/test-local-bot.ipynb`
```
docker exec -it my-gpt-bot1 bash

   10  pip --version
   11  python --version
   12  pip install langchain
   python
       from langchain.prompts.chat import ChatPromptTemplate
       from langserve import RemoteRunnable
       openai_llm = RemoteRunnable("https://pub-ip.nip.io/openai/")
       prompt = ChatPromptTemplate.from_messages(
    [
        (
            "system",
            "You are a nice person who loves to keep the others happy "
            + "You are also creative and always answer in 300 words.",
        ),
        ("human", "tell a joke about the summer in 500 words?"),
    ]
).format_messages()

openai_llm.invoke(prompt)

# Steam endpoint /openai/stream
for chunk in openai_llm.stream(prompt):
    print(chunk.content, end="", flush=True)

```

## References
This solution was tailored from [LangServe]( https://github.com/langchain-ai/langserve.git)