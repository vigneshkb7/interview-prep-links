# AI AGENT



Traditional GenAI (Prompt-and-Response): You ask a chatbot to write an email to a client about a meeting. It writes the email. You copy it, open your email app, check your calendar for open slots, paste the text, and hit send. You are the orchestrator.

An AI Agent is a software program driven by an AI model that can break down a goal, make its own decisions, and use external tools to get a job done from start to finish.



# AGENTIC AI

Agentic AI (Goal-Driven): You tell the AI, "Schedule a sync with Sarah next week to review the project budget." The AI agent checks your calendar, emails Sarah with three open options, waits for her reply, picks the slot she chose, sends the calendar invite, and notifies you when it's done. The AI is the orchestrator.

Agentic AI is the term for this whole style of computing—shifting from AI that just talks to you, to AI that can act autonomously on your behalf.

# Langraph is a framework for building agentic AI. It provides a set of tools and abstractions that make it easier to create AI agents that can understand goals, plan actions, and interact with external systems.

Langgraph is build on top of the langchain and is fully interoperable with it. It provides a higher-level abstraction for building agentic AI, making it easier to create complex agents that can perform a wide range of tasks.

it is like aading more features on top of langchain to make it more powerful and easier to use for building agentic AI systems.

The 2 main components of Langraph are:

1. **Graph**: A directed graph that represents the flow of information and actions in an agentic AI system. Each node in the graph represents a specific action or decision point, and the edges represent the flow of information between nodes.

Node & Edges

Stateful orchestration: The graph structure allows for stateful orchestration of actions, enabling agents to maintain context and make informed decisions based on previous interactions.

2. **Agent**: An agent is an entity that can perceive its environment, make decisions, and take actions to achieve its goals. In Langraph, agents are represented as nodes in the graph, and they can interact with other agents and external systems to accomplish their objectives.


# ReAct Agents

Reasoning. + Acting. = ReAct

ReAct agents are a type of agentic AI that combines reasoning and acting capabilities. They can reason about their environment, make decisions based on that reasoning, and take actions to achieve their goals.

Human in the loop: ReAct agents can also incorporate human feedback into their decision-making process, allowing them to learn from human input and improve their performance over time.


# Messages

AI Message -> message from the AI agent to the user or another agent, containing information about the agent's state, actions, or decisions.

Human Message -> message from a human user to the AI agent, containing instructions, feedback, or other information relevant to the agent's tasks.

System Message -> message from the system to the AI agent, providing context or instructions for the agent's operation.

Tool Message -> message from an external tool or system to the AI agent, providing information or data that the agent can use to make decisions or take actions.

{"messages":[{"type":"system","content":"You are a helpful assistant."},{"type":"human","content":"Write a python function to add two numbers."},{"type":"ai","content":"Here is a simple Python function that adds two numbers:\n\n```python\ndef add_numbers(a, b):\n    return a + b\n\n# Example usage:\nresult = add_numbers(3, 5)\nprint(result)  # Output: 8\n```\n\nYou can call the `add_numbers` function with any two numbers to get their sum."}]}

This message structure allows for clear communication between the AI agent, human users, and external systems, enabling effective collaboration and decision-making in agentic AI systems.

# Prompts

we should always use prompts to guide the AI agent's behavior and decision-making process. Prompts can be used to provide context, instructions, or constraints for the agent's actions.

# Tools

Tools are external resources that AI agents can use to perform specific tasks. They can be APIs, databases, or other systems that provide functionality that the agent needs to achieve its goals.

# Workflow

Workflows are sequences of steps that define how an AI agent should operate to achieve a specific goal. They can include decision points, loops, and other control structures that allow the agent to adapt its behavior based on the situation.

