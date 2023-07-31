<div align="center">

# <span><img height="30" src="https://raw.github.com/vemonet/libre-chat/main/docs/assets/logo.png"></span> Libre Chat

[![Publish package](https://github.com/vemonet/libre-chat/actions/workflows/publish.yml/badge.svg)](https://github.com/vemonet/libre-chat/actions/workflows/publish.yml) [![Test package](https://github.com/vemonet/libre-chat/actions/workflows/test.yml/badge.svg)](https://github.com/vemonet/libre-chat/actions/workflows/test.yml) [![Coverage Status](https://coveralls.io/repos/github/vemonet/libre-chat/badge.svg?branch=main)](https://coveralls.io/github/vemonet/libre-chat?branch=main)

[![PyPI - Version](https://img.shields.io/pypi/v/libre-chat.svg?logo=pypi&label=PyPI&logoColor=silver)](https://pypi.org/project/libre-chat/)
[![PyPI - Python Version](https://img.shields.io/pypi/pyversions/libre-chat.svg?logo=python&label=Python&logoColor=silver)](https://pypi.org/project/libre-chat/)
[![License](https://img.shields.io/pypi/l/libre-chat)](https://github.com/vemonet/libre-chat/blob/main/LICENSE.txt)

Easily configure and deploy a **fully self-hosted chatbot web service** based on open source Large Language Models (LLMs), such as [Llama 2](https://ai.meta.com/llama/), without the need for knowledge in machine learning or programmation.

</div>

- 🌐 Free and Open Source chatbot web service with UI and API
- 🏡 Fully self-hosted, not tied to any service, and offline capable. Forget about API keys! Models and embeddings can be pre-downloaded, and the training and inference processes can run off-line if necessary.
- 🚀 Easy to setup, no need to program, just configure the service with a [YAML](https://yaml.org/) file, and start it with 1 command
- 📦 Available as a `pip` package 🐍, or `docker` image 🐳
- ⚡ No need for GPU, this will work even on your laptop CPU (but can take up to 1min to answer on recent laptops, works better on a server)
- 🦜 Use [`LangChain`](https://python.langchain.com) to support performant open source models inference: all **Llama 2 GGML** ([7B](https://huggingface.co/TheBloke/Llama-2-7B-Chat-GGML) | [13B](https://huggingface.co/llamaste/Llama-2-13b-chat-hf) | [70B](https://huggingface.co/llamaste/Llama-2-70b-chat-hf)), all **Llama 2 GPTQ** ([7B](https://huggingface.co/TheBloke/Llama-2-7B-chat-GPTQ) | [13B](https://huggingface.co/TheBloke/Llama-2-13B-chat-GPTQ) | [70B](https://huggingface.co/TheBloke/Llama-2-70B-chat-GPTQ))
- 🤖 Various types of agents can be deployed:
  - **💬 Generic conversation**: do not need any additional training, just configure settings such as the template prompt
  - **📚 Documents-based question answering**: automatically build similarity vectors from locally provided PDF documents, the chatbot will use them to answer your question, and return which documents were used to generate the answer.
- 🪶 Modern and lightweight chat web interface, working as well on desktop as on mobile, with support for light/dark theme


![UI screenshot](https://raw.github.com/vemonet/libre-chat/main/docs/assets/screenshot.png)

![UI screenshot](https://raw.github.com/vemonet/libre-chat/main/docs/assets/screenshot-light.png)

> ⚠️ Development on this project has just started, use it with caution

## 📖 Documentation

For more details on how to use Libre Chat check the documentation at **[vemonet.github.io/libre-chat](http://vemonet.github.io/libre-chat)**

## 🐳 Deploy with docker

If you just want to quickly deploy it using the pre-trained model `Llama-2-7B-Chat-GGML`, you can use docker:

```bash
docker run -it -p 8000:8000 ghcr.io/vemonet/libre-chat:main
```

You can configure the deployment using environment variables. For this using a `docker compose` and a `.env` file is easier, first create the `docker-compose.yml` file:

```yaml
version: "3"
services:
  libre-chat:
    image: ghcr.io/vemonet/libre-chat:main
    volumes:
    - ./chat.yml:/app/chat.yml
    ports:
      - 8000:8000
```

And create a `chat.yml` file with your configuration in the same folder as the `docker-compose.yml`:

```yaml
llm:
  model_type: llama
  model_path: ./models/llama-2-7b-chat.ggmlv3.q3_K_L.bin
  # We recommend to predownload the files, but you can provide download URLs that will be used if the files are not present
  model_download: https://huggingface.co/TheBloke/Llama-2-7B-Chat-GGML/resolve/main/llama-2-7b-chat.ggmlv3.q3_K_L.bin
  temperature: 0.01
  max_new_tokens: 256
template:
  # Always use input for the human input variable with a generic agent
  variables: [input, history]
  prompt: |
    Your are an assistant, please help me

    {history}
    Human: {input}
    Assistant:
vector:
  vector_path: null # Path to the vectorstore to do QA retrieval, e.g. ./vectorstore/db_faiss
  # Set to null to deploy a generic conversational agent
  vector_download: null
  embeddings_path: ./embeddings/all-MiniLM-L6-v2 # Embeddings used to generate the vectors
  # You can also directly use embeddings model from HuggingFace:
  # embeddings_path: sentence-transformers/all-MiniLM-L6-v2
  embeddings_download: https://public.ukp.informatik.tu-darmstadt.de/reimers/sentence-transformers/v0.2/all-MiniLM-L6-v2.zip
  documents_path: ./documents # For documents to vectorize
  return_source_documents: true
  vector_count: 2
  chunk_size: 500
  chunk_overlap: 50
info:
  title: "Libre Chat"
  version: "0.1.0"
  description: |
    Open source and free chatbot powered by [LangChain](https://python.langchain.com) and [Llama 2](https://ai.meta.com/llama)
  examples:
  - "What is the capital of the Netherlands?"
  - "How can I create a logger with timestamp using python logging?"
  contact:
    name: "Vincent Emonet"
    email: "vincent.emonet@gmail.com"
  license_info:
    name: "MIT license"
    url: "https://raw.github.com/vemonet/libre-chat/main/LICENSE.txt"
```

Finally start your chat service with:

```bash
docker compose up
```

## 📦️ Usage with pip

This package requires Python >=3.8, simply install it with `pipx` or `pip`:

```bash
pip install libre-chat
```

### ⌨️ Use as a command-line interface

You can easily start a new chat web service including UI and API using your terminal:

```bash
libre-chat start
```

Provide a specific config file:

```bash
libre-chat start config/chat-vectorstore-qa.yml
```

For re-build of the vectorstore:

```bash
libre-chat build --vector vectorstore/db_faiss --documents documents
```

Get a full rundown of the available options with:

```bash
libre-chat --help
```

### 🐍 Use with python

Or you can use this package in python scripts:

 ```python
import logging

import uvicorn
from libre_chat import ChatConf, ChatEndpoint, Llm

logging.basicConfig(level=logging.getLevelName("INFO"))
conf = ChatConf(
	model_path="models/llama-2-7b-chat.ggmlv3.q3_K_L.bin",
    vector_path=None
)
llm = Llm(conf=conf)
print(llm.query("What is the capital of the Netherlands?"))

# Create and deploy a FastAPI app based on your LLM
app = ChatEndpoint(llm=llm, conf=conf)
uvicorn.run(app)
 ```

## 🧑‍💻 Development setup

The final section of the README is for if you want to run the package in development. Feel free to contribute!


### 📥️ Clone

Clone the repository:

```bash
git clone https://github.com/vemonet/libre-chat
cd libre-chat
```
### 🐣 Install dependencies

Install [Hatch](https://hatch.pypa.io), this will automatically handle virtual environments and make sure all dependencies are installed when you run a script in the project:

```bash
pipx install hatch
```

Download pre-trained model and embeddings for local development:

```bash
tests/download.sh
```

### 🛩️ Run dev API

```bash
hatch run dev
```

### ☑️ Run tests

Make sure the existing tests still work by running the test suite and linting checks. Note that any pull requests to the repository on github will automatically trigger running of the test suite;

```bash
hatch run test
```

To display all logs when debugging:

```bash
hatch run test -s
```

You can also run the tests on multiple python versions:

```bash
hatch run all:test
```


### 📖 Generate documentation

The documentation is automatically generated from the markdown files in the `docs` folder and python docstring comments, and published by a GitHub Actions workflow.

Start the docs on [http://localhost:8001](http://localhost:8001)

```bash
hatch run docs
```

### ♻️ Reset the environment

In case you are facing issues with dependencies not updating properly you can easily reset the virtual environment with:

```bash
hatch env prune
```

Manually trigger installing the dependencies in a local virtual environment:

```bash
hatch -v env create
```

## 🤝 Credits

Inspired by:

- https://github.com/kennethleungty/Llama-2-Open-Source-LLM-CPU-Inference
- https://github.com/liltom-eth/llama2-webui

<a href="https://www.flaticon.com/free-icons/llama" title="llama icons">Llama icons created by Freepik - Flaticon</a>

## 📋 To do

- [ ] Try with 70B model
- [ ] Speed up inference, better use of GPUs
- [ ] Add support for returning sources in UI when using documents-based QA
- [ ] Add authentication mechanisms (OAuth/OpenID Connect)
- [ ] Improve handling of chat history, which is broken when using multiple workers, and unsafe when having multiple users (couple it with authentication)
