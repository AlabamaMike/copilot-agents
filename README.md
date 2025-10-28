# copilot-agents
Custom agents built for GitHub Copilot to automate agentic coding workflows.

## Available Agents

### 🔍 Codebase Analysis Agent

**Agent Name:** `codebase-analysis`

**Purpose:** A specialist at understanding HOW code works - analyzes implementation details, traces data flow, and explains technical workings with precise file:line references.

**Use Cases:**
- Understanding how a specific function or module works
- Tracing data flow through the application
- Analyzing implementation patterns and architecture
- Getting detailed explanations of complex code with exact references
- Understanding control flow and execution paths
- Mapping dependencies between components

**How to Use:**
Invoke the agent in GitHub Copilot by mentioning it in your chat:
```
@codebase-analysis How does the authentication flow work in this codebase?
@codebase-analysis Trace the data flow from the API endpoint to the database
@codebase-analysis Explain how the payment processing is implemented
```

**What You'll Get:**
- Precise file:line references for every code element
- Step-by-step data flow traces
- Detailed implementation explanations
- Control flow analysis
- Technical insights with proper context

**Key Features:**
- ✅ Provides exact file:line references (e.g., `src/auth/login.js:45-67`)
- ✅ Traces data transformations step-by-step
- ✅ Explains implementation patterns and design choices
- ✅ Documents dependencies and component interactions
- ✅ Includes relevant code snippets with line numbers
- ✅ Analyzes error handling and edge cases

**Configuration:** `.github/agents/codebase-analysis.yml`

---

## Setup

These custom agents are automatically available when this repository is configured with GitHub Copilot. The agent configurations are stored in the `.github/agents/` directory.

## Contributing

To add new agents:
1. Create a new YAML file in `.github/agents/`
2. Define the agent's name, description, and instructions
3. Update this README with documentation
4. Test the agent in GitHub Copilot

## Agent Structure

Each agent is defined with:
- **name**: Unique identifier for the agent
- **description**: Brief summary of the agent's purpose
- **instructions**: Detailed prompt that guides the agent's behavior

## License

See repository license for details.
