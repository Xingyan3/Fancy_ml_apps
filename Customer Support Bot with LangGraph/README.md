# Customer Support Bot with LangGraph — README

## Overview

This project implements an intelligent **customer support assistant** for Swiss Airlines using **LangChain**, **LangGraph**, and several custom tools backed by a SQLite database. The assistant helps users manage travel-related tasks such as searching for flights, reviewing reservations, booking or modifying excursions, and updating user information.

The system is designed to be *safe*, *extensible*, and *interactive*, using **interrupts** to confirm sensitive operations before execution.

## Features

### 1. Conversational Customer Support Assistant
- Answers customer queries.
- Uses LLM reasoning powered by Anthropic Claude or OpenAI models.
- Responds in context and keeps track of conversation state.

### 2. Tool-Enabled Actions
Tools allow the bot to perform real actions on behalf of users, including:
- `fetch_user_flight_information` – View current flight and ticket details.
- `search_flights` – Query flights by airports and time ranges.
- `book_excursion`, `update_excursion`, `cancel_excursion` – Manage trip recommendations.
- Tools use SQLite queries to ensure fast local execution.

### 3. Interrupt-Before Execution
Sensitive actions (e.g., booking, updating, cancelling) require explicit user confirmation.  
The system uses **interrupt_before** to pause the graph prior to tool execution.

### 4. Conditional Interrupts
Tools are categorized into:
- **Safe tools** (read-only) – Execute immediately.
- **Sensitive tools** (modify data) – Require user approval.

This preserves both safety and conversational fluidity.

### 5. Modular LangGraph Architecture
The system uses:
- A reusable `State` object storing messages and user info.
- A custom Assistant wrapper around the LLM.
- Tool nodes with structured error handling.
- Secure `RunnableConfig` injection for user-specific context.

## Project Structure

```
project/
│
├── tools/
│   ├── flights.py
│   ├── excursions.py
│   └── utilities.py
│
├── state/
│   └── state.py
│
├── graph/
│   ├── part1_basic.py
│   ├── part2_add_confirmation.py
│   └── part3_conditional.py
│
├── db/
│   └── flights.db
│
└── README.md
```

## How It Works

### State Management
A TypedDict-based `State` holds:
- The running chat history.
- User profile information.

### Assistant Loop
Ensures the LLM does not return empty or malformed messages, retrying until a valid response or tool call is produced.

### Tools Layer
Each tool:
- Connects securely to the database.
- Executes validated SQL queries.
- Returns structured data.

### Confirmation Layer
Sensitive actions prompt:
1. An interrupt return to the user.
2. A confirmation request.
3. Continued execution only on approval.

### Flight Information Security
User context such as `passenger_id` is injected via `RunnableConfig`, ensuring privacy and preventing data access across users.

## Installation

### Requirements
- Python 3.10+
- `langchain-core >= 0.2.16`
- SQLite3

### Install Dependencies
```bash
pip install -r requirements.txt
```

### Environment Variables
Create a `.env` file:

```
ANTHROPIC_API_KEY=your_key_here
OPENAI_API_KEY=your_key_here
```

## Running the Project

```bash
python main.py
```

### Optional: Enable LangSmith Tracing
```
LANGCHAIN_TRACING_V2=true
LANGCHAIN_API_KEY=your_key_here
```

## Example Use Cases

- “What flights do I have booked?”  
- “Change my flight to next week.”  
- “Book me an excursion to Basel.”


## License

MIT License

## Reference

https://langchain-ai.github.io/langgraph/tutorials/customer-support/customer-support/