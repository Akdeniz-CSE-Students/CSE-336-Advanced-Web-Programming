Building a Career Assistant AI Agent


Instructions
In this assignment, you will design and implement a Career Assistant AI Agent that communicates with potential employers on your behalf.



You will implement an AI agent system consisting of:

Primary Agent (Career Agent)

Response Evaluator Agent (Critic/Judge)

Email Notification Tool

Unknown Question Alert Tool

Career Response Agent
Your agent should:

Receive a message from a potential employer

Generate a professional response on your behalf

Maintain tone: professional, concise, polite

Use your CV/profile information as context (static or RAG-based)

Minimum capabilities:

Answer interview invitations

Respond to technical questions

Politely decline offers

Ask clarifying questions when needed

Response Evaluator (Self-Critic Agent)
Before the message is sent:

The evaluator must assess the generated response.

It should check:

Professional tone

Clarity

Completeness

Safety (no hallucinations, no false claims)

Relevance to employer’s message

If the evaluator score is below a threshold:

The agent must revise the response automatically.

Log the evaluation score and feedback.

Implementation options:

LLM-as-a-Judge prompt

Rule-based scoring system

Hybrid approach

Mobile Notification Tool 
Implement a tool that:

Sends a notification to your mobile device when:

A new employer message arrives

A final response is approved and sent

Possible implementations:

Firebase Cloud Messaging (FCM)

Telegram Bot API

WhatsApp Business API

Email-to-push service

Pushover API

You must:

Document the architecture

Demonstrate a working notification

Unknown Question Detection Tool 
Your agent must detect when:

It does not have sufficient knowledge

The question is outside your expertise

The confidence score is low

In such cases:

Trigger a notification to you

Ask for human intervention

Log the event

Example triggers:

Salary negotiation beyond a threshold

Legal questions

Deep technical question outside your domain

Ambiguous job offer

Architecture Requirements
You must include:

Agent loop design

Tool invocation mechanism

Prompt design documentation

Flow diagram of the system

Suggested stack:

Python + OpenAI API (or similar LLM)

LangChain / LlamaIndex (optional)

FastAPI backend

Any notification service

Simple frontend (optional)

Deliverables
✅ Working demo (live demo)

✅ Source code (GitHub)

✅ Architecture diagram

✅ 3 test cases:

Standard interview invitation

Technical question

Unknown/unsafe question

✅ Short report (3–5 pages) including:

Design decisions

Evaluation strategy

Failure cases

Reflection

🌟 Bonus (Optional)
Add memory (conversation history tracking)

Add confidence scoring visualization

Deploy to cloud



By completing this assignment, students will:

Understand agent-tool interaction

Implement self-evaluating AI systems

Design human-in-the-loop pipelines

Handle uncertainty and confidence estimation

Integrate external APIs with LLM agents