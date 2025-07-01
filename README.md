# Email Assistant (LangGraph + LangSmith)

An automated email assistant that triages, drafts, and updates responses using LangGraph agents, with memory refinement from human feedback. Optimised for long-term adaptation and human-in-the-loop workflows.

---

## Pipeline Overview

1. **Input**: User email is parsed and processed.
2. **Triage**: An LLM router classifies the email as `respond`, `notify`, or `ignore` using stored triage preferences.
3. **Interrupts**:
   - `notify` emails trigger human review.
   - `respond` emails proceed to draft generation.
4. **Response Generation**: Drafts are created via a tool-augmented LLM with memory on response style and calendar habits.
5. **Human Feedback**: Responses and tool calls (e.g. calendar scheduling, sending emails) are reviewed, edited, or ignored via the Agent Inbox interface.
6. **Memory Update**: Preferences (triage, calendar, response style) are updated using edits and feedback via structured LLM calls.
7. **Completion**: Responses marked as `Done` are finalised and sent. Email is marked as read.
8. **Testing**: LangSmith test cases verify agent correctness across scenarios.

---

## Features

- **Memory-aware**: Preferences are stored in `BaseStore` and updated continuously.
- **Interrupt-driven**: Edits and rejections via `interrupt()` alter memory and future behaviour.
- **Tool-specific review policies**: Only selected tools (`send_email_tool`, `schedule_meeting_tool`, `Question`) trigger human validation.
- **StateGraph architecture**: Workflow is defined as an explicit state machine using LangGraph.
- **Structured prompting**: System and user prompts are modular and customised per node (`triage`, `agent`, `feedback`).
- **Testable**: Agents are fully testable using LangSmith instrumentation.

---

## Memory Namespaces

| Namespace | Purpose |
|-----------|---------|
| `("email_assistant", "triage_preferences")` | Filters noise |
| `("email_assistant", "response_preferences")` | Controls tone and structure of responses |
| `("email_assistant", "cal_preferences")` | Captures calendar and meeting behaviour |
| `("email_assistant", "background")` | Stores past interactions for long-term context |

---

## Key Components

| Component | Role |
|----------|------|
| `triage_router` | Filters input. May pass to `interrupt_handler` or agent |
| `triage_interrupt_handler` | Handles notify-type interrupts |
| `llm_call` | Generates actions using tools |
| `interrupt_handler` | Invokes human review for tool calls |
| `mark_as_read_node` | Finalises email thread |
| `update_memory` | Stores structured preference updates |

---

## Tools

Available tools include:

- `send_email_tool`
- `schedule_meeting_tool`
- `check_calendar_tool`
- `Question`
- `Done`

---

## Deployment

Requires:

- Python environment with:
  - `langgraph`
  - `langchain`
  - `langsmith`
- `.env` with:
  - OpenAI key
  - Gmail API credentials

---

## Testing

Use LangSmith to trace runs and validate agent logic. Human feedback integration and memory updates are observable across sessions.

---

## Acknowledgements

Built using concepts and code patterns from the *Building Ambient Agents with LangGraph* course by LangChain.

