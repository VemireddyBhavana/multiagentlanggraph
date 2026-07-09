# Helpora 2.0 🤖🤖🤖 — Multi-Agent LangGraph Support System

A multi-agent student support system built as part of the **NIAT Agentic AI Workshop**, using **LangGraph**, **LangChain**, and **Groq**.

## Overview
This project is an extended version of **Helpora**, a student support AI assistant.

In the first version, Helpora handled only **billing / refund-related tickets**.  
In this version, Helpora is upgraded into a **multi-agent support system** that can handle **three different types of student tickets**:

- **Billing issues** → duplicate charges, refunds, payment problems
- **Tech issues** → LMS crashes, upload failures, app bugs
- **Content issues** → locked topics, prerequisites, topic unlocking

Instead of using one single agent for every problem, this project splits the work into **specialist agents** and uses a **router agent** to send each ticket to the correct specialist.

---

## Use Case
The notebook demonstrates Helpora handling three student support tickets:

### 1. Billing Ticket — Aditya
> “I was charged twice for my course fee.”  
Student ID: **S-7-042**

### 2. Tech Ticket — Priya
> “The LMS keeps crashing when I upload assignments.”  
Student ID: **S-7-158**

### 3. Content Ticket — Rohan
> “I completed the loops topic but functions is still locked.”  
Student ID: **S-7-091**

---

## Goal
The goal of this project is to build a **multi-agent AI support system** where:

- a **router** reads a ticket and decides what kind of issue it is
- the ticket is passed to the **correct specialist agent**
- the specialist uses its own tools and data to solve the issue
- the final reply is returned to the student

This demonstrates how **LangGraph** can be used to coordinate multiple AI agents using **shared state + routing logic**.

---

# System Architecture

## Agents in the system

### 1. Router Agent
The router is responsible for classifying each ticket into one of these categories:

- `BILLING`
- `TECH`
- `CONTENT`

It does not solve the issue itself.  
Its job is only to **route the ticket to the correct specialist**.

---

### 2. Billing Specialist — Helpora Billing
Handles:
- duplicate charges
- refunds
- payment-related problems
- fee-related support tickets

Uses tools:
- `lookup_payments(student_id)`
- `create_task(summary)`

---

### 3. Tech Specialist — Helpora Tech
Handles:
- LMS crashes
- assignment upload failures
- app / platform issues
- known technical problems

Uses tools:
- `search_tech_issues(keyword)`
- `create_task(summary)`

---

### 4. Content Specialist — Helpora Content
Handles:
- locked topics
- missing topic access
- prerequisite-based unlocking
- content access support

Uses tools:
- `check_prerequisites(student_id, topic)`
- `unlock_topic(student_id, topic)`

---

# Tech Stack
- **Python**
- **LangChain**
- **LangGraph**
- **Groq API**
- **SQLite**
- **Google Colab**

---

# Project Workflow

## Step 1 — Setup
- load API key
- install required libraries
- connect the LLM using **ChatGroq**

## Step 2 — Prepare the data
The project uses three kinds of support data:

### Billing data
A SQLite **payments database** with student payment records

### Tech data
A list of **known LMS / app issues** and workarounds

### Content data
A student progress dataset with:
- completed topics
- topic prerequisites

---

## Step 3 — Create tools
Each specialist gets only the tools relevant to its job.

### Billing tools
- `lookup_payments(student_id)`
- `create_task(summary)`

### Tech tools
- `search_tech_issues(keyword)`

### Content tools
- `check_prerequisites(student_id, topic)`
- `unlock_topic(student_id, topic)`

---

## Step 4 — Build specialist agents
Three specialist agents are created using **LangChain `create_agent()`**:

- **Billing Agent**
- **Tech Agent**
- **Content Agent**

Each agent has:
- its own prompt
- its own tools
- a narrow task scope

---

## Step 5 — Build the router
A router function reads a support ticket and classifies it into:

- `BILLING`
- `TECH`
- `CONTENT`

---

## Step 6 — Build the LangGraph workflow
The graph is built using:

- a **shared state**
- a **router node**
- three **specialist nodes**
- **conditional edges** that decide which specialist should run next

### Graph flow
```text
START
  ↓
ROUTER
  ├── BILLING → Billing Agent
  ├── TECH    → Tech Agent
  └── CONTENT → Content Agent
  ↓
 END
