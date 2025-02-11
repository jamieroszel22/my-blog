---
layout: post
title: "Unleashing AI Power at Home: Ollama, Open WebUI, and DeepSeek-R1"
date: 2025-01-29 9:00:00 -0500
categories: Tutorial
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

Install Docker from the official [website](https://www.docker.com/).
Pull and Run the Image:

 ```bash
 docker pull ollama/open-webui:latest
 docker run -d --name open-webui --add-host host.docker.internal:host-gateway -p 3000:3000 ollama/open-webui:latest
 ```

#### Using Podman

Install Podman from the official [website](https://podman-desktop.io/).
Pull and Run the Image:

 ```bash
 podman pull ollama/open-webui:latest
 podman run -d --name open-webui --network slirp4netns:allow_host_loopback=true -p 3000:3000 ollama/open-webui:latest
 ```

*Note*: The networking flags (`--add-host` for Docker and `--network` for Podman) are essential as they allow the Open WebUI container to communicate with Ollama running on your host system (port `11434`). Without these flags, the container won't be able to connect to Ollama.

Access Open WebUI through your web browser at `http://localhost:3000`.

## What is DeepSeek?

DeepSeek is a cutting-edge project aimed at enhancing LLM capabilities. The DeepSeek-R1 model represents advancements in AI, focusing on robustness and scalability. The best exlainer I've seen is provided on YouTube by [Dave's Garage](https://youtu.be/r3TpcHebtxM?si=LoREJDPKeyFtQGyy). In short, it punches above its weight compared to models that cost 100x as much, it's cheap and efficient, and it's open source.

### My Favorite Part

DeepSeek is a reasoning model, which means that it thinks before it answers. This is actually my favorite part of using DeepSeek. Here's an example:

First, I gave it a prompt:

```text
Tell me about the age of the earth.
```

After that, it thinks:

```text
Okay, so I need to figure out how old the Earth is. I remember hearing something about it being around 4.5 billion years old, but I'm not exactly sure where that number comes from or what it means. Let's start by thinking about what I know.

First off, the Earth was formed when a bunch of materials collided and fused together, right? That process is called planetary formation. But how do we determine its age? I think it has something to do with radioactive isotopes because they decay over time at a constant rate. So, if we can find out which isotopes were present when the Earth was formed and compare them now, we might be able to calculate its age.

Wait, but what isotopes are used for this? I remember something about uranium decaying into lead or something like that. If we have rocks with uranium and lead in them, maybe we can use the decay of uranium-238 to determine how old the rock is. But wait, not all rocks are the same age, right? Some are older than others because they might have been formed later when new materials were added.

Oh, I think there's something called zirconium minerals that form in lava flows and can be used as a reference for the Earth's age. Those minerals supposedly crystallize quickly so they don't get contaminated with newer materials from the mantle. So maybe the oldest zirconium found on Earth is a good indicator of how old the planet is.

But wait, I'm not sure if zirconium's age is exactly 4.5 billion years or just used as an anchor point. Maybe it's more like the oldest zircon found is around that age. Also, what about other isotopes? Like rubidium-strontium dating or something else?
```

Then, only after thinking about it, it gives me its answer:

```text
The Earth's age is determined through various scientific methods primarily involving radioactive isotopes found in minerals such as zircon. Here's a structured summary of the process and conclusion:

Planetary Formation : The Earth formed from a collision and fusion of celestial materials, a process known as planetary formation.
Radioactive Isotopes : The decay of radioactive isotopes over time is used to determine age. For example:
Uranium-238 decays into lead.
Rubidium-strontium dating and other methods are also employed.
Reference Minerals : Zircon minerals, found in lava flows, are considered reliable for Earth's age determination as they crystallize quickly and avoid contamination from the mantle.
Independent Methods : Various methods like uranium-lead dating and Oklo nuclear plant (a 2 billion year-old zircon) provide cross-checks, ensuring consistency around a similar age.
Age Determination : The oldest zircon found on Earth indicates the planet's age to be approximately 4.5 billion years. Independent isotopic analyses corroborate this figure with minor variations but consistent within a range.
Considerations : Variations between methods are accounted for by considering planetary evolution and mixing of materials over time.
Conclusion : The scientific consensus, supported by multiple methods, places the Earth's age at around 4.5 billion years old, with some methods slightly differing in specifics but all converging on this figure.
This conclusion is derived from a thorough examination of isotopic decay processes and their application to dating minerals within ancient geological formations.
```

I really enjoy this aspect, as I now know *how* it arrived at its answer, which then lets me know if I need to re-prompt.

## Benefits of Running AI Locally

By installing Ollama and Open WebUI, you gain transparency into the operations of models like DeepSeek-R1, allowing for customized experiments and privacy-focused applications.

Explore the exciting potential of local AI with ease and flexibility. Happy experimenting!
