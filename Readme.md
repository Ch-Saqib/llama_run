# HOW WE RUN LLAMA IN YOUR LOCAL MACHINE (WIN/MAC) ###

Run Llama 3.1, Phi 3, Mistral, Gemma 2, and other models. Customize and create your own.

<a href="https://www.youtube.com/watch?v=9sPKTNGaPf8">Ollama on Google Colab: A Game-Changer!</a>

## In-Depth Tutorial on Using Ollama in Google Colab

Ollama is a powerful tool that allows you to run and experiment with language models locally. It offers flexibility for running different models, including some that may not be available on cloud-based APIs. In this tutorial, we'll walk through how to set up and use Ollama within Google Colab, even though Ollama is typically designed for local environments.

Given that Ollama typically runs as a Docker container or through a local setup, using it directly in Google Colab requires some creative workarounds. We will explore a few strategies to make the most out of Ollama's capabilities within Colab.

## Table of Contents
- [Introduction to Ollama](#introduction-to-ollama)
- [Setting Up Ollama](#setting-up-ollama)
  - [Running Ollama Locally](#running-ollama-locally)
  - [Using Ngrok to Expose Local Ollama Instance to Google Colab](#using-ngrok-to-expose-local-ollama-instance-to-google-colab)
- [Interacting with Ollama in Google Colab](#interacting-with-ollama-in-google-colab)
  - [Connecting Colab to Your Local Ollama Instance](#connecting-colab-to-your-local-ollama-instance)
  - [Running API Calls from Colab](#running-api-calls-from-colab)
- [Advanced Usage](#advanced-usage)
  - [Customizing Ollama Models](#customizing-ollama-models)
  - [Chaining API Calls for Complex Tasks](#chaining-api-calls-for-complex-tasks)
- [Conclusion](#conclusion)

## 1. Introduction to Ollama

Ollama is a platform that enables you to run language models on your local machine. It is often used to run large language models (LLMs) for tasks like text generation, question answering, or code generation. Ollama typically uses Docker for deployment, which ensures consistency across different environments.

### Why Use Ollama in Colab?
- **Experimentation**: Integrating it with Colab allows you to experiment with models remotely and share your work easily.
- **Flexibility**: By connecting Colab to a local instance of Ollama, you can leverage Colab's cloud environment to process and analyze results, even if the heavy computation is done locally.

## 2. Setting Up Ollama

### Running Ollama Locally

Before using Ollama in Google Colab, you need to set it up on your local machine. Here's how:

1. **Install Docker**:  

   Ollama requires Docker to run its containers. Install Docker for your operating system by following the instructions [here](https://docs.docker.com/get-docker/).

2. **Download and Run Ollama**:

     * Once Docker is installed, you can pull the Ollama Docker image and run it.

   ```bash
   docker pull ollama/ollama:latest
   docker run -d -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama
   ```
     * This command pulls the latest Ollama Docker image and runs it on port 11434.

### Using Ngrok to Expose Local Ollama Instance to Google Colab 

To interact with Ollama from Google Colab, you need to expose your local Ollama instance to the internet. Ngrok is a handy tool that creates secure tunnels to localhost, allowing you to access your local server from anywhere.

**1.Install Ngrok:**

 - Download and install Ngrok from [here](https://ngrok.com/download)

**2.Run Ngrok:**

 - Start Ngrok to tunnel your local Ollama instance.

 ```bash
 ngrok http --log stderr 11434 --host-header localhost:11434
 ```
 - This command will provide a public URL that forwards to your local Ollama instance on port 11434.

**3.Copy the Public URL:**

    ```bash
    t=2024-09-05T18:46:58+0500 lvl=info msg="started tunnel" obj=tunnels name=command_line addr=http://localhost:11434 url=https://99fd-129.ngrok-free.app
    ```
  - Ngrok will output a public URL like http://abcd1234.ngrok.io. Copy this URL; you’ll need it in Google Colab.

## 3: Download the Llama Model in the Ollama Container

To download the Llama 3.1 model within the Ollama container, follow these steps:

 **1.Open Docker Dashboard:** Navigate to your Docker Dashboard or use the command line.

 **2.Access the Ollama Container:**

        Find the ollama container from the list of running containers.
        Click on the container to open the details.
        Go to the Exec tab (or use docker exec via terminal).

 **3.Run the Model Download Command:**

  - In the command input field, type the following command and execute it:

  ```bash
  ollama run llama3.1
  ```

  This command will initiate the download of the Llama 3.1 model into the container.

  Note: If you PC hardware is weak you can run the following model:

  ```bash
  ollama run tinyllama
  ```

## 4. Interacting with Ollama in Google Colab

Now that Ollama is running locally and exposed via Ngrok, you can interact with it from Google Colab.

**Connecting Colab to Your Local Ollama Instance**

In your Google Colab notebook:

  **1.Install Required Libraries:**

   - Colab comes with many libraries pre-installed, but you might need requests for API interactions.

   ```bash
   !pip install requests
   ```

   **2.Set Up the Connection:**

    - Use the Ngrok URL to connect Colab to your local Ollama instance.

  ```python
  import requests

# Replace this URL with your Ngrok URL
ollama_url = "https://99fd-104-28-212-129.ngrok-free.app"

def query_ollama(prompt, model="tinyllama"):
   headers = {
      "ngrok-skip-browser-warning": "true"  # This header bypasses the Ngrok browser warning
   }
   data = {
      "prompt": prompt,
      "model": model,
      "stream": False  # Disable streaming for a simple response
   }
   
   # Sending the request to generate a completion from the model
   response = requests.post(f"{ollama_url}/api/generate", json=data, headers=headers)
   
   # If the response was successful, return the generated text
   if response.status_code == 200:
      return response.json().get("response", "No response found")
   else:
      return f"Error: {response.status_code}, {response.text}"

    # Test the connection with a simple Hello World prompt
    response = query_ollama("Greet me in 3 words!")
    print(response)
  ```


