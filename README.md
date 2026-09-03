# Local Autonomous RAG Agent & Vector Pipeline (n8n + Qdrant + Google Gemini)

A containerized, enterprise-ready Retrieval-Augmented Generation (RAG) system built with **n8n**, **Qdrant**, and **Google Gemini**. This repository contains a two-part architecture designed to ingest unstructured documents into a local vector database and provide zero-hallucination, agentic QA over private company data.

![n8n](https://img.shields.io/badge/n8n-Workflow%20Automation-FF6D5A?style=flat&logo=n8n)
![Qdrant](https://img.shields.io/badge/Qdrant-Vector%20Database-DC2626?style=flat&logo=qdrant)
![Google Gemini](https://img.shields.io/badge/Google%20Gemini-LLM%20%26%20Embeddings-8E75B2?style=flat&logo=googlegemini)
![Docker](https://img.shields.io/badge/Docker-Containerized-2496ED?style=flat&logo=docker)
![License](https://img.shields.io/badge/License-MIT-green.svg)

---

## 📐 System Architecture

Unlike linear prompt chains that suffer from context window saturation and hallucinations, this system decouples document processing from conversation state using a two-pipeline vector architecture:

<img width="1568" height="821" alt="image" src="https://github.com/user-attachments/assets/9f2efc1a-1565-439c-83dc-4f7553ca28ea" />

## 🔥 Key Architectural Features

### 1. Synchronous Ingestion Pipeline
* **Binary Processing:** Fetches and holds binary document data in memory prior to vectorization.
* **Context-Aware Chunking:** Utilizes recursive text splitting (1,000-character chunk size with 200-character overlap) to prevent context fragmentation across chunk boundaries.
* **Vector Vectorization:** Generates high-dimensional vector representations via Google's `embedding-001` model.
* **Persistent Vector Storage:** Stores mathematical embeddings inside a self-hosted, Rust-based Qdrant vector database.

### 2. Stateful Retrieval Agent
* **Autonomous Tool Calling:** An n8n AI Agent dynamically evaluates incoming user queries and determines when to invoke the Qdrant retrieval engine.
* **Window Buffer Memory:** Maintains conversational state across dialogue turns.
* **Zero-Hallucination Guardrails:** Forces the LLM to ground its responses exclusively in context retrieved from Qdrant similarity searches.

---

## 🛠️ Infrastructure Requirements

* **Docker & Docker Compose**
* **n8n** (Self-hosted v1.0+)
* **Qdrant Vector Database** (Self-hosted container)
* **Google Gemini API Key** (Embeddings & Chat Model)

---

## 🚀 Quickstart & Setup Guide

### Step 1: Deploy Qdrant in Docker

Run the following command to provision a local Qdrant instance with persistent volume storage:

```bash
docker run -d -p 6333:6333 -p 6334:6334 \
  -v qdrant_storage:/qdrant/storage \
  qdrant/qdrant
