
# AI Sales Agent Using n8n

An AI-powered Sales and Presales Assistant developed using n8n, Google Gemini, Openai, JavaScript, GitHub, and Gmail.

The project automates customer interactions, training course inquiries, quotation calculations, lead qualification, email communication, and error handling through interconnected n8n workflows.

The AI Sales Agent is named **Merna**.

**Developed by:** Merna Mansour

---

## 1. Project Overview

The AI Sales Agent is an intelligent chatbot designed to assist customers interested in professional and technical training programs.

The agent, Merna, communicates with customers in Arabic and English, retrieves information from a GitHub-hosted knowledge base, collects customer requirements, calculates training quotations, and supports automated email communication.

The project consists of three n8n workflows:

1. Sales AI Agent
2. Knowledge Base
3. Error Handling

These workflows demonstrate AI agent orchestration, tool calling, document retrieval, JavaScript-based calculations, conversational memory, and error management.

---

## 2. Features

- AI-powered customer support and sales assistance
- Arabic and English language support
- Customer information collection
- Training program inquiries
- GitHub knowledge-base integration
- Automated quotation calculation
- Lead qualification and routing rules
- Gmail integration
- Conversation memory
- AI guardrails
- Backup AI Agent
- Error logging using n8n Data Tables
- Public chatbot interface

---

## 3. Technologies Used

| Technology | Purpose |
|------------|---------|
| n8n | Workflow automation and orchestration |
| Google Gemini | Primary AI language model |
| OpenAI | Guardrails and backup AI Agent |
| JavaScript | Training quotation calculator |
| GitHub | Knowledge-base hosting and project version control |
| Gmail | Email communication |
| Simple Memory | Conversation context management |
| n8n Data Tables | Error logging |
| Markdown | Knowledge-base documentation |

---

## 4. Project Architecture

The project consists of three workflows.

### Workflow 1: Sales AI Agent

The main workflow handles customer conversations and sales operations.

```text
                    Chat Trigger
                         |
                         v
                      Guardrails
                         |
                         v
                      AI Agent
                         |
          +--------------+---------------+
          |              |               |
          v              v               v
     Gemini Model    Simple Memory    AI Tools
                                         |
                          +--------------+--------------+
                          |              |              |
                          v              v              v
                    Knowledge Base   Calculator      Gmail
                          |
                          v
                    GitHub Content
```

The main AI Agent also includes an error-output path connected to a backup AI Agent.

### Workflow 2: Knowledge Base

The knowledge-base workflow retrieves training information from GitHub.

```text
When Executed by Another Workflow
                 |
                 v
           GitHub - Get a File
                 |
                 v
           Extract from File
                 |
                 v
          Extracted Text
```

The exported workflow also contains a Google Gemini Message a Model node and an Edit Fields node for producing an `answer` field.

In the current export, the extraction node is not connected to the Gemini node. This connection must be completed for the full question-answering sequence to execute.

### Workflow 3: Error Handling

The error-handling workflow records execution errors.

```text
            Error Trigger
                  |
                  v
          Data Table - Insert Row
                  |
                  v
           Error Record
```

The stored information includes:

- Workflow name
- Error message

---

## 5. Main AI Sales Agent

The main workflow uses an n8n AI Agent named Merna.

### Agent Responsibilities

The AI Agent is instructed to:

1. Greet customers professionally.
2. Detect the conversation language.
3. Understand customer requirements.
4. Retrieve training information from the knowledge base.
5. Collect required customer details.
6. Calculate quotations using the calculator tool.
7. Present calculated prices to customers.
8. Apply internal lead-routing rules.
9. Use Gmail for authorized email communication.
10. Confirm completed actions.

### Language Support

The system prompt defines two supported languages:

- Arabic
- English

The agent is instructed to maintain the customer's selected language throughout the conversation unless a language change is explicitly requested.

### Required Customer Information

For quotation requests, the agent collects:

- Customer name
- Company or organization name
- Training topic
- Number of participants
- Contact email address

The calculator additionally requires:

- Training duration in days
- Delivery format

The agent must collect these calculator inputs before requesting a quotation.

---

## 6. Knowledge Base Integration

The knowledge base is stored in a Markdown file hosted on GitHub.

### Knowledge Base File

`Merna_Training_Knowledge_Base.md`

### Knowledge Retrieval Process

1. The main AI Agent calls the knowledge-base workflow.
2. The workflow receives a customer question.
3. The GitHub node retrieves the Markdown file.
4. The Extract from File node converts the file into text.
5. The extracted content can be used to answer customer questions.

### Knowledge Base Content

The demonstration knowledge base covers:

- Robotic Process Automation
- Data Analytics
- Cloud Computing
- Cybersecurity
- Artificial Intelligence and Machine Learning
- Software Development

