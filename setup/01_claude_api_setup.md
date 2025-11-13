# Claude API Setup Guide

## Overview

This course uses Claude 4 Sonnet via the Anthropic API for all agent demonstrations and homework. You need an API key to run code outside of live instructor sessions.

**Cost estimate**: $10-20 for all homework assignments across the course.

---

## Step 1: Create Anthropic Account

1. Navigate to: https://console.anthropic.com
2. Click "Sign Up" (top right)
3. Use email or Google/GitHub authentication
4. Verify your email address

---

## Step 2: Add Payment Method

**Important**: The API requires a payment method even though there's a free tier.

1. Log in to Anthropic Console: https://console.anthropic.com
2. Click your profile (top right) → "Billing"
3. Click "Add payment method"
4. Enter credit card details
5. Confirm billing information

**Free tier limits**:
- $5 of free credits for new accounts
- Should cover initial experimentation
- After free credits: pay-as-you-go pricing

---

## Step 3: Generate API Key

1. In Anthropic Console, navigate to "API Keys"
   - Direct link: https://console.anthropic.com/settings/keys
2. Click "Create Key"
3. Name your key: "Agents_Bootcamp" (for easy identification)
4. Copy the key immediately - you won't see it again
5. Store securely (see Security section below)

**Your API key looks like**: `sk-ant-api03-...` (starts with `sk-ant`)

---

## Step 4: Test API Key

Run this test in a new Python environment:
```python
import anthropic

# Replace with your actual API key
client = anthropic.Anthropic(api_key="sk-ant-api03-YOUR_KEY_HERE")

message = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=100,
    messages=[
        {"role": "user", "content": "Say 'API key working' if you receive this."}
    ]
)

print(message.content[0].text)
```

Expected output: "API key working" (or similar confirmation)

---

## Security Best Practices

### DO:
- Store API key in Google Colab Secrets (see [setup/03_colab_configuration.md](03_colab_configuration.md))
- Use environment variables locally (never hardcode)
- Regenerate key if accidentally exposed
- Delete unused keys from Anthropic Console

### DON'T:
- Commit API keys to Git repositories
- Share API keys in Slack/email/screenshots
- Store in plain text files
- Use the same key across multiple projects

---

## Cost Management

### Monitor Usage

1. Check usage dashboard: https://console.anthropic.com/settings/usage
2. Set up budget alerts (Billing → Alerts)
3. Review charges weekly during the course

### Cost Optimization Tips

- Use `max_tokens` parameter to limit response length
- Cache system prompts (reduces costs 90% for repeated calls)
- Test with small examples before running on full datasets

**Typical homework costs**:
- Chapter 1: $2-3
- Chapter 2: $3-4
- Chapter 3: $2-3
- Chapter 4: $3-4
- Final project: $5-7

---

## Troubleshooting

### "Invalid API Key" Error

**Cause**: Key copied incorrectly or expired

**Solution**:
1. Regenerate key in Anthropic Console
2. Ensure no extra spaces when copying
3. Verify key starts with `sk-ant-api03-`

### "Insufficient Credits" Error

**Cause**: Free tier exhausted or payment method declined

**Solution**:
1. Check billing dashboard: https://console.anthropic.com/settings/billing
2. Verify payment method is active
3. Add backup payment method

### "Rate Limit Exceeded" Error

**Cause**: Too many requests in short time

**Solution**:
1. Add delays between API calls (`time.sleep(1)`)
2. Use LangChain's built-in rate limiting
3. Batch requests where possible

---

## API Key Lifecycle

### During the Course
- Keep your key active throughout all 3 days
- Monitor usage weekly
- Don't delete until final project submitted

### After the Course
- Option 1: Keep key for continued learning (set budget alerts)
- Option 2: Delete key from Anthropic Console
- Option 3: Rotate key (delete old, create new)

---

## Alternative: Using Instructor's Key

**During live sessions only**, you can follow along without your own API key. The instructor runs all live examples.

**For homework and practice**, you must use your own API key.

---

## Additional Resources

- Anthropic API Documentation: https://docs.anthropic.com
- Pricing calculator: https://console.anthropic.com/settings/cost
- API status page: https://status.anthropic.com
- Support: support@anthropic.com

---

## Next Steps

After obtaining your API key:
1. Proceed to [setup/02_langchain_installation.md](02_langchain_installation.md)
2. Configure Colab secrets: [setup/03_colab_configuration.md](03_colab_configuration.md)
3. Verify setup: [setup/04_verify_setup.ipynb](04_verify_setup.ipynb)

---

**Questions?** Contact instructor: javier@jmarin.info
