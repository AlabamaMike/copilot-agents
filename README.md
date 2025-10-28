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

### 📁 Code Finder Agent

**Agent Name:** `code-finder`

**Purpose:** A specialist at finding WHERE code lives in a codebase - locates relevant files and organizes them by purpose, without analyzing their contents.

**Use Cases:**
- Quickly locating files related to a specific feature or topic
- Understanding project file organization and structure
- Finding all files of a specific type (tests, configs, docs, types)
- Discovering where to add new code for a feature
- Mapping out file clusters and related code locations
- Getting an inventory of files by category

**How to Use:**
Invoke the agent in GitHub Copilot by mentioning it in your chat:
```
@code-finder Find all files related to authentication
@code-finder Locate payment processing code
@code-finder Show me all test files for the API layer
@code-finder Where should I add new shopping cart code?
```

**What You'll Get:**
- Organized file lists by category (implementation, tests, config, docs, types, examples)
- Full paths from repository root
- Directory clusters showing related files
- File organization patterns
- Recommendations for where to add new files

**Key Features:**
- ✅ Fast file location using grep, find, and glob patterns
- ✅ Categorizes files by purpose (implementation, tests, config, docs, types, examples)
- ✅ Shows directory organization and file clusters
- ✅ Provides full paths and file counts
- ✅ Identifies naming patterns and conventions
- ✅ Notes common locations for different file types

**Configuration:** `.github/agents/code-finder.yml`

---

### 🔎 Pattern Finder Agent

**Agent Name:** `pattern-finder`

**Purpose:** A specialist at finding code patterns and examples in the codebase - locates similar implementations that can serve as templates or inspiration for new work.

**Use Cases:**
- Finding similar implementations to use as templates
- Discovering how specific patterns are implemented
- Locating usage examples for a particular approach
- Understanding coding conventions through examples
- Finding test patterns for similar features
- Exploring multiple variations of the same pattern

**How to Use:**
Invoke the agent in GitHub Copilot by mentioning it in your chat:
```
@pattern-finder Show me pagination patterns in the codebase
@pattern-finder Find error handling patterns in API routes
@pattern-finder How is authentication implemented? Show me examples
@pattern-finder Find state management patterns in React components
```

**What You'll Get:**
- Multiple pattern examples with code snippets
- File:line references for each pattern
- Key characteristics of each pattern
- Testing patterns for the implementation
- Information about where patterns are used
- Related utilities and helper files

**Key Features:**
- ✅ Shows existing patterns without judgment or critique
- ✅ Provides multiple variations of the same pattern
- ✅ Includes complete code examples with context
- ✅ Documents test patterns alongside implementation patterns
- ✅ Notes where each pattern is used in the codebase
- ✅ Focuses on "what exists" rather than "what should be"
- ✅ Catalogs patterns like a reference library

**Configuration:** `.github/agents/pattern-finder.yml`

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
