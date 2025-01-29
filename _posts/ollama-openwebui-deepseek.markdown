---
layout: post
title: "Unleashing AI Power at Home: Ollama, Open WebUI, and DeepSeek-R1"
date: 2025-01-29 12:00:00 -0500
categories: ai tutorial
---

I don't know anyone who isn't talking about [DeepSeek](https://www.deepseek.com/) right now. If you'd like to try it for yourself, then you can use the app - it's overtaken ChatGPT to become the #1 free app in the App Stores. But why not run it locally, on your own system?
This guide will walk you through setting up two essential components: [Ollama](https://ollama.com/) and [Open WebUI](https://openwebui.com/), introduce the DeepSeek project, and explore the benefits of running AI locally.

## Installing Ollama on Your System

Ollama makes it easy for users to run large language models (LLMs) like DeepSeek locally. To install it:

1. **Visit the [Ollama](https://ollama.com/) website**.
2. **Download Ollama**: Follow the instructions provided on their website to download and install the application.
3. **Verify Installation**: Open a terminal or command prompt and type 

```bash
ollama --version
```

to ensure it's installed correctly.

## Installing DeepSeek-R1

To use the DeepSeek-R1 model, Ddownload it from [DeepSeek-R1](https://ollama.com/library/deepseek-r1) in Ollama's library.

## Variety of Models Available

Ollama offers a variety of models ranging from 1.5 billion to 671 billion parameters. For most systems, including modern MacBook chips, the 7 billion parameter model works well and provides a balanced performance.

## Installing Open WebUI

**Open WebUI** provides an intuitive interface for interacting with your LLMs. You can set it up using either traditional installation methods or by using containerization tools like [Docker](https://www.docker.com/) and [Podman](https://podman-desktop.io/).

### Traditional Installation

1. **Clone the Repository**: Use Git to clone the Open WebUI repository:

    ```bash
    git clone https://github.com/ollama/open-webui.git
    ```

1. **Install Dependencies** : Navigate into the cloned directory and install necessary dependencies with:

    ```bash
    cd open-webui && npm install
    ```

1. Run the Server : Start the server using:

```bash
npm start
```

### Containerized Installation

For a more streamlined setup, you can use Docker or Podman:

#### Using Docker

1. **Install Docker** from the [official website](https://www.docker.com/).
1. **Pull and Run the Image**:

```bash
docker pull ollama/open-webui:latest
docker run -d --name open-webui -p 3000:3000 ollama/open-webui:latest
```

#### Using Podman

1. **Install Podman** from the [official website]((https://podman-desktop.io/)).
1. **Pull and Run the Image**:
```bash
podman pull ollama/open-webui:latest
podman run -d --name open-webui -p 3000:3000 ollama/open-webui:latest
```

Access Open WebUI through your web browser at `http://localhost:3000`.

## What is DeepSeek?

DeepSeek is a cutting-edge project aimed at enhancing LLM capabilities. The DeepSeek-R1 model represents advancements in AI, focusing on robustness and scalability. The best exlainer I've seen is provided on YouTube by [Dave's Garage](https://youtu.be/r3TpcHebtxM?si=LoREJDPKeyFtQGyy). In short, it punches above its weight compared to models that cost 100x as much, it's cheap and efficient, and it's open source.

### My Favorite Part

DeepSeek is a reasoning model, which means that it thinks before it answers. This is actually my favorite part of using DeepSeek. Here's an example:

First, I gave it a prompt:

![Deepseek prompt](/assets/images/posts/create_blog/deepseek/deepseek1.png)

After that, it thinks:

![Deepseek prompt](/assets/images/posts/create_blog/deepseek/deepseek2.png)

Then, only after thinking about it, it gives me its answer:

![Deepseek prompt](/assets/images/posts/create_blog/deepseek/deepseek3.png)

I really enjoy this aspect, as I now know *how* it arrived at its answer, which then lets me know if I need to re-prompt.

## Benefits of Running AI Locally

By installing Ollama and Open WebUI, you gain transparency into the operations of models like DeepSeek-R1, allowing for customized experiments and privacy-focused applications.

Explore the exciting potential of local AI with ease and flexibility. Happy experimenting!
