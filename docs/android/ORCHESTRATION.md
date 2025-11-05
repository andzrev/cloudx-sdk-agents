# CloudX SDK Integration Subagents

This document describes the specialized Claude Code subagents for integrating CloudX SDK first look alongside Google AdMob and AppLovin MAX with proper fallback.

## Overview

CloudX SDK integration uses a **multi-agent architecture** where specialized subagents handle different aspects of the integration:

```
┌─────────────────────────────────────────────────┐
│           You (or Main Claude Agent)            │
│              Integration Coordinator             │
└──────────────┬──────────────────────────────────┘
               │
               ├──► @agent-cloudx-android-integrator      (Implementation)
               ├──► @agent-cloudx-android-auditor         (Validation)
               ├──► @agent-cloudx-android-build-verifier  (Testing)
               └──► @agent-cloudx-android-privacy-checker (Compliance)
```

## Subagent Reference

### 1. @agent-cloudx-android-integrator
**Purpose:** Implements CloudX SDK integration with first look fallback

**Responsibilities:**
- Adds CloudX SDK dependencies to build.gradle
- Implements CloudX initialization in Application class
- Creates ad loading managers with fallback logic
- Updates existing ad code to try CloudX first
- Ensures proper API usage (correct names, explicit `.load()` calls)

**When to use:**
- "Integrate CloudX SDK into my app"
- "Add CloudX as primary ad network with AdMob fallback"
- "Update banner ads to try CloudX first"

**Tools:** Read, Write, Edit, Grep, Glob, Bash
**Model:** Sonnet (requires reasoning for code generation)

---

### 2. @agent-cloudx-android-auditor
**Purpose:** Validates that AdMob/AppLovin fallback paths remain intact

**Responsibilities:**
- Verifies existing ad SDK initialization still runs
- Confirms fallback triggers in `onAdLoadFailed` callbacks
- Checks state flags track which SDK loaded
- Ensures analytics/tracking hooks remain
- Flags removed or broken fallback code

**When to use:**
- "Verify my AdMob fallback still works"
- "Audit the integration for regressions"
- "Check if fallback will trigger correctly"

**Tools:** Read, Grep, Glob
**Model:** Sonnet (requires analysis)

---

### 3. @agent-cloudx-android-build-verifier
**Purpose:** Runs Gradle builds and tests to catch compilation errors

**Responsibilities:**
- Executes Gradle build commands
- Captures and parses build output
- Identifies compilation errors with file:line references
- Suggests fixes for common issues
- Reports dependency conflicts

**When to use:**
- "Build the project and check for errors"
- "Run tests to verify integration"
- "Check if the app still compiles"

**Tools:** Bash, Read
**Model:** Haiku (fast execution, simple parsing)

---

### 4. @agent-cloudx-android-privacy-checker
**Purpose:** Validates privacy compliance (GDPR, CCPA, COPPA)

**Responsibilities:**
- Verifies CloudX privacy API usage
- Checks IAB consent string handling
- Ensures privacy signals pass to all SDKs
- Validates consent flow timing
- Flags privacy violations

**When to use:**
- "Check privacy compliance after integration"
- "Verify GDPR consent handling"
- "Ensure CloudXPrivacy is configured correctly"

**Tools:** Read, Grep, Glob
**Model:** Haiku (fast checklist validation)

---

## Integration Workflow

### Phase 1: Implementation
```
You: "Integrate CloudX SDK with AdMob fallback in my app"

→ @agent-cloudx-android-integrator implements:
  ├─ Adds dependencies
  ├─ Updates Application.onCreate()
  ├─ Creates BannerAdManager with first look
  ├─ Creates InterstitialAdManager with first look
  └─ Creates RewardedAdManager with first look
```

### Phase 2: Validation
```
You: "Audit the integration to ensure AdMob fallback works"

→ @agent-cloudx-android-auditor checks:
  ├─ AdMob initialization still present
  ├─ Fallback triggers in onAdLoadFailed
  ├─ State flags track load status
  ├─ Show logic checks both SDKs
  └─ Reports: ✅ PASS or ❌ FAIL with fixes
```

### Phase 3: Testing
```
You: "Build the app and run tests"

→ @agent-cloudx-android-build-verifier runs:
  ├─ ./gradlew clean build
  ├─ ./gradlew test
  └─ Reports: ✅ SUCCESS or ❌ FAIL with error details
```

### Phase 4: Compliance
```
You: "Check privacy compliance"

→ @agent-cloudx-android-privacy-checker validates:
  ├─ CloudXPrivacy API used correctly
  ├─ Consent flow timing
  ├─ IAB SharedPreferences
  ├─ Privacy consistency across SDKs
  └─ Reports: ✅ COMPLIANT or ❌ ISSUES with fixes
```

---

## Usage Examples

### Example 1: Full Integration from Scratch
```
You: "I need to integrate CloudX SDK into my Android app that currently uses AdMob for banner and interstitial ads. Keep AdMob as fallback."

Step 1: Use @agent-cloudx-android-integrator
"Use @agent-cloudx-android-integrator to add CloudX SDK with AdMob fallback"

Step 2: Use @agent-cloudx-android-auditor
"Use @agent-cloudx-android-auditor to verify AdMob fallback paths are intact"

Step 3: Use @agent-cloudx-android-build-verifier
"Use @agent-cloudx-android-build-verifier to build the project"

Step 4: Use @agent-cloudx-android-privacy-checker
"Use @agent-cloudx-android-privacy-checker to validate privacy compliance"
```

