---
layout: post
title:  "Setting Up My Personal AI Writing Assistant: A Local LLM Journey"
date:   2025-01-10 16:48:55 -0500
categories: ai tutorial
---
In an era where AI assistance has become increasingly valuable for content creation and editing, I recently embarked on setting up my own local AI infrastructure. By running language models directly on my hardware, I've created a personalized writing assistant and copy editor that's both private and accessible throughout my home network.

## Installation Deep Dive

The setup process involved several key components, each with its own considerations:

### Ollama Setup

1. First, I installed <a href="https://ollama.ai/" target="_blank" rel="noopener noreferrer">Ollama</a> from their official website. The process was straightforward:
   * Mac: A simple download and drag-to-Applications installation
   * Windows: Running the provided installer, which handled everything including PATH setup

2. Model Installation:

```bash
ollama pull llama2:8b
ollama pull phi
```

The download sizes are pretty substantial - around 4GB for Llama2 and 2.7GB for Phi-4, so patience and storage are necessary. This is why I installed 6 total terabytes of storage on my pc!

### OpenWebUI Integration

Installing <a href="https://openwebui.com/" target="_blank" rel="noopener noreferrer">Open WebUI</a> required a few more steps:

```bash
docker pull ghcr.io/open-webui/open-webui:main
docker run -d -p 3000:8080 --name open-webui -v open-webui:/app/backend/data ghcr.io/open-webui/open-webui:main
```

After installation, connecting OpenWebUI to Ollama just required pointing it to the local Ollama API endpoint (typically `http://localhost:11434`).

## Model Performance Insights

After extensive testing, I've noticed some interesting performance characteristics:

### Llama2 8B

**Strengths:**

* Excellent general writing assistance
* Strong grammar correction capabilities
* Good at maintaining context in longer conversations

**Limitations:**

* Generation speed averages 15-20 tokens per second on my hardware
* Occasionally struggles with complex technical topics
* Memory context window can be limiting for very long documents

### Microsoft Phi-4

**Strengths:**

* Surprisingly capable for its size
* Excellent at code-related tasks
* Faster inference speed compared to Llama2

**Limitations:**

* Sometimes less coherent on very long outputs
* More prone to hallucinations on specific technical topics

## Hardware Requirements and Performance Notes

My setup runs on the following PC configuration:

* CPU: AMD Ryzen 7 9800X3D
* GPU: Geforce RTX 4070 Ti Super
* RAM: 32GB
* Storage: NVMe SSD with 2TB capacity

and also runs on my Macbook Pro with the M2 Pro chip and 32GB of memory.

I've found this configuration handles both models comfortably, though having an NVMe drive and a dedicated GPU makes a noticeable difference in model loading times.

## Challenges and Solutions

The journey wasn't without its hurdles. Here are the main challenges I encountered and how I resolved them:

### Memory Management

I haven't had any issues running models with fewer than 7-9b parameters. 

### Network Configuration Challenges

Setting up network access required addressing:

* Proper port forwarding on the local network
* Security considerations for local API access
* Docker network configuration for OpenWebUI

### Performance Optimization

Several tweaks were necessary to get optimal performance:

* Adjusting model quantization settings in Ollama
* Finding the right balance between context window size and performance
* Implementing proper caching mechanisms for frequently used prompts

## Network Configuration

One of the most satisfying aspects of this setup was configuring my PC to act as a local AI server. By opening up access on my local network, I've created a personal AI infrastructure that's accessible from any device in my house. This means I can:

* Draft blog posts from my tablet while relaxing on the couch
* Get quick edits done from my laptop in the home office
* Access my AI assistant from any device without relying on cloud services

## Benefits of Local LLMs

Running these models locally offers several advantages:

* Complete privacy for my writing and editing process
* No subscription fees or API costs
* Consistent access without internet dependency
* Full control over the model selection and configuration

## Looking Forward

This setup has transformed my writing workflow, providing me with always-available AI assistance while maintaining privacy and control. As newer and more capable models become available for local deployment, I look forward to further enhancing this system.

For those interested in creating a similar setup, I encourage you to explore Ollama and OpenWebUI. While the initial configuration requires some technical comfort, the resulting capability to run powerful language models on your own hardware is well worth the effort.
