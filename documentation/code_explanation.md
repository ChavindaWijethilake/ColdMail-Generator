# 📂 Code Explanation

This document provides a detailed breakdown of the codebase for the Cold Email Generator application.

## 1. `app/main.py`
**Purpose**: The entry point of the Streamlit application. It handles the user interface and coordinates the flow between the scraper, portfolio manager, and LLM chains.

### Key Functions
-   `create_streamlit_app(llm, portfolio, clean_text)`:
    -   Sets up the Streamlit UI (title, input field, submit button).
    -   **Input**: Takes a URL from the user.
    -   **Process**:
        1.  Loads content from the URL using `WebBaseLoader`.
        2.  Cleans the text using `clean_text`.
        3.  Loads the portfolio into ChromaDB via `portfolio.load_portfolio()`.
        4.  Extracts job details using `llm.extract_jobs()`.
        5.  Iterates through jobs, queries relevant portfolio links, and generates an email using `llm.write_mail()`.
    -   **Output**: Displays the generated email as markdown code.

## 2. `app/chains.py`
**Purpose**: Manages interactions with the Groq LLM (Llama 3.3). It defines the prompt templates and chains for job extraction and email writing.

### Class: `Chain`
-   `__init__`: Initializes the `ChatGroq` client with the `llama-3.3-70b-versatile` model.
-   `extract_jobs(cleaned_text)`:
    -   **Input**: Cleaned text from the career page.
    -   **Prompt**: Instructs the LLM to extract job postings into a specific JSON format (`role`, `experience`, `skills`, `description`).
    -   **Output**: A list of dictionaries containing job details.
-   `write_mail(job, links)`:
    -   **Input**: A job description dictionary and a list of relevant portfolio links.
    -   **Prompt**: Instructs the LLM to write a cold email as "Chavinda Wijethilake", highlighting relevant skills and portfolio items.
    -   **Output**: A string containing the generated cold email.

## 3. `app/portfolio.py`
**Purpose**: Handles the storage and retrieval of portfolio items using a vector database (ChromaDB). This allows for semantic matching of job skills to portfolio projects.

### Class: `Portfolio`
-   `__init__`: Reads `resource/portfolio.csv` and initializes the ChromaDB client and collection.
-   `load_portfolio()`:
    -   Checks if the collection is empty.
    -   If empty, it reads the CSV and adds documents (Techstack) and metadata (Links) to the ChromaDB collection.
-   `query_links(skills)`:
    -   **Input**: A list of skills (e.g., ["Python", "React"]).
    -   **Process**: Queries the ChromaDB collection for the 2 most relevant portfolio items matching the skills.
    -   **Output**: A list of metadata (links) corresponding to the matched items.

## 4. `app/utils.py`
**Purpose**: Utility functions for data processing.

### Key Functions
-   `clean_text(text)`:
    -   **Input**: Raw HTML text scraped from the website.
    -   **Process**:
        -   Removes HTML tags.
        -   Removes URLs and special characters.
        -   Normalizes whitespace (single spaces).
    -   **Output**: Clean, plain text suitable for LLM processing.

## 5. `app/resource/portfolio.csv`
**Purpose**: The data source for the user's portfolio.
-   **Columns**:
    -   `Techstack`: Keywords describing the technologies used (e.g., "React, Node.js").
    -   `Links`: The URL to the portfolio item or case study.

## 6. `app/requirements.txt`
**Purpose**: Lists all Python dependencies required to run the project.
-   `streamlit`: Web framework.
-   `langchain-groq`: Groq integration for LangChain.
-   `chromadb`: Vector database.
-   `pandas`: Data manipulation.
