# Bridge M3.3 → M3.4: Readiness Check

## Overview

This repository contains the readiness check notebook for the bridge between **M3.3 (API Development & Security)** and **M3.4 (Load Testing & Scaling)** in the Production Deployment module.

The notebook verifies that your API security implementation is working correctly before you move on to performance capacity testing.

## Files

- **Bridge_M3.3_to_M3.4_Readiness.ipynb** - Interactive readiness check notebook
- **bridge_M3_3_to_M3_4.md** - Source bridge script documentation
- **README.md** - This file

## Prerequisites

Before running the readiness checks:

1. **Completed M3.3**: You should have finished the API Development & Security video and hands-on work
2. **Python 3.7+**: Required for running the Jupyter notebook
3. **curl**: Command-line tool for HTTP requests (pre-installed on most systems)
4. **Optional - Deployed API**: You can run checks in offline mode to see examples, or against your deployed API

## Installation

### 1. Clone this repository

```bash
git clone https://github.com/yesvisare/ccc_l1_bridge.git
cd ccc_l1_bridge
```

### 2. Install Jupyter (if not already installed)

```bash
pip install jupyter notebook
# OR
pip install jupyterlab
```

### 3. Launch Jupyter

```bash
jupyter notebook
# OR
jupyter lab
```

### 4. Open the notebook

Navigate to `Bridge_M3.3_to_M3.4_Readiness.ipynb` in the Jupyter interface.

## How to Use

### Offline Mode (Default)

By default, the notebook runs in **offline mode**, which means:
- No actual API calls are made
- Example outputs are displayed for each checkpoint
- Safe to run without a deployed API
- Perfect for understanding what each test does

**To use offline mode:**
1. Open the notebook
2. Run all cells in order (Cell > Run All)
3. Review the example outputs and expected behaviors

### Live Mode (Against Your API)

To test your actual deployed API:

1. **Update Configuration** (Cell 3):
   ```python
   API_URL = "https://your-actual-api.com/query"
   VALID_API_KEY = "your_real_api_key_here"
   OFFLINE_MODE = False
   ```

2. **Run Sequentially**: Execute cells from top to bottom to:
   - Test authentication (401 checks)
   - Test rate limiting (429 checks)
   - Test input validation (injection blocking)
   - Check security logs

3. **Review Results**: Each checkpoint will show:
   - ✓ PASS: Test succeeded
   - ✗ FAIL: Issue detected - action required
   - ⚠️  WARNING: Inconclusive or needs attention

## Safety Guidelines

### ⚠️ IMPORTANT: Only Test Your Own API

**DO NOT run these tests against:**
- APIs you don't own
- Production systems without permission
- Third-party services
- Shared development environments without approval

**Why?**
- Rate limit tests generate 100+ rapid requests
- Injection tests send attack payloads
- These actions may trigger security alerts or blocks
- Unauthorized testing may violate terms of service or laws

### Safe Testing Practices

✅ **DO:**
- Run offline mode first to understand the tests
- Test against your own API in a development/test environment
- Coordinate with your team before running load tests
- Monitor your API logs during testing
- Start with small request counts before scaling up

❌ **DON'T:**
- Run rate limit tests against production without planning
- Test injection payloads against live user-facing systems
- Execute tests without understanding what they do
- Share API keys in the notebook (they're excluded from git)
- Run tests during business hours if they could impact users

## Checkpoint Details

### Checkpoint 1: Authentication (401)
Tests three scenarios:
1. Missing API key → Should return 401
2. Invalid API key → Should return 401
3. Valid API key → Should return 200

**Impact**: Ensures unauthorized users cannot access your API.

### Checkpoint 2: Rate Limiting (429)
Sends multiple rapid requests to verify:
- First ~60 requests/minute succeed
- Additional requests return 429
- Retry-After headers are present

**Impact**: Prevents abuse and resource exhaustion.

### Checkpoint 3: Input Validation
Tests 8 attack patterns:
- SQL Injection (2 variants)
- Prompt Injection (2 variants)
- XSS (2 variants)
- Path Traversal (2 variants)

**Impact**: Blocks 90-95% of common attack vectors.

### Checkpoint 4: Security Logging
Checks that your security.log contains:
- Authentication failures
- Rate limit violations
- Validation errors
- Successful requests

**Impact**: Enables threat detection and incident response.

## Troubleshooting

### "Could not connect to API"
- Check that API_URL is correct and accessible
- Verify your API is running
- Check firewall/network settings
- Try offline mode first

### "Permission denied reading log file"
- Update SECURITY_LOG_PATH to your actual log location
- Try: `sudo cat /path/to/security.log`
- Check file permissions
- Run in offline mode to see example log format

### "All rate limit tests passed (no 429)"
- Your rate limits might be higher than test count
- Try the full bash script (100 requests) instead of the notebook's 20-request test
- Reduce delay between requests
- Check rate limit configuration

### "Attack payloads returned 200 instead of 400"
- Your input validation may need strengthening
- Review M3.3 video for validation implementation
- Check validation rules and regex patterns
- Ensure middleware is properly configured

## What's Next?

Once all four checkpoints pass:

✓ Your API security is production-ready
✓ You're ready to test performance capacity
✓ Proceed to **M3.4: Load Testing & Scaling**

If any checkpoints fail:
1. Revisit M3.3 hands-on video
2. Fix the failing security controls
3. Re-run this readiness check
4. Then proceed to M3.4

## Resources

- **Bridge Script**: See `bridge_M3_3_to_M3_4.md` for the complete bridge video narration
- **Module Context**: This bridges M3.3 (API Development & Security) → M3.4 (Load Testing & Scaling)
- **Expected Duration**: 15-20 minutes to run all checks

## Security Note

This notebook contains security testing tools. The attack payloads are:
- Educational and standard security testing patterns
- Safe to run against your own systems
- Designed to verify your defenses work correctly
- Should be blocked by properly configured validation

Always obtain proper authorization before security testing any system.

## License

This is educational content for the Production Deployment module. Use responsibly and ethically.

---

**Ready to verify your API security and move to performance testing?**

Run the notebook and ensure all checkpoints pass! 🚀
