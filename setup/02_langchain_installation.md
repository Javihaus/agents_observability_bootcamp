# LangChain Installation Guide

## Overview

LangChain is the primary framework for building agentic systems in this course. Since we use Google Colab, installation is handled per-notebook, but this guide explains the components and local setup (optional).

---

## Google Colab Installation (Recommended)

Each course notebook begins with this cell:
```python
# Install dependencies
!pip install -q langchain==0.1.0 langchain-anthropic==0.1.1 anthropic==0.18.1
!pip install -q python-dotenv pandas numpy matplotlib seaborn

print("Installation complete!")
```

**You don't need to do anything** - just run the first cell of each notebook.

---

## Why These Specific Versions?

| Package | Version | Reason |
|---------|---------|--------|
| `langchain` | 0.1.0 | Latest stable release with agent improvements |
| `langchain-anthropic` | 0.1.1 | Official Claude integration |
| `anthropic` | 0.18.1 | Claude 4 Sonnet support |

**Version locking ensures consistency** - all students run identical code.

---

## Local Installation (Optional)

If you want to work locally instead of Colab:

### Prerequisites
- Python 3.9+ installed
- pip package manager
- Virtual environment tool (venv or conda)

### Steps
```bash
# Create virtual environment
python -m venv agents_env

# Activate (macOS/Linux)
source agents_env/bin/activate

# Activate (Windows)
agents_env\Scripts\activate

# Install from requirements.txt
pip install -r requirements.txt

# Verify installation
python -c "import langchain; print(langchain.__version__)"
```

Expected output: `0.1.0`

---

## LangChain Components Used in Course

### Core Modules

**LangChain Core** (`langchain-core`)
- Message schemas (HumanMessage, AIMessage, SystemMessage)
- Runnable interface (chain abstraction)
- Callbacks and tracing

**LangChain Community** (`langchain-community`)
- Third-party integrations
- Additional tools and utilities

**LangChain Anthropic** (`langchain-anthropic`)
- Claude model wrapper (ChatAnthropic)
- Streaming support
- Function calling integration

### What We Won't Use

- LangChain loaders (we use direct API calls)
- Vector databases (out of scope for this course)
- Memory persistence (teaching stateless patterns)

---

## Verifying Installation

Run this code to verify all components:
```python
# Test LangChain
from langchain.schema import HumanMessage, SystemMessage
print("LangChain core: OK")

# Test Anthropic integration
from langchain_anthropic import ChatAnthropic
print("Anthropic integration: OK")

# Test callback system
from langchain.callbacks import StdOutCallbackHandler
print("Callbacks: OK")

# Test version
import langchain
assert langchain.__version__ == "0.1.0", "Wrong version!"
print(f"Version check: {langchain.__version__}")

print("\nAll components verified!")
```

---

## Common Installation Issues

### Issue: "No module named 'langchain'"

**Cause**: Package not installed in active environment

**Solution**:
```bash
# In Colab
!pip install langchain

# Locally
pip install langchain
```

### Issue: "ImportError: cannot import name 'ChatAnthropic'"

**Cause**: Missing `langchain-anthropic` package

**Solution**:
```bash
pip install langchain-anthropic
```

### Issue: Version conflicts

**Cause**: Existing incompatible packages

**Solution**:
```bash
# Uninstall all LangChain packages
pip uninstall -y langchain langchain-core langchain-anthropic

# Reinstall from requirements.txt
pip install -r requirements.txt
```

---

## LangSmith (Optional but Recommended)

LangSmith provides advanced tracing for debugging agents. It's optional for this course but highly useful.

### Setup LangSmith

1. Create account: https://smith.langchain.com
2. Get API key: https://smith.langchain.com/settings
3. Set environment variables:
```python
import os
os.environ["LANGCHAIN_TRACING_V2"] = "true"
os.environ["LANGCHAIN_API_KEY"] = "your_langsmith_key"
```

4. View traces: https://smith.langchain.com/traces

**Cost**: Free tier includes 5,000 traces/month (sufficient for this course)

---

## Package Documentation

- LangChain docs: https://python.langchain.com/docs/get_started/introduction
- Anthropic integration: https://python.langchain.com/docs/integrations/chat/anthropic
- API reference: https://api.python.langchain.com/en/latest/

---

## Updating Packages (After Course)

To get latest versions after the course ends:
```bash
pip install --upgrade langchain langchain-anthropic anthropic
```

**Warning**: Breaking changes may occur. Course notebooks tested with versions in `requirements.txt`.

---

## Next Steps

After installing LangChain:
1. Configure Google Colab secrets: [setup/03_colab_configuration.md](03_colab_configuration.md)
2. Run verification notebook: [setup/04_verify_setup.ipynb](04_verify_setup.ipynb)

---

**Questions?** Contact instructor: javier@jmarin.info
