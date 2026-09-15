# Demo: RAG in n8n

This README contains the n8n workflow JSON for a Retrieval-Augmented
Generation (RAG) demo using:

-   **n8n**
-   **Ollama Chat Model**
-   **Qwen3 Embeddings (`qwen3-embedding:0.6b`)**
-   **In-memory vector store**
-   **AI Agent**
-   PDF and CSV file uploads

## Workflow Overview

### Load Data Flow

1.  Upload PDF or CSV files using the n8n form trigger.
2.  The **Default Data Loader** reads the binary files.
3.  **Qwen3 Embeddings** converts document content into embeddings.
4.  **Insert Data to Store** stores the embedded content in the
    in-memory vector store.

### Retriever Flow

1.  A user submits a question through the chat trigger.
2.  The **AI Agent** receives the question.
3.  The **Query Data Tool** searches the vector store.
4.  The same **Qwen3 Embeddings** model is used for retrieval.
5.  The **Ollama Chat Model** generates an answer using the retrieved
    context.

> **Important:** The workflow uses the same embedding model for both
> inserting and retrieving data. Keeping the embedding model and
> settings consistent is important for reliable vector search.

## Quick Start

1.  Import the workflow JSON into n8n.
2.  Configure the **Ollama** credential.
3.  Make sure the `qwen3-embedding:0.6b` model is available in Ollama.
4.  Execute the **Upload your file here** flow and upload a PDF or CSV.
5.  Open the chat interface.
6.  Ask questions about the uploaded documents.

## n8n RAG Documentation