Additional information includes training durations, target audiences, delivery methods, certification information, and frequently asked questions.

Training information is demonstration data and should be replaced with verified business information before production use.

---

## 7. Training Quotation Calculator

The pricing calculator is implemented using JavaScript in an n8n Code Tool.

It calculates quotation values based on:

- Number of participants
- Training duration
- Delivery format

### Pricing Tiers

| Number of Participants | Price per Person per Day |
|------------------------|--------------------------|
| 1-10 | 450 |
| 11-20 | 400 |
| 21-30 | 350 |
| 31-50 | 320 |
| 51-999 | 280 |

### Delivery Format Multipliers

| Delivery Format | Multiplier |
|-----------------|------------|
| Virtual | 1.0 |
| Onsite | 1.2 |
| Hybrid | 1.1 |

### Calculation Formulas

```text
Base Price = Participants × Price per Person per Day × Days

Total Price = Base Price × Delivery Multiplier

Deposit = Total Price × 0.50

Remaining Balance = Total Price - Deposit
```

### Example Input

```json
{
  "participants": 20,
  "days": 4,
  "delivery_format": "onsite"
}
```

### Example Output

```json
{
  "participants": 20,
  "days": 4,
  "delivery_format": "onsite",
  "tier": "medium",
  "price_per_person_per_day": 400,
  "base_price": 32000,
  "delivery_multiplier": 1.2,
  "total_price": 38400,
  "deposit_50_percent": 19200,
  "remaining_balance": 19200,
  "currency": "EGP"
}
```

The actual calculator also generates a quotation ID.

### Calculator Configuration Note

In the exported workflow, the input schema uses `delivery fomat`, while the JavaScript expects `delivery_format`.

The schema must be corrected to use:

```json
{
  "participants": 12,
  "days": 4,
  "delivery_format": "virtual"
}
```

All prices are demonstration values.

---

## 8. Lead Qualification and Routing

The AI Agent uses predefined internal routing rules.

### Organization Categories

**BIG_NAME**

- Government entities
- Universities
- Banks
- Telecommunications companies
- Major enterprises

**SMALL_NAME**

- Other organizations

### Audience Categories

**BIG_AUDIENCE**

30 or more participants.

**SMALL_AUDIENCE**

Fewer than 30 participants.

### Routing Rules

| Organization Type | Audience Size | Destination |
|-------------------|---------------|-------------|
| SMALL_NAME | BIG_AUDIENCE | Prospect |
| BIG_NAME | BIG_AUDIENCE | Sales |
| BIG_NAME | SMALL_AUDIENCE | Sales |
| SMALL_NAME | SMALL_AUDIENCE | Prospect |

If the organization cannot be classified confidently, the system prompt directs the agent toward sales review.

These rules are defined in the system prompt. A separate deterministic routing node has not been implemented in the exported workflow.

---

## 9. Gmail Integration

The main workflow includes a Gmail Send Message tool.

### Email Functions

The agent is designed to support:

- Sending training quotations
- Preparing customer follow-up messages
- Forwarding sales opportunities
- Sharing quotation details with the appropriate recipient

### Current Configuration

The exported Gmail node has:

- A fixed testing recipient
- AI-generated email subject
- AI-generated email body
- Plain-text email format

Dynamic customer and sales-team recipients require additional configuration.

Email operations should be tested and validated before being enabled for real customers.

---

## 10. AI Guardrails

The main workflow contains an AI Guardrails node.

The guardrails configuration includes:

- Keyword-based checks
- Jailbreak detection
- An OpenAI language model
- An error-handling branch

The configured keyword examples include:

- Password
- Sourcecode

### Guardrails Flow

```text
Customer Message
       |
       v
   Guardrails
       |
       +---- Accepted Input ----> AI Agent
       |
       +---- Rejected Input ----> Data Table
                                      |
                                      v
                                Stop and Error
```

The guardrails are intended to reduce the risk of unsafe or inappropriate inputs reaching the sales agent.

---

## 11. Backup AI Agent

The main workflow contains a second AI Agent used as a backup path.

### Backup Configuration

- OpenAI language model
- Simple Memory
- Sales assistant system prompt
- Error logging for backup-agent failures

When the primary AI Agent follows its configured error output, processing is directed to the backup AI Agent.

The backup agent currently does not have the same knowledge-base, calculator, or Gmail tools connected to it.

These integrations can be added in a future version.

---

## 12. Error Handling

A separate error workflow is used to capture failures.

### Error Workflow Components

1. Error Trigger
2. Data Table - Insert Row

### Stored Error Information

| Field | Description |
|-------|-------------|
| workfloww_name | Name of the workflow |
| Message | Execution error message |

