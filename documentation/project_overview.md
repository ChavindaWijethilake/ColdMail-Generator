# 🚀 Project Overview: Cold Email Generator

## Scope & Depth
The **Cold Email Generator** is a niche automation tool designed for freelance developers and software engineering interns to optimize their job application process.

### **What it does:**
-   **Automation**: Replaces the manual process of reading long job descriptions and drafting custom emails.
-   **Personalization**: Uses AI to understand the *specific needs* of a job posting and tailors the pitch accordingly.
-   **Evidence-Based**: Unlike generic AI writers, this tool searches a **real portfolio** to find relevant proof of work (case studies, Github links) to back up the claims in the email.

### **Limitations:**
-   **Single URL**: Currently processes one URL at a time.
-   **Static Portfolio**: The portfolio is loaded from a CSV file; updating it requires modifying the file and restarting the app (or clearing the database).
-   **Text-Based**: Relies on the text content of the webpage; complex JavaScript-rendered sites might require more advanced scraping (Selenium/Playwright) if `WebBaseLoader` fails.

## 🏗️ System Architecture

The system follows a linear pipeline data flow:

```mermaid
flowchart LR
    subgraph Frontend
    A[User Input (URL)] --> B[Streamlit UI]
    B --> C[Display Generated Email]
    end

    subgraph "Data Processing"
    B --> D[Web Scraper]
    D --> E[Text Cleaner]
    end

    subgraph "AI Core"
    E --> F[LLM: Job Extraction]
    F --> G{Job Details Identified?}
    G -- Yes --> H[Query Vector DB]
    end

    subgraph "Knowledge Base"
    I[Portfolio CSV] --> J[ChromaDB Collection]
    J -- Relevant Links --> H
    end

    subgraph "Generation"
    H --> K[LLM: Email Composition]
    K --> C
    end
```

## 🛠️ Technologies & Frameworks

### 1. **Streamlit (Frontend)**
-   **Why?**: Pure Python web framework. Allows for rapid prototyping of data apps without writing HTML/CSS/JS.
-   **Role**: Handles user input and displays the markdown-formatted email.

### 2. **LangChain (Orchestration)**
-   **Why?**: The industry standard for building LLM applications. It simplifies prompt management ("PromptTemplates") and chaining multiple steps (Extraction -> Query -> Generation).
-   **Role**: Manages the flow of data between the application code and the LLM.

### 3. **Groq (Inference Engine)**
-   **Why?**: Extremely fast inference speed.
-   **Model**: `llama-3.3-70b-versatile`. This is an open-source model by Meta, hosted by Groq. It offers performance comparable to GPT-4 but with much lower latency, making the app feel responsive.

### 4. **ChromaDB (Vector Database)**
-   **Why?**: A lightweight, open-source vector database that runs locally.
-   **Role**: Stores the user's portfolio "Techstacks" as vector embeddings. When a job description mentions a skill (e.g., "React"), ChromaDB finds the portfolio items that are semantically closest to "React".

### 5. **Pandas**
-   **Role**: Used to load and manipulate the structured data from `portfolio.csv` before ingesting it into the vector database.