For additional information, see the [n8n RAG
documentation](https://docs.n8n.io/advanced-ai/rag-in-n8n/).

## Workflow JSON

``` json
{
  "name": "Demo: RAG in n8n",
  "nodes": [
    {
      "parameters": {
        "formTitle": "Upload your data to test RAG",
        "formFields": {
          "values": [
            {
              "fieldLabel": "Upload your file(s)",
              "fieldType": "file",
              "acceptFileTypes": ".pdf, .csv",
              "requiredField": true
            }
          ]
        },
        "options": {}
      },
      "type": "n8n-nodes-base.formTrigger",
      "typeVersion": 2.2,
      "position": [-128, 0],
      "id": "f7a656ec-83fc-4ed2-a089-57a9def662b7",
      "name": "Upload your file here",
      "webhookId": "82848bc4-5ea2-4e5a-8bb6-3c09b94a8c5d"
    },
    {
      "parameters": {
        "dataType": "binary",
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.documentDefaultDataLoader",
      "typeVersion": 1.1,
      "position": [320, 160],
      "id": "94aecac0-03f9-4915-932b-d14a2576607b",
      "name": "Default Data Loader"
    },
    {
      "parameters": {
        "content": "### Readme\nLoad your data into a vector database with the 📚 **Load Data** flow, and then use your data as chat context with the 🐕 **Retriever** flow.\n\n**Quick start**\n1. Click on the `Execute Workflow` button to run the 📚 **Load Data** flow.\n2. Click on `Open Chat` button to run the 🐕 **Retriever** flow. Then ask a question about content from your document(s)\n\n\nFor more info, check [our docs on RAG in n8n](https://docs.n8n.io/advanced-ai/rag-in-n8n/).",
        "height": 300,
        "width": 440,
        "color": 4
      },
      "type": "n8n-nodes-base.stickyNote",
      "position": [-656, -64],
      "typeVersion": 1,
      "id": "0d07742b-0b36-4c2e-990c-266cbe6e2d4d",
      "name": "Sticky Note"
    },
    {
      "parameters": {
        "content": "### 📚 Load Data Flow",
        "height": 460,
        "width": 700,
        "color": 7
      },
      "type": "n8n-nodes-base.stickyNote",
      "position": [-224, 832],
      "typeVersion": 1,
      "id": "d19d04f3-5231-4e47-bed7-9f24a4a8a4a8a4",
      "name": "Sticky Note1"
    },
    {
      "parameters": {
        "mode": "insert",
        "memoryKey": {
          "__rl": true,
          "value": "vector_store_key",
          "mode": "list",
          "cachedResultName": "vector_store_key"
        }
      },
      "type": "@n8n/n8n-nodes-langchain.vectorStoreInMemory",
      "typeVersion": 1.2,
      "position": [64, 0],
      "id": "bf50a11f-ca6a-4e04-a6d2-42fee272b260",
      "name": "Insert Data to Store"
    },
    {
      "parameters": {
        "mode": "retrieve-as-tool",
        "toolName": "knowledge_base",
        "toolDescription": "Use this knowledge base to answer questions from the user",
        "memoryKey": {
          "__rl": true,
          "value": "vector_store_key",
          "mode": "list",
          "cachedResultName": "vector_store_key"
        }
      },
      "type": "@n8n/n8n-nodes-langchain.vectorStoreInMemory",
      "typeVersion": 1.2,
      "position": [944, 208],
      "id": "09c0db62-5413-440e-8c13-fb6bb66d9b6a",
      "name": "Query Data Tool"
    },
    {
      "parameters": {
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.agent",
      "typeVersion": 2,
      "position": [944, -16],
      "id": "579aed76-9644-42d1-ac13-7369059ff1c2",
      "name": "AI Agent"
    },
    {
      "parameters": {
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.chatTrigger",
      "typeVersion": 1.1,
      "position": [720, -16],
      "id": "9c30de61-935a-471f-ae88-ec5f67beeefc",
      "name": "When chat message received",
      "webhookId": "4091fa09-fb9a-4039-9411-7104d213f601"
    },
    {
      "parameters": {
        "content": "### 🐕 2. Retriever Flow",
        "height": 460,
        "width": 680,
        "color": 7
      },
      "type": "n8n-nodes-base.stickyNote",
      "position": [608, -64],
      "typeVersion": 1,
      "id": "28bc73a1-e64a-47bf-ac1c-ffe644894ea5",
      "name": "Sticky Note2"
    },
    {
      "parameters": {
        "content": "### Embeddings\n\nThe Insert and Retrieve operation use the same embedding node.\n\nThis is to ensure that they are using the **exact same embeddings and settings**.\n\nDifferent embeddings might not work at all, or have unintended consequences.\n",
        "height": 240,
        "width": 320,
        "color": 4
      },
      "type": "n8n-nodes-base.stickyNote",
      "position": [672, 448],
      "typeVersion": 1,
      "id": "0cf8c647-418c-4d1a-8952-766145afca72",
      "name": "Sticky Note3"
    },
    {
      "parameters": {
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.lmChatOllama",
      "typeVersion": 1,
      "position": [816, 192],
      "id": "16cf5bc1-bffd-4029-8d35-d764f4b62007",
      "name": "Ollama Chat Model",
      "credentials": {
        "ollamaApi": {
          "id": "Uj2JKcKxgcy6P8t4",
          "name": "Ollama account"
        }
      }
    },
    {
      "parameters": {
        "model": "qwen3-embedding:0.6b"
      },
      "type": "@n8n/n8n-nodes-langchain.embeddingsOllama",
      "typeVersion": 1,
      "position": [448, 448],
      "id": "bbcfe36a-0985-4f26-b5f1-48a28094f070",
      "name": "Embeddings Ollama",
      "credentials": {
        "ollamaApi": {
          "id": "Uj2JKcKxgcy6P8t4",
          "name": "Ollama account"
        }
      }
    }
  ],
  "pinData": {},
  "connections": {
    "Upload your file here": {
      "main": [[{"node": "Insert Data to Store", "type": "main", "index": 0}]]
    },
    "Default Data Loader": {
      "ai_document": [[{"node": "Insert Data to Store", "type": "ai_document", "index": 0}]]
    },
    "Query Data Tool": {
      "ai_tool": [[{"node": "AI Agent", "type": "ai_tool", "index": 0}]]
    },
    "When chat message received": {
      "main": [[{"node": "AI Agent", "type": "main", "index": 0}]]
    },
    "Ollama Chat Model": {
      "ai_languageModel": [[{"node": "AI Agent", "type": "ai_languageModel", "index": 0}]]
    },
    "Embeddings Ollama": {
      "ai_embedding": [[
        {"node": "Query Data Tool", "type": "ai_embedding", "index": 0},
        {"node": "Insert Data to Store", "type": "ai_embedding", "index": 0}
      ]]
    }
  },
  "active": false,
  "settings": {
    "executionOrder": "v1",
    "binaryMode": "separate"
  },
  "versionId": "c4cf680f-2a59-4438-ac9c-ae9f267f313b",
  "meta": {
    "templateId": "rag-starter-template",
    "templateCredsSetupCompleted": true,
    "instanceId": "90c3529ffd720cd872eee30b94231081871f01173ab7de4454af50aba4a2a21f"
  },
  "nodeGroups": [],
  "id": "SrgGqbgwnG20RlS9",
  "tags": []
}
```
