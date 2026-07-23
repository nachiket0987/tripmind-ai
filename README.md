# TripMind AI — Intelligent Multi-Agent Travel Planner

![Python](https://img.shields.io/badge/Python-3.10%2B-blue?style=flat-square&logo=python)
![OpenAI](https://img.shields.io/badge/OpenAI-GPT--3.5--Turbo-412991?style=flat-square&logo=openai)
![Streamlit](https://img.shields.io/badge/UI-Streamlit-FF4B4B?style=flat-square&logo=streamlit)
![Docker](https://img.shields.io/badge/Docker-Containerized-2496ED?style=flat-square&logo=docker)
![AWS](https://img.shields.io/badge/AWS-EC2%20%7C%20ECR-FF9900?style=flat-square&logo=amazonaws)
![TaskflowAI](https://img.shields.io/badge/Framework-TaskflowAI-green?style=flat-square)

> A production-grade, multi-agent AI system that delivers comprehensive travel destination insights — flights, weather, events, and itineraries — in a single query.

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [System Architecture](#system-architecture)
- [Agents](#agents)
- [Tools](#tools)
- [Application Walkthrough](#application-walkthrough)
- [Deployment](#deployment)
- [Getting Started](#getting-started)
- [Project Structure](#project-structure)
- [Connect](#connect)

---

## Overview

TripMind AI was built to solve a real-world client requirement: provide users with accurate, detailed, and instant travel destination insights embedded directly into a platform — all in a single query.

The system simulates an industry-standard engineering scenario where a modular, scalable AI pipeline was designed and deployed end-to-end on AWS. The framework of choice was **TaskflowAI**, selected for its robust multi-agent orchestration capabilities and support for modular system design.

> **Note:** Background concepts including AI agents, agent pipelines, and RAG (Retrieval-Augmented Generation) systems are documented in the [`docs/`](docs/) folder.

---

## Features

- 🌍 **Destination Research** — Fetches attraction details, images, and contextual information using web and Wikipedia search
- 📅 **Event Discovery** — Finds local events at the destination for specified travel dates
- 🌤️ **Weather Intelligence** — Retrieves real-time weather data including temperature, precipitation, and packing recommendations
- ✈️ **Flight Search** — Identifies top affordable flight options using the Amadeus API
- 📝 **Unified Travel Report** — Aggregates all agent outputs into a single, structured, markdown-formatted travel report
- 🚀 **Production Deployment** — Fully Dockerized and deployed to AWS EC2 via GitHub Actions CI/CD

---

## Tech Stack

| Category | Technology |
|----------|-----------|
| Language | Python 3.10+ |
| Agent Framework | TaskflowAI |
| LLM | OpenAI GPT-3.5 Turbo |
| UI | Streamlit |
| Web Search | Serper API |
| Knowledge Base | Wikipedia API |
| Flight Data | Amadeus API |
| Weather Data | Weather.com API |
| Containerization | Docker |
| Cloud | AWS EC2 + ECR |
| CI/CD | GitHub Actions (Self-Hosted Runner) |

---

## Model Selection

Multiple LLMs were evaluated before finalizing **GPT-3.5 Turbo**:

| Model | Result | Reason |
|-------|--------|--------|
| **GPT-3.5 Turbo** ✅ | Selected | Best tool-calling, token management, and report generation at low cost |
| GPT-4 / o1 Series | Rejected | Excellent performance but prohibitively expensive for this use case |
| LLaMA Models | Rejected | Struggled with large token inputs and multi-agent reasoning |
| Google Models | Rejected | Capable on context length but poor reasoning and memory performance |
| Groq Inference | Rejected | Failed to meet performance benchmarks for this pipeline |

---

## System Architecture

### Main Pipeline

![TripMind AI Main Pipeline](https://github.com/user-attachments/assets/45f1226e-2e3c-4ae7-999f-9ff0db143bc7)

### LLM Configuration (GPT-3.5 Turbo)

![LLM Setup](https://github.com/user-attachments/assets/c33b9de0-e125-4ee7-8fff-67dc952dfe0f)

- The **GPT-3.5 Turbo** model is loaded via `taskflowai.OpenaiModels`
- Cost-effective and performant for multi-agent task orchestration
- API key should be stored securely using a `.env` file

---

## Agents

### Web Research Agent

![Web Research Agent](https://github.com/user-attachments/assets/83e0f923-6e4e-4763-b11d-09980ea09458)

Responsible for conducting web-based research and retrieving relevant destination information and imagery.

- **Role:** Web Research Agent
- **Goal:** Thorough, visually-focused destination research
- **Tools:** SerperSearch, WikiArticles, WikiImages

---

### Travel Agent

![Travel Agent](https://github.com/user-attachments/assets/2d985f64-b996-45a1-9114-534d8969f110)

Handles travel logistics — flight searches and weather data retrieval.

- **Role:** Travel Agent
- **Goal:** Provide flight options and weather conditions for a destination
- **Tools:** SearchFlights (Amadeus API), GetWeatherData (Weather.com API)

---

### Reporter Agent

![Reporter Agent](https://github.com/user-attachments/assets/6928996a-69c2-49f2-af66-911be403e60f)

Aggregates outputs from all agents into a single, comprehensive, visually-rich travel report.

- **Role:** Travel Report Agent
- **Goal:** Generate a structured, markdown-formatted report covering destination details, events, weather, and flights
- **Inputs:** Web Research Agent output + Travel Agent output

---

## Tools

### Wiki Image Search

![Wiki Image Tool](https://github.com/user-attachments/assets/d3e1323f-fb04-47a2-a588-bb4a2ac7f589)

Fetches relevant images from Wikipedia using `taskflowai.WikipediaTools`.

---

### Wiki Article Search

![Wiki Article Tool](https://github.com/user-attachments/assets/e36413f9-799f-41e0-b513-09fec0a69f9a)

Retrieves destination-related articles from Wikipedia for contextual depth.

---

### Serper Web Search

![Serper Tool](https://github.com/user-attachments/assets/2152df64-700d-428f-a86e-597091f7af79)

Performs live web searches using the Serper API via `taskflowai.WebTools`.

---

### Weather Data Fetcher

![Weather Tool](https://github.com/user-attachments/assets/f573e7f3-abf5-4ace-9c81-c0d0abb28be4)

Fetches real-time weather data from the Weather.com API via `taskflowai.WebTools`.

---

### Flight Search

![Flight Tool](https://github.com/user-attachments/assets/39e79100-0d2d-46db-a877-935fc0ce67f3)

Searches available flights using the Amadeus API via `taskflowai.AmadeusTools`, with support for origin, destination, dates, and preference filters.

---

## Application Walkthrough

The `deployment/app.py` file ties all agents and tasks together into a Streamlit UI. Below is a breakdown of each task defined in the app.

### Research Destination Task

![Research Destination](https://github.com/user-attachments/assets/c3a36834-c2d6-49c5-bf30-214399da5f67)

- Uses the **Web Research Agent** to gather destination details and high-quality images
- Output is formatted in clean, structured markdown

### Research Events Task

<img width="753" alt="Research Events Task" src="https://github.com/user-attachments/assets/a39322b5-0473-4e9c-92ea-9ce70874f336" />

- Generates a detailed events report for the destination and specified travel dates
- Includes event name, date, time, venue, ticket info, and embedded images

### Research Weather Task

<img width="699" alt="Research Weather Task" src="https://github.com/user-attachments/assets/328577fe-57fc-4394-98c9-ffc2871d9258" />

- Produces a weather report covering temperature ranges, precipitation, and packing recommendations

### Search Flights Task

<img width="790" alt="Search Flights Task" src="https://github.com/user-attachments/assets/002b296b-fa9b-4fc3-bdfc-b0085f63e0f8" />

- Returns the top 3 affordable and convenient flight options in bullet-point format

### Write Travel Report Task

<img width="730" alt="Write Travel Report Task" src="https://github.com/user-attachments/assets/ee1c64cb-78b4-42e4-86fb-95be412e996c" />

- Aggregates all above task outputs into one comprehensive, image-rich travel report

---

### UI Screenshots (Live Run)

![Screenshot](https://github.com/user-attachments/assets/159316be-1895-4dcb-9925-18e058c90b17)
<img width="958" alt="1" src="https://github.com/user-attachments/assets/ff149519-e5fd-4271-abf0-d5eb9d8fca82" />
<img width="956" alt="2" src="https://github.com/user-attachments/assets/5e13255a-16c7-4510-bdf3-d0385c176034" />
<img width="959" alt="3" src="https://github.com/user-attachments/assets/563c21d2-f15a-4402-9f6c-fea788ad5090" />
<img width="959" alt="4" src="https://github.com/user-attachments/assets/4e5a60dc-c40c-4740-82b8-5d4ff6dc908f" />
<img width="958" alt="5" src="https://github.com/user-attachments/assets/01845f17-4dee-4ebb-bc2f-2bb90bbc7f20" />
<img width="959" alt="6" src="https://github.com/user-attachments/assets/ed802136-072a-4b0b-b705-2d2bd00d7be6" />
<img width="959" alt="7" src="https://github.com/user-attachments/assets/eb1799c5-bbe2-43ce-a1c3-13db53d6af69" />
<img width="959" alt="8" src="https://github.com/user-attachments/assets/26e91312-4b7a-49ef-92df-be60069aea20" />
<img width="959" alt="9" src="https://github.com/user-attachments/assets/72b54321-bc6f-418f-95b3-c980fd05e1fd" />
<img width="954" alt="10" src="https://github.com/user-attachments/assets/52c7f0e3-57a9-4f60-a04c-d97a0e41c15e" />
<img width="958" alt="11" src="https://github.com/user-attachments/assets/8e7bda45-96e8-426d-a558-6af7ab082346" />
<img width="947" alt="12" src="https://github.com/user-attachments/assets/6fc5a251-b140-4e8f-acf4-84dd6e4dbc1e" />
<img width="959" alt="13" src="https://github.com/user-attachments/assets/60c05b81-a9db-45d4-8635-a9c49e646d91" />
<img width="959" alt="14" src="https://github.com/user-attachments/assets/ca81265a-19eb-4a51-8fb5-4af9e5441e1a" />
<img width="957" alt="15" src="https://github.com/user-attachments/assets/e7411c35-41eb-4a22-8998-e23d1e9e95e1" />
<img width="958" alt="16" src="https://github.com/user-attachments/assets/e6d5bce8-b33e-4681-9bae-ea51d11b4f1d" />
<img width="957" alt="17" src="https://github.com/user-attachments/assets/92fa0f85-de82-467e-b8fa-fe252b89c80f" />
<img width="956" alt="18" src="https://github.com/user-attachments/assets/29a63694-5913-40df-8f55-7c99a61f228c" />
<img width="958" alt="19" src="https://github.com/user-attachments/assets/e7adf831-760e-4486-9b2c-74f9f6d6fee3" />
<img width="956" alt="21" src="https://github.com/user-attachments/assets/06043e1d-16ce-44c7-ba68-56d14c35f1ec" />
<img width="959" alt="22" src="https://github.com/user-attachments/assets/fba03c7f-aef3-4f35-96c3-eb64af957ac0" />
<img width="957" alt="23" src="https://github.com/user-attachments/assets/9d4f1e02-8dad-40c8-b946-3fceac5f1158" />
<img width="959" alt="24" src="https://github.com/user-attachments/assets/1c121042-2468-490c-b241-5bad92ed006c" />
<img width="956" alt="25" src="https://github.com/user-attachments/assets/02502c0e-a714-4515-8558-c906d8cdd747" />
<img width="959" alt="26" src="https://github.com/user-attachments/assets/e7312a43-7e5b-4824-ac48-c4ad164e6c77" />
<img width="959" alt="27" src="https://github.com/user-attachments/assets/dfe4c8e3-e70a-410c-bf7d-5054d4a1412b" />
<img width="956" alt="28" src="https://github.com/user-attachments/assets/2a1a099c-b011-45de-9729-61c6b62b6382" />

---

## Deployment

### Deployment Pipeline

![Deployment Pipeline](https://github.com/user-attachments/assets/2aa5d232-3766-4ac1-b48a-eb055ac1f7e7)

The application is containerized with Docker and deployed to **AWS EC2** via a **GitHub Actions self-hosted runner**. The pipeline covers:

1. **Checkout** — Pull the latest `main` branch code
2. **Docker Verify** — Confirm Docker is correctly configured on the runner
3. **Docker Build** — Build the Streamlit app image with required API keys as build args
4. **AWS Auth** — Authenticate to AWS using stored GitHub secrets
5. **Push to ECR** — Tag and push the Docker image to AWS Elastic Container Registry
6. **Deploy to EC2** — Pull the image on EC2, stop any existing container, and run the updated one

#### Prerequisites

- Active AWS account with ECR and EC2 access
- All API keys stored as **GitHub Secrets** (`OPENAI_API_KEY`, `WEATHER_API_KEY`, `SERPER_API_KEY`, `AMADEUS_API_KEY`, `AMADEUS_API_SECRET`)
- EC2 key pair PGP key added to GitHub Secrets for SSH access

> For the full CI/CD configuration, see [`.github/workflows/deploy.yml`](.github/workflows/deploy.yml).  
> Docker and GitHub Self-Hosted Runner setup commands are available in [`scripts.sh`](scripts.sh).  
> To register a new runner: **Repo Settings → Actions → Runners → New Self-Hosted Runner**

### Post-Deployment Screenshots

![EC2 Deploy 1](https://github.com/user-attachments/assets/9e55c346-e864-49b4-b63e-ff677ae3e873)
![EC2 Deploy 2](https://github.com/user-attachments/assets/8d92472d-ea81-49ec-9672-aa2a1de370d8)

---

## Getting Started

### Prerequisites

- Python 3.10 or higher
- Conda (recommended)
- API keys for: OpenAI, Serper, Amadeus, and Weather.com

### Installation

```bash
# Create and activate a virtual environment
conda create -p ./venv python=3.10
conda activate ./venv

# Install dependencies
pip install -r requirements.txt
```

### Environment Variables

Create a `.env` file in the root directory:

```env
OPENAI_API_KEY=your_openai_key
SERPER_API_KEY=your_serper_key
AMADEUS_API_KEY=your_amadeus_key
AMADEUS_API_SECRET=your_amadeus_secret
WEATHER_API_KEY=your_weather_key
```

### Run the Application

```bash
streamlit run deployment/app.py
```

> **Framework Note:** This project uses [TaskflowAI](https://www.taskflowai.org/) for multi-agent orchestration. Its modular design makes it straightforward to extend with additional agents or swap tools. Alternative frameworks such as **LangGraph** and **CrewAI** are also compatible with this architecture.

---

## Project Structure

```bash
tripmind-ai/
├── .github/
│   └── workflows/
│       └── deploy.yml          # CI/CD pipeline for AWS deployment
├── deployment/
│   └── app.py                  # Streamlit UI and task orchestration
├── docs/
│   ├── Agentic RAG Pipeline.md
│   └── Types of Agentic RAG.md
├── flowcharts/
│   └── project_pipeline.jpg
├── notebooks/
│   └── TripPlanner_Multi_AI_Agent_Experimental.ipynb
├── src/agentic/
│   ├── agents/
│   │   ├── reporter_agent.py
│   │   ├── travel_agent.py
│   │   └── web_research_agent.py
│   ├── exception/
│   │   └── __init__.py
│   ├── logger/
│   │   └── __init__.py
│   ├── tools/
│   │   ├── get_weather_data.py
│   │   ├── search_articles.py
│   │   ├── search_flights.py
│   │   ├── search_images.py
│   │   └── serper_search.py
│   └── utils/
│       ├── __init__.py
│       └── main_utils.py
├── .gitignore
├── demo.py
├── README.md
├── requirements.txt
├── scripts.sh
├── setup.py
└── template.py
```

---

## Connect

**Nachiket Gadilohar** — Built with 💙 as a production-grade AI engineering project.

| Platform | Link |
|----------|------|
| 📧 Email | [nachiketlohar0306@gmail.com](mailto:nachiketlohar0306@gmail.com) |
| 🐙 GitHub | [github.com/nachiket0987](https://github.com/nachiket0987) |
| 💼 LinkedIn | [linkedin.com/in/nachiket-gadilohar-profile](https://www.linkedin.com/in/nachiket-gadilohar-profile/) |
