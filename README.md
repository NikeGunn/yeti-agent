<div align="center">

# 🏔️ Yeti Agent

### Autonomous AI Browser Agent for Production

[![PyPI](https://img.shields.io/pypi/v/yeti-agent?color=4f46e5&label=PyPI)](https://pypi.org/project/yeti-agent/)
[![Python](https://img.shields.io/pypi/pyversions/yeti-agent?color=22c55e)](https://pypi.org/project/yeti-agent/)
[![License](https://img.shields.io/github/license/NikeGunn/yeti-agent?color=06b6d4)](https://github.com/NikeGunn/yeti-agent/blob/main/LICENSE)
[![GitHub Stars](https://img.shields.io/github/stars/NikeGunn/yeti-agent?style=social)](https://github.com/NikeGunn/yeti-agent)

**AI-powered browser automation that monitors, tests, and automates production websites.**
**Built in Nepal 🇳🇵 — Used worldwide.**

[Landing Page](https://nikegunn.github.io/yeti-agent) · [PyPI](https://pypi.org/project/yeti-agent/) · [GitHub](https://github.com/NikeGunn/yeti-agent) · [Issues](https://github.com/NikeGunn/yeti-agent/issues)

</div>

---

## Install

```bash
# One-line install (Linux / macOS / Windows via Git Bash)
curl -fsSL https://nikegunn.github.io/yeti-agent/install.sh | bash

# Or via pip / uv
pip install yeti-agent
uv add yeti-agent
```

## Quick Start

```python
from browser_use import Agent, ChatOpenAI

agent = Agent(
    task="Go to HackerNews and find the top 3 AI stories",
    llm=ChatOpenAI(model="gpt-4o"),
)
result = agent.run_sync()
print(result.final_result())
```

That's it. Three lines to a running agent.

## LLM Providers

Yeti Agent works with **8+ LLM providers** out of the box:

```python
from browser_use import ChatOpenAI       # OpenAI (gpt-4o, o3, gpt-5)
from browser_use import ChatAnthropic    # Anthropic (claude-sonnet-4, claude-opus-4)
from browser_use import ChatGoogle       # Google (gemini-2.0-flash, gemini-2.5-pro)
from browser_use import ChatGroq         # Groq (llama-3-70b)
from browser_use import ChatOllama       # Ollama (local models)
from browser_use import ChatAzureOpenAI  # Azure OpenAI
from browser_use import ChatMistral      # Mistral
from browser_use import ChatLiteLLM      # LiteLLM (100+ models)
```

## Production Monitoring

Monitor critical user flows 24/7:

```python
from browser_use import Agent, Browser, BrowserProfile, ChatAnthropic

profile = BrowserProfile(
    headless=True,
    allowed_domains=["your-app.com", "*.your-app.com"],
)

agent = Agent(
    task="""
    1. Go to https://your-app.com/login
    2. Login with test credentials
    3. Navigate to checkout, add an item
    4. Verify payment form loads correctly
    5. Report any errors or broken elements
    """,
    llm=ChatAnthropic(model="claude-sonnet-4-20250514"),
    browser=Browser(browser_profile=profile),
)

result = await agent.run(max_steps=50)
```

## Structured Data Extraction

Extract typed data from any website:

```python
from pydantic import BaseModel
from browser_use import Agent, ChatOpenAI

class Product(BaseModel):
    name: str
    price: float
    rating: float
    in_stock: bool

agent = Agent(
    task="Extract all products from the first page",
    llm=ChatOpenAI(model="gpt-4o"),
    output_model_schema=Product,
)
products = await agent.run()
```

## Custom Tools

Extend the agent with your own functions:

```python
from browser_use import Agent, Tools, ChatGoogle

tools = Tools()

@tools.action(description="Send Slack alert when issue found")
def send_alert(message: str, severity: str) -> str:
    slack.post(channel="#alerts", text=f"[{severity}] {message}")
    return "Alert sent"

agent = Agent(
    task="Monitor checkout flow and alert on any failures",
    llm=ChatGoogle(model="gemini-2.0-flash"),
    tools=tools,
)
```

## CLI

```bash
yeti-agent open https://example.com    # Navigate to URL
yeti-agent state                       # See clickable elements
yeti-agent click 5                     # Click element by index
yeti-agent type "Hello"                # Type text
yeti-agent screenshot page.png         # Take screenshot
yeti-agent close                       # Close browser
yeti-agent init                        # Generate starter template
yeti-agent doctor                      # Check your setup
```

## Template Quickstart

```bash
yeti-agent init --template default
```

Creates a ready-to-run Python script. Available templates:
- **default** — Minimal setup to get started
- **advanced** — All configuration options with comments
- **tools** — Custom tools and extending the agent

## Architecture

```
Your Task (plain English)
    → LLM (GPT / Claude / Gemini / Ollama)
        → Agent (plans & executes)
            → Browser (CDP protocol)
                → Results (typed data)
```

**Event-driven** with watchdog services for security, popups, downloads, DOM changes, and crash recovery — all running independently through an event bus.

## Key Features

| Feature | Description |
|---------|-------------|
| **20+ Browser Actions** | Click, type, scroll, navigate, extract, multi-tab, file upload, PDF export |
| **Security Watchdogs** | Domain allowlists/blocklists, data masking, proxy support, permission handling |
| **Multi-Tab** | Agent manages multiple tabs autonomously — opens, switches, aggregates |
| **MCP Integration** | Works as MCP server for Claude Desktop, connects to external MCP servers |
| **Headless Mode** | Full headless support for CI/CD, serverless, and production |
| **Cloud Browsers** | Connect to cloud CDP endpoints for scalable automation |
| **GIF Generation** | Auto-generate visual recordings of agent runs |
| **Structured Output** | Pydantic v2 models with type validation |
| **Loop Detection** | Catches repetitive agent behavior automatically |
| **Fallback LLM** | Secondary LLM for recovery after failures |

## Configuration

```python
from browser_use import Agent, Browser, BrowserProfile

profile = BrowserProfile(
    headless=True,                                    # No visible browser
    allowed_domains=["*.your-app.com"],               # Security
    proxy={"server": "http://proxy:8080"},            # Proxy
    user_data_dir="./chrome-profile",                 # Persist cookies
)

agent = Agent(
    task="...",
    llm=llm,
    browser=Browser(browser_profile=profile),
    use_vision=True,                                  # Screenshots in prompts
    max_actions_per_step=5,                           # Actions per LLM call
    generate_gif=True,                                # Record run
)
```

## Use Cases

- **Production Monitoring** — Test checkout, login, payments 24/7
- **E2E QA Automation** — Replace brittle Selenium with AI that adapts to UI changes
- **Data Extraction** — Scrape into typed Pydantic models
- **Workflow Automation** — Multi-step form filling, report generation
- **Regression Detection** — Post-release verification smarter than screenshot diffs
- **SLA Compliance** — Verify vendor portals meet requirements

## Python Support

- Python 3.11, 3.12, 3.13
- Fully async (`asyncio`)
- Sync wrapper available (`agent.run_sync()`)

## Development

```bash
git clone https://github.com/NikeGunn/yeti-agent.git
cd yeti-agent
uv venv --python 3.11 && source .venv/bin/activate
uv sync --dev
uv run pytest -vxs tests/ci          # Run tests
uv run pyright                        # Type check
uv run ruff check --fix && uv run ruff format  # Lint
```

## Credits

Yeti Agent is built on top of the [browser-use](https://github.com/browser-use/browser-use) open-source framework. We extend it with production monitoring capabilities, enterprise features, and the Yeti Agent platform.

## License

MIT License — see [LICENSE](LICENSE) for details.

---

<div align="center">

**Made with ❤️ in Nepal 🇳🇵 by [Nikhil Bhagat](https://github.com/NikeGunn)**

[⭐ Star this repo](https://github.com/NikeGunn/yeti-agent) if you find it useful!

</div>