The main Sales AI Agent workflow references this error workflow in its configuration.

The main workflow also contains Data Table nodes for recording guardrails-related and backup-agent errors.

---

## 13. Repository Structure

```text
AI-sales-Agent/
│
├── README.md
│
├── Sales AI Agent.json
│
├── knowledge based.json
│
├── error.json
│
├── Merna_Training_Knowledge_Base.md
│
└── screenshots/
    ├── sales-agent.png
    ├── knowledge-base.png
    └── error-workflow.png
```

The screenshots directory is optional.

---

## 14. Installation and Setup

### Prerequisites

- n8n Cloud account or self-hosted n8n
- Google Gemini API access
- OpenAI API access or a supported managed AI connection
- GitHub account
- Gmail account
- Access to n8n Data Tables

### Step 1: Import Workflows

1. Open n8n.
2. Create a new workflow.
3. Import `Sales AI Agent.json`.
4. Import `knowledge based.json`.
5. Import `error.json`.

### Step 2: Configure Credentials

Connect the necessary accounts:

- Google Gemini
- OpenAI
- GitHub
- Gmail

Credentials must be configured in the imported n8n instance.

### Step 3: Configure Knowledge Base

1. Upload the Markdown knowledge-base file to GitHub.
2. Open the Knowledge Base workflow.
3. Configure the GitHub repository and file path.
4. Verify that Extract from File returns text.
5. Connect the extracted-text output to the Gemini node if using its question-answering functionality.
6. Test the workflow using a sample question.

### Step 4: Configure Calculator

1. Open the Sales AI Agent workflow.
2. Locate the Calculation Tool.
3. Verify the JavaScript code.
4. Correct the input schema field names.
5. Test using sample participant counts and training durations.

### Step 5: Configure Gmail

1. Open the Gmail tool.
2. Connect Gmail credentials.
3. Verify the Send Message operation.
4. Use a controlled testing recipient.
5. Test email generation and delivery.
6. Add validated dynamic recipients when ready.

### Step 6: Configure Error Handling

1. Open the Error workflow.
2. Configure the n8n Data Table.
3. Verify the column mappings.
4. Confirm that the main workflow references the correct error workflow.

### Step 7: Test the Agent

Open the chat interface and send a message.

Example:

```text
Customer:
What training courses are available?

Merna:
I can help you explore the available training
programs. Let me check the training information
and provide the relevant options.
```

Test the knowledge-base, calculator, guardrails, and Gmail operations separately before using the complete automation.

### Step 8: Publish

After testing:

1. Save the workflows.
2. Publish or activate the required workflows.
3. Enable the public chat interface.
4. Copy the production chat URL.
5. Test the published chatbot.

---

## 15. Current Implementation Limitations

The exported project contains several components that require additional configuration or validation:

1. The knowledge-base extraction and Gemini question-answering paths are disconnected.
2. The calculator input schema contains a field-name mismatch.
3. Gmail currently uses a fixed recipient.
4. The backup AI Agent does not have the main agent's tools.
5. Lead routing is prompt-based rather than enforced by separate workflow conditions.
6. Error handling needs to be tested under actual failure conditions.
7. The quotation ID uses a random three-digit suffix and is not guaranteed to be unique.

These limitations should be addressed before deploying the system in a production sales environment.

---

## 16. Future Improvements

Potential improvements include:

- CRM integration
- Google Sheets lead tracking
- Persistent conversation history
- Automated customer follow-up
- Improved sales lead scoring
- Human approval for email delivery
- Dynamic recipient routing
- Structured quotation storage
- Improved quotation ID generation
- Additional knowledge-base documents
- More advanced retrieval systems
- Sales analytics dashboard
- Automated reporting
- Enhanced backup-agent capabilities

---

## 17. Security and Privacy

- Do not commit API keys or access tokens.
- Review workflow exports for sensitive information.
- Keep customer records and conversation logs private.
- Restrict access to error logs containing sensitive data.
- Validate email recipients before sending.
- Use verified knowledge-base content.
- Test automated actions before production deployment.
- Avoid publishing confidential business rules or customer information.

---

## 18. Learning Reference

This project was developed as a practical learning exercise following Sara Ghanem's AI Sales Agent tutorial on YouTube.

The implementation focuses on:

- AI agents
- Prompt engineering
- n8n workflow automation
- Tool integration
- Knowledge-base retrieval
- JavaScript calculations
- Email automation
- AI guardrails
- Error handling

---

## 19. Author

**Merna Mansour**

GitHub: [MernaMansour](https://github.com/MernaMansour)

---

## 20. Disclaimer

This project is developed for educational and demonstration purposes.

The training catalog, pricing information, and business rules are demonstration content.

The workflows require appropriate configuration, testing, and verification before production deployment.
