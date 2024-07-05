# stream-ai-apigee

# gpt-bot

## Architecture
* Run Chatbot as container on an ubuntu 20 VM using api of GPT 3.5 
* Expose the service via public ip
* Stream by Apigee X proxy



## 1 Running in Docker

This project folder `gpt-bot` includes a Dockerfile that allows you to easily build and host your LangServe app.

### Building the Image

To build the image, you simply:

```shell
docker build . -t my-gpt-bot
```

### Running the Image Locally

To run the image, you'll need to include any environment variables
necessary for your application.

In the below example, we inject the `OPENAI_API_KEY` environment
variable with the value set in my local environment
(`$OPENAI_API_KEY`)

We also expose port 8000 with the `-p port:port` option.

```shell
docker run -e OPENAI_API_KEY=$OPENAI_API_KEY -p 8000:8000 my-gpt-bot -t gpt-bot1
```

## Step 2 Test local bot 
Refer to 