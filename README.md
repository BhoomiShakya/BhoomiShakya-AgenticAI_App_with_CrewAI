# CrewAI Medical Research & Blogging Automation

## Overview
This project automates the process of researching and writing medical-related blog posts using **CrewAI** with **Groq AI** and **OpenAI LLMs**. It consists of multiple AI agents performing specialized tasks, such as gathering information from the web and generating well-structured blog articles.

---

## Technologies Used
- **Python** (Core language)
- **CrewAI** (For multi-agent collaboration)
- **LangChain Groq** (For running Llama 3 model)
- **LiteLLM** (Lightweight LLM wrapper for multiple AI models)
- **SerperDevTool** (For web search and fact-checking)
- **Dotenv** (For managing API keys securely)

---

## Installation
To set up and run this project, install the necessary dependencies by running:

```sh
pip install crewai python-dotenv
pip install -U litellm
pip install crewai-tools
pip install langchain-groq
```

---

## Environment Setup
Create a **.env** file and add the following API keys:

```
OPENAI_API_KEY=your_openai_api_key
SERPER_API_KEY=your_serper_api_key
GROQ_API_KEY=your_groq_api_key
```

Make sure to replace `your_openai_api_key`, `your_serper_api_key`, and `your_groq_api_key` with your actual keys.

---

## Project Structure

- **Agents:**
  - `Senior Research Analyst`: Gathers research on the given topic from various sources.
  - `Content Writer`: Converts research data into an engaging blog post.

- **Tasks:**
  - `Research Task`: Collects data, fact-checks, and compiles a structured research report.
  - `Writing Task`: Transforms the research into a formatted blog post with citations.

---

## Code Explanation

### 1. Import Dependencies
The necessary libraries are imported, including CrewAI, LLMs, and SerperDevTool for web searching.

```python
import os
import time
import litellm
from dotenv import load_dotenv
from crewai import Agent, Task, Crew, LLM
from crewai_tools import SerperDevTool
from langchain_groq import ChatGroq
```

### 2. Load API Keys
We load API keys from the `.env` file and ensure they are available.

```python
load_dotenv()
OPENAI_API_KEY = os.getenv("OPENAI_API_KEY")
SERPER_API_KEY = os.getenv("SERPER_API_KEY")
GROQ_API_KEY = os.getenv("GROQ_API_KEY")
```

### 3. Define LLMs
You can use either **OpenAI's GPT-3.5** or **Groq's Llama 3** for processing.

```python
llm = LLM(model="gpt-3.5-turbo", api_key=OPENAI_API_KEY)
# Alternative: Using Groq AI
# llm = ChatGroq(model="llama3-70b-8192", api_key=GROQ_API_KEY)
```

### 4. Define the Search Tool
The `SerperDevTool` is used to fetch online research data.

```python
search_tool = SerperDevTool(api_key=SERPER_API_KEY)
```

### 5. Create AI Agents

#### **Senior Research Analyst**
Responsible for conducting detailed research.

```python
senior_research_analyst = Agent(
    role="Senior Research Analyst",
    goal=f"Research and analyze comprehensive information on {topic}.",
    backstory="AI and ML expert with experience in medical research.",
    tools=[search_tool],
    llm=llm
)
```

#### **Content Writer**
Transforms research into an engaging and well-structured blog post.

```python
content_writer = Agent(
    role="Content Writer",
    goal="Transform research findings into high-quality blog posts.",
    backstory="Experienced writer in medical topics.",
    tools=[search_tool],
    llm=llm
)
```

### 6. Define Research and Writing Tasks

#### **Research Task**
The Research Analyst gathers data, fact-checks, and organizes findings.

```python
research_tasks = Task(
    description="Conduct research on recent medical AI advancements, analyze trends, and compile a structured report.",
    expected_output="A structured research document with citations and key findings.",
    agent=senior_research_analyst
)
```

#### **Writing Task**
The Content Writer transforms research into a structured blog post.

```python
writing_tasks = Task(
    description="Write an engaging blog post based on the research brief.",
    expected_output="A well-structured blog post in markdown format with citations.",
    agent=content_writer
)
```

### 7. Create and Run the Crew

```python
crew = Crew(
    agents=[senior_research_analyst, content_writer],
    tasks=[research_tasks, writing_tasks],
    verbose=True
)
```

### 8. Call Groq API for AI Responses
A function is created to query Groq AI for additional insights.

```python
def call_groq():
    return litellm.completion(
        model="llama3-70b-8192",
        api_key=GROQ_API_KEY,
        messages=[
            {"role": "system", "content": "You are a helpful AI assistant."},
            {"role": "user", "content": "Tell me about Medical using Gen AI."}
        ]
    )
```

### 9. Implement Retry Mechanism
To prevent failures from API request errors, a retry logic is added.

```python
def retry_request(func, retries=3, delay=5):
    for attempt in range(retries):
        try:
            return func()
        except litellm.APIError as e:
            print(f"⚠️ Attempt {attempt + 1} failed: {e}")
            time.sleep(delay)
    raise Exception("❌ All retry attempts failed.")
```

### 10. Execute the Crew
Finally, the system is triggered to start the research and writing process.

```python
result = retry_request(call_groq)
print(result)
```

---

## How to Run the Project

1. **Clone the Repository**
   ```sh
   git clone https://github.com/your-repo-name.git
   cd your-repo-name
   ```

2. **Install Dependencies**
   ```sh
   pip install -r requirements.txt
   ```

3. **Run the Script**
   ```sh
   python main.py
   ```

---

## Expected Output
- A structured **research report** with well-categorized findings.
- A **high-quality blog post** in markdown format with citations.

---

## Future Improvements
- Enhance the AI agents with more domain-specific knowledge.
- Add UI for managing research topics dynamically.
- Expand support for more LLM models.

---

## Contributors
- **Your Name** - [GitHub Profile](https://github.com/your-username)