### Example 2: Fix Existing Integration
```
You: "My CloudX integration isn't working. Ads aren't loading."

Step 1: Use @agent-cloudx-android-auditor
"Use @agent-cloudx-android-auditor to find what's broken in the integration"

→ Auditor finds: "Missing .load() call on CloudXAdView"

Step 2: Use @agent-cloudx-android-integrator
"Use @agent-cloudx-android-integrator to fix: add explicit .load() calls to banner ads"

Step 3: Use @agent-cloudx-android-build-verifier
"Use @agent-cloudx-android-build-verifier to verify the fix compiles"
```

### Example 3: Privacy Audit
```
You: "I need to ensure my CloudX integration is GDPR compliant before launching in EU"

Use @agent-cloudx-android-privacy-checker
"Use @agent-cloudx-android-privacy-checker to audit GDPR compliance"

→ Checker reports:
  ❌ CloudXPrivacy uses wrong field names
  ⚠️  Privacy set after ads loaded
  ✅ IAB TCF string present

Fix issues with @agent-cloudx-android-integrator
```

---

## Agent Communication Patterns

### Sequential (Recommended)
Agents run one after another, each building on the previous:
```
Integrator → Auditor → Build Verifier → Privacy Checker
```

### Parallel (Advanced)
Independent agents can run simultaneously:
```
Auditor + Privacy Checker (both read-only, no conflicts)
```

### Iterative (Debugging)
Loop until all checks pass:
```
1. Integrator makes changes
2. Build Verifier tests → FAIL
3. Integrator fixes errors
4. Build Verifier tests → PASS
5. Auditor validates → PASS
```

---

## Installation

Agents are already installed in this project at:
```
.claude/agents/
├── cloudx-android-integrator.md
├── cloudx-android-auditor.md
├── cloudx-android-build-verifier.md
└── cloudx-android-privacy-checker.md
```

To use globally across all projects:
```bash
# Copy to user directory
cp .claude/agents/*.md ~/.claude/agents/
```

---

## Invoking Agents

### Method 1: Explicit (Recommended)
```
Use @agent-cloudx-android-integrator to integrate CloudX SDK
Use @agent-cloudx-android-auditor to check fallback paths
Use @agent-cloudx-android-build-verifier to run ./gradlew build
Use @agent-cloudx-android-privacy-checker to validate GDPR compliance
```

### Method 2: Implicit (Auto-routing)
Claude Code may automatically invoke agents based on your request:
```
"Integrate CloudX SDK with AdMob fallback"
→ Automatically routes to @agent-cloudx-android-integrator

"Check if the build succeeds"
→ Automatically routes to @agent-cloudx-android-build-verifier
```

---

## Agent Coordination

### When integrator needs validation:
```kotlin
// In @agent-cloudx-android-integrator response:
"I've implemented the first look logic. For validation that fallback
paths are correct, please use: @agent-cloudx-android-auditor"
```

### When auditor finds issues:
```kotlin
// In @agent-cloudx-android-auditor response:
"❌ FAIL: Missing fallback trigger in BannerFragment.kt:78
To fix this, use: @agent-cloudx-android-integrator"
```

### When build fails:
```kotlin
// In @agent-cloudx-android-build-verifier response:
"❌ BUILD FAILED: Compilation error detected
Check error details and use @agent-cloudx-android-integrator to fix if needed"
```

---

## Best Practices

### 1. Always Audit After Integration
```bash
✅ Integrator → Auditor → Build Verifier
❌ Integrator only (might miss fallback issues)
```

### 2. Run Build Verifier Early
Catch compilation errors before they compound:
```bash
✅ Change code → Build → Fix errors → Repeat
❌ Make all changes → Build → 50 errors
```

### 3. Check Privacy Before Production
```bash
✅ Privacy Checker before app store submission
❌ Skip privacy checks (regulatory risk)
```

### 4. Use Specific Agent Commands
```bash
✅ "Use @agent-cloudx-android-integrator to add banner first look"
❌ "Fix my ads" (ambiguous, might not route correctly)
```

---

## Troubleshooting

### "Agent not found"
**Problem:** Agent files not in `.claude/agents/`
**Solution:** Copy from this repo or create symlinks

### "Agent does wrong task"
**Problem:** Implicit routing sent to wrong agent
**Solution:** Use explicit "Use @agent-cloudx-X to..." syntax

### "Agents conflict"
**Problem:** Multiple agents editing same files
**Solution:** Run sequentially, not in parallel for write operations

### "Build verifier takes too long"
**Problem:** Uses Sonnet instead of Haiku
**Solution:** Verify `model: haiku` in agent frontmatter

---

## API Reference Quick Links

- **CloudX SDK docs:** See [CLAUDE.md](./CLAUDE.md)
- **Integration patterns:** See [integration_agent_claude.md](./integration_agent_claude.md)
- **Architecture:** See [integration_agent_codex.md](./integration_agent_codex.md)

---

## Support

For issues with:
- **SDK behavior:** CloudX support
- **Agent behavior:** Claude Code GitHub issues
- **Integration strategy:** Use @agent-cloudx-android-integrator agent

---

**Ready to integrate CloudX SDK?**

Start with: `Use @agent-cloudx-android-integrator to integrate CloudX SDK with AdMob fallback`
