Create a `chat.yml` file with your configuration before starting the web service.

Below is an example of configuration using the Llama 2 7B GGML model, without vectorstore, to deploy a **generic conversational chatbot**

```yaml title="chat.yml"
llm:
  model_type: llama
  model_path: ./models/llama-2-7b-chat.ggmlv3.q3_K_L.bin # We recommend to predownload the files, but you can provide download URLs that will be used if the files are not present:
  model_download: https://huggingface.co/TheBloke/Llama-2-7B-Chat-GGML/resolve/main/llama-2-7b-chat.ggmlv3.q3_K_L.bin
  temperature: 0.01    # Config how creative, but also potentially wrong, the model can be. 0 is safe, 1 is adventurous
  max_new_tokens: 1024 # Max number of words the LLM can generate

prompt:
  # Always use input for the human input variable with a generic agent
  variables: [input, history]
  template: |
    Your are an assistant, please help me

    {history}
    Human: {input}
    Assistant:

vector:
  vector_path: null # Path to the vectorstore to do QA retrieval, e.g. ./vectorstore/db_faiss
  # Set to null to deploy a generic conversational agent
  vector_download: null
  embeddings_path: ./embeddings/all-MiniLM-L6-v2 # Path to embeddings used to generate the vectors, or use directly from HuggingFace: sentence-transformers/all-MiniLM-L6-v2
  embeddings_download: https://public.ukp.informatik.tu-darmstadt.de/reimers/sentence-transformers/v0.2/all-MiniLM-L6-v2.zip
  documents_path: ./documents # Path to documents to vectorize
  chunk_size: 500             # Maximum size of chunks, in terms of number of characters
  chunk_overlap: 50           # Overlap in characters between chunks
  chain_type: stuff           # Or: map_reduce, reduce, map_rerank. More details: https://docs.langchain.com/docs/components/chains/index_related_chains
  search_type: similarity     # Or: similarity_score_threshold, mmr. More details: https://python.langchain.com/docs/modules/data_connection/retrievers/vectorstore
  return_sources_count: 2     # Number of sources to return when generating an answer
  score_threshold: null       # If using the similarity_score_threshold search type. Between 0 and 1

info:
  title: "Libre Chat"
  version: "0.1.0"
  description: |
    Open source and free chatbot powered by [LangChain](https://python.langchain.com) and [Llama 2](https://ai.meta.com/llama) [7B](https://huggingface.co/TheBloke/Llama-2-7B-Chat-GGML)
  examples:
  - What is the capital of the Netherlands?
  - Which drugs are approved by the FDA to mitigate Alzheimer symptoms?
  - How can I create a logger with timestamp using python logging?
  favicon: https://raw.github.com/vemonet/libre-chat/main/docs/docs/assets/logo.png
  repository_url: https://github.com/vemonet/libre-chat
  public_url: https://chat.semanticscience.org
  contact:
    name: Vincent Emonet
    email: vincent.emonet@gmail.com
  license_info:
    name: MIT license
    url: https://raw.github.com/vemonet/libre-chat/main/LICENSE.txt
  workers: 4
```