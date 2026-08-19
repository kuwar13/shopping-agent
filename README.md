# 🛒 AI Shopping Agent

An **agentic AI shopping assistant** built with **LangChain, Groq, Streamlit, and SQLite**.

The application understands natural-language shopping requests, searches a product catalog, checks product ratings, analyzes uploaded product images, recommends suitable products, and places orders only after explicit user confirmation.

---

## ✨ Features

-  Natural-language shopping assistant
-  Product search by name, description, and category
-  Filter products by maximum price
-  Filter organic/non-organic products
-  Retrieve product ratings and review counts
-  Search products using uploaded images
-  LangChain agent with tool calling
-  Checkout only after user confirmation
-  SQLite database for products, reviews, and orders
-  Interactive Streamlit chat interface

---

##  How It Works

The LLM acts as an **agent** that decides which tool to use based on the user's request.

```text
User
  │
  ▼
Streamlit UI
  │
  ▼
LangChain Agent
  │
  ├──► search_products()
  │        │
  │        ▼
  │      SQLite
  │
  ├──► get_rating()
  │        │
  │        ▼
  │      SQLite
  │
  ├──► describe_product_image()
  │        │
  │        ▼
  │     Groq Vision Model
  │
  └──► checkout()
           │
           ▼
         SQLite
```

The LLM does **not access the database directly**. It calls Python tools, and those tools interact with SQLite.

---

##  Tech Stack

| Technology | Purpose |
|---|---|
| Python | Backend logic |
| LangChain | Agent and tool orchestration |
| Groq | LLM inference |
| GPT-OSS | Agent reasoning and tool calling |
| Qwen | Image understanding |
| Streamlit | Chat-based frontend |
| SQLite | Product, review, and order storage |
| python-dotenv | Environment variable management |

---

## 🤖 Agent Tools

### `search_products`

Searches the product database using:

- Keyword
- Maximum price
- Organic preference

Example:

```python
search_products(
    query="honey",
    max_price=20,
    is_organic=True
)
```

---

### `get_rating`

Retrieves the average customer rating and number of reviews for a product.

Example:

```python
get_rating(product_id=1)
```

---

### `describe_product_image`

Uses a multimodal LLM to analyze an uploaded product image and extract useful search information.

For example, an uploaded image could be interpreted as:

```text
Product: Honey
Organic: Yes
Search keyword: honey
```

The agent can then search the product database using those attributes.

---

### `checkout`

Creates an order in the SQLite database.

The agent is instructed to call this tool **only after the user explicitly confirms the purchase**.

Example conversation:

```text
User:
I want organic honey under $20 with at least a 4.5 rating.

Assistant:
1. Organic Raw Honey — $14.99 ★4.62
2. Organic Buckwheat Honey — $18.99 ★4.62
3. Organic Acacia Honey — $17.99 ★4.75

Would you like to order one?

User:
Order number 3.
```

The agent then calls the checkout tool.

---

## 💾 Database

The project uses **SQLite**, so no separate database server is required.

The database contains three main tables:

### Products

Stores the product catalog.

```text
products
├── id
├── name
├── category
├── price
├── description
└── is_organic
```

### Reviews

Stores ratings associated with products.

```text
reviews
├── id
├── product_id
└── rating
```

### Orders

Stores successfully placed orders.

```text
orders
├── id
├── product_id
├── product_name
├── price
└── ordered_at
```

---

## 📁 Project Structure

```text
shopping_agent/
│
├── app.py
├── shopping_agent.py
├── reviews_api.py
├── setup_db.py
├── requirements.txt
├── .env.example
├── .gitignore
├── README.md
│
└── resources/
```

### Main Files

**`app.py`**

Contains the Streamlit user interface and manages the chat experience.

**`shopping_agent.py`**

Contains the LangChain agent, Groq models, agent instructions, and tools.

**`reviews_api.py`**

Contains logic for retrieving ratings from SQLite.

**`setup_db.py`**

Creates and populates the local SQLite database.

---

##  Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/YOUR_USERNAME/shopping-agent.git
```

Move into the project:

```bash
cd shopping-agent
```

---

### 2. Create a virtual environment

Python 3.11 is recommended.

```bash
python3.11 -m venv .venv
```

Activate it:

#### macOS / Linux

```bash
source .venv/bin/activate
```

#### Windows

```bash
.venv\Scripts\activate
```

---

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

---

### 4. Configure the Groq API key

Create a `.env` file:

```bash
touch .env
```

Add:

```env
GROQ_API_KEY=your_groq_api_key_here
```

Do **not** commit `.env` to GitHub.

---

### 5. Initialize the database

```bash
python setup_db.py
```

This creates the local SQLite database and sample product data.

---

##  Run the Agent in Terminal

```bash
python shopping_agent.py
```

Example query:

```text
I want organic honey with a rating above 4.5 and a price under $20.
```

Example output:

```text
#1 Organic Raw Honey — $14.99 ★4.62

#2 Organic Buckwheat Honey — $18.99 ★4.62

#3 Organic Acacia Honey — $17.99 ★4.75

Would you like to order one?
```

---

##  Run the Streamlit App

```bash
streamlit run app.py
```

Then open:

```text
http://localhost:8501
```

---

##  Example Agent Flow

Consider the request:

```text
Find me organic honey under $20 with at least a 4.5 rating.
```

The agent can perform the following workflow:

```text
User Request
     │
     ▼
LLM understands requirements
     │
     ▼
search_products()
     │
     ▼
SQLite Products
     │
     ▼
get_rating()
     │
     ▼
Compare Ratings
     │
     ▼
Recommend Products
     │
     ▼
Wait for User Confirmation
     │
     ▼
checkout()
     │
     ▼
Save Order to SQLite
```

This demonstrates an **agentic workflow**, where the model decides which actions to perform instead of relying on a single prompt-response call.

---

##  Safety

The application separates **recommendation** from **action**.

The agent can:

```text
Search → Compare → Recommend
```

without user confirmation.

However:

```text
Checkout
```

requires explicit confirmation from the user.

This pattern is useful when building production agents that perform actions with real-world side effects.

---

## What This Project Demonstrates

This project demonstrates practical experience with:

- Agentic AI architecture
- LLM tool calling
- LangChain agents
- Structured tool parameters
- Multi-step reasoning workflows
- External data retrieval
- SQLite integration
- Multimodal AI
- Human-in-the-loop workflows
- Streamlit application development

---

##  Future Improvements

Possible improvements include:

- Semantic/vector-based product search
- Product embeddings
- Persistent conversation memory
- User accounts
- Shopping cart functionality
- Product comparison tool
- Real e-commerce APIs
- Real payment integration
- LangGraph workflow orchestration
- Agent observability with LangSmith
- Automated tool and agent testing
- Recommendation personalization

---

##  Why LangChain?

LangChain works well for the current implementation because the shopping workflow primarily involves:

```text
User → Agent → Tool → Result 
```

For more complex workflows involving repeated searches, retries, conditional branches, approval states, or loops, the application could be extended using **LangGraph**.

---
