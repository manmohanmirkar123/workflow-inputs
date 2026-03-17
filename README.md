# GitHub Actions Workflow: checking-inputs

## Overview

This GitHub Actions workflow demonstrates how to **validate and use different types of inputs** in a `workflow_dispatch` event. It's designed to understand GitHub Actions inputs, their types, validation, and conditional execution.

The workflow is triggered manually (via **workflow_dispatch**) and accepts 5 different input types:

1. **Boolean** (`run_tests`)
2. **Choice** (`test_type`)
3. **Environment** (`environment`)
4. **Number** (`build-number`)
5. **String** (`message`)

## Workflow YAML Breakdown

```yaml
name: checking-inputs
on:
  workflow_dispatch:
    inputs:
      run_tests: # use underscore instead of hyphen
        description: "run tests?"
        type: boolean
        required: true
      test_type:
        description: "type of test to run"
        type: choice
        required: true
        options:
          - unit
          - integration
          - all
      environment:
        description: "choose an environment"
        required: true
        type: environment
      build-number:
        description: "custom build number"
        required: false
        type: number
      message:
        description: "Additional message"
        type: string
        required: false
```

### Key Input Features Explained:

| Input          | Type          | Required | Description          | Validation                                                           |
| -------------- | ------------- | -------- | -------------------- | -------------------------------------------------------------------- |
| `run_tests`    | `boolean`     | ✅ Yes   | Toggle to run tests  | Checkbox (true/false)                                                |
| `test_type`    | `choice`      | ✅ Yes   | Test suite selection | Dropdown: `unit`, `integration`, `all`                               |
| `environment`  | `environment` | ✅ Yes   | Deployment target    | Dropdown of GitHub Environments (create via Settings > Environments) |
| `build-number` | `number`      | ❌ No    | Custom build ID      | Numeric input only                                                   |
| `message`      | `string`      | ❌ No    | Free text            | Multiline text field                                                 |

## Jobs & Steps

### Job: `build-deploy`

```yaml
jobs:
  build-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: printing workflow inputs
        run: |
          echo "Run tests: ${{ github.event.inputs.run_tests }}"
          echo "Test type: ${{ github.event.inputs.test_type }}"
          echo "Environment: ${{ github.event.inputs.environment }}"
          echo "Build number: ${{ github.event.inputs.build-number }}"
          echo "Message: ${{ github.event.inputs.message }}"

      - name: Run Tests (if selected)
        if: ${{ inputs.run_tests == true}}
        run: echo "Testing ......."
```

**Step 1: Print All Inputs**

- Always runs
- Demonstrates accessing inputs via `${{ github.event.inputs.input-name }}`
- Shows **default values** for optional inputs (empty if not provided)

**Step 2: Conditional Test Execution**

- Uses `if:` condition: `${{ inputs.run_tests == true }}`
- **Shorthand syntax** `inputs.name` (vs full `github.event.inputs.name`)
- Only executes when boolean input is `true`

## How to Use (For Freshers)

### 1. **Save the Workflow**

```
.github/workflows/checking-inputs.yml
```

### 2. **Create GitHub Environments (Required for `environment` input)**

**If no environments exist:**

1. Go to repo → **Settings** tab
2. Sidebar → **Environments**
3. Click **New environment** → Name it (e.g., `production`, `staging`)
4. Add **Protection rules** if needed (optional)
5. Save ✅

**Environment input will now show your created environments in dropdown!**

### 3. **Trigger Manually**

1. Go to repo → **Actions** tab
2. Select **checking-inputs** workflow
3. Click **Run workflow** → Fill inputs → **Run workflow**

### 3. **Example Input Combinations**

**Scenario A: Full Tests**

```
✓ run_tests: true
test_type: all
environment: production
build-number: 42
message: "Release v1.0.0"
```

**Scenario B: Quick Unit Tests**

```
✓ run_tests: true
test_type: unit
environment: staging
# build-number & message empty
```

**Scenario C: Dry Run (No Tests)**

```
✗ run_tests: false
# Other inputs ignored due to conditional logic
```

## Best Practices Demonstrated

✅ **Use underscores** in input names (not hyphens)  
✅ **Mark required inputs** explicitly  
✅ **Provide clear descriptions**  
✅ **Use conditionals** for dynamic behavior  
✅ **Short syntax** `${{ inputs.name }}` in `if:` conditions  
✅ **Handle optional inputs** gracefully (they print as empty)

## Common Mistakes to Avoid

❌ Don't use `${{ inputs.name }}` in first step (use full path)  
❌ Forget `required: true` for mandatory inputs  
❌ Mix hyphen/underscore in names  
❌ No descriptions (users get confused)

## Testing the Workflow

1. **Commit** to `.github/workflows/checking-inputs.yml`
2. **Manual trigger** with different input combinations
3. **Check logs** to verify input values
4. **Verify conditional logic** (test step only runs when selected)

This workflow serves as a **foundation** for more complex CI/CD pipelines with input validation!

---

**Pro Tip:** Environment input requires GitHub Environments to be pre-configured in repo Settings!

---

