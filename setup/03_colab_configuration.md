# Google Colab Configuration Guide

## Overview

This guide explains how to securely manage your Claude API key in Google Colab using the Secrets feature. This prevents accidental exposure of sensitive credentials.

---

## Why Use Colab Secrets?

**DON'T DO THIS** (insecure):
```python
# Hardcoding API key in notebook
api_key = "sk-ant-api03-your-actual-key"  # NEVER DO THIS
```

**DO THIS** (secure):
```python
# Using Colab Secrets
from google.colab import userdata
api_key = userdata.get('ANTHROPIC_API_KEY')
```

**Benefits**:
- Keys stored encrypted by Google
- Not visible in notebook source code
- Can share notebooks safely without exposing keys
- Keys persist across Colab sessions

---

## Step 1: Add API Key to Colab Secrets

### Method A: Using Colab UI (Recommended)

1. Open any notebook in Google Colab
2. Click the **key icon** (🔑) in the left sidebar
   - If you don't see it, click the panel toggle to show sidebar
3. Click **"Add new secret"**
4. Configure secret:
   - **Name**: `ANTHROPIC_API_KEY`
   - **Value**: Your Claude API key (e.g., `sk-ant-api03-...`)
5. Toggle **"Notebook access"** to ON for this secret
6. Click **"Save"**

### Method B: Programmatically (Alternative)

Run this code cell in Colab:
```python
from google.colab import userdata
import getpass

# Enter your API key when prompted (input will be hidden)
api_key = getpass.getpass("Enter your Anthropic API key: ")

# Store in secrets
# Note: This is temporary and needs to be done per session
# Using UI method (Method A) is permanent
```

---

## Step 2: Access Secrets in Notebooks

In any course notebook, use this code:
```python
from google.colab import userdata

# Retrieve API key
ANTHROPIC_API_KEY = userdata.get('ANTHROPIC_API_KEY')

# Use with LangChain
from langchain_anthropic import ChatAnthropic

llm = ChatAnthropic(
    model="claude-sonnet-4-20250514",
    anthropic_api_key=ANTHROPIC_API_KEY,
    max_tokens=1000
)

# Test
response = llm.invoke("Hello Claude!")
print(response.content)
```

---

## Step 3: Verify Secret Configuration

Run this verification cell:
```python
from google.colab import userdata

try:
    api_key = userdata.get('ANTHROPIC_API_KEY')

    # Check format
    assert api_key.startswith('sk-ant'), "Key doesn't match expected format"
    assert len(api_key) > 50, "Key seems too short"

    print("✓ Secret configured correctly!")
    print(f"Key prefix: {api_key[:15]}...")  # Show only first 15 chars

except userdata.SecretNotFoundError:
    print("ERROR: ANTHROPIC_API_KEY not found in secrets")
    print("Please add it using the key icon in left sidebar")
except Exception as e:
    print(f"ERROR: {e}")
```

Expected output:
```
✓ Secret configured correctly!
Key prefix: sk-ant-api03-X...
```

---

## Common Issues

### Issue: "SecretNotFoundError"

**Cause**: Secret not added or named incorrectly

**Solution**:
1. Open secrets panel (key icon 🔑)
2. Verify secret name is exactly `ANTHROPIC_API_KEY`
3. Check "Notebook access" toggle is ON
4. Restart runtime (Runtime → Restart runtime)

### Issue: "NotebookAccessDisabledError"

**Cause**: Secret exists but access not granted

**Solution**:
1. Open secrets panel
2. Find `ANTHROPIC_API_KEY`
3. Toggle "Notebook access" to ON
4. Save changes

### Issue: Key works in one notebook but not another

**Cause**: Secrets are per-notebook by default

**Solution**:
- Option 1: Enable access for each notebook individually
- Option 2: Set as default for all notebooks (in secrets settings)

---

## Managing Multiple API Keys

If you have multiple API keys (e.g., production vs development):
```python
from google.colab import userdata

# Add multiple secrets with descriptive names
PROD_KEY = userdata.get('ANTHROPIC_API_KEY_PROD')
DEV_KEY = userdata.get('ANTHROPIC_API_KEY_DEV')

# Use appropriate key based on context
current_key = PROD_KEY if IS_PRODUCTION else DEV_KEY
```

---

## Security Best Practices

### DO:
- Use Colab Secrets for all API keys
- Set unique names (e.g., `ANTHROPIC_API_KEY`, `LANGSMITH_API_KEY`)
- Review which notebooks have access to secrets
- Delete secrets after course if not continuing

### DON'T:
- Print full API keys in notebook output
- Hardcode keys in notebook cells
- Share notebooks with secrets embedded
- Use same key across personal and course work

---

## Alternative: Environment Variables (Local Development)

If working locally instead of Colab:

Create `.env` file:
```
ANTHROPIC_API_KEY=sk-ant-api03-your-key-here
```

Load in Python:
```python
from dotenv import load_dotenv
import os

load_dotenv()
api_key = os.getenv("ANTHROPIC_API_KEY")
```

**Remember**: Add `.env` to `.gitignore` (already included in this repo)

---

## Colab-Specific Features

### Runtime Persistence
- Secrets persist across runtime restarts
- Remain available even if runtime disconnects
- Stored at Google account level

### Sharing Notebooks
When you share a Colab notebook:
- Recipients DON'T get your secrets
- They must add their own API keys
- Notebooks remain executable with their credentials

### GitHub Integration
Colab notebooks stored in GitHub:
- Secrets NOT included in repository
- Each collaborator manages their own secrets
- Safe to make repositories public

---

## Testing Your Configuration

Run the complete verification notebook: [setup/04_verify_setup.ipynb](04_verify_setup.ipynb)

This tests:
- Secret retrieval
- API connectivity
- LangChain integration
- Sample agent execution

---

## Next Steps

After configuring secrets:
1. Run [setup/04_verify_setup.ipynb](04_verify_setup.ipynb)
2. Verify all tests pass
3. You're ready for Day 1!

---

**Questions?** Contact instructor: javier@jmarin.info
