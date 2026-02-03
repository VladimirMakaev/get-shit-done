# Testing Patterns

**Analysis Date:** 2026-02-02

## Test Framework

**Runner:**
- None detected
- No `jest.config.*`, `vitest.config.*`, or similar found

**Assertion Library:**
- Not applicable

**Run Commands:**
```bash
# No test commands defined in package.json
```

## Test File Organization

**Location:**
- No test files found
- No `.test.js`, `.test.ts`, `.spec.js`, or `.spec.ts` files exist

**Naming:**
- Not applicable

**Structure:**
```
No test directory structure
```

## Test Structure

**Suite Organization:**
Not applicable - no tests present

**Patterns:**
- Codebase relies on manual testing and production validation
- Install script includes verification functions:
  ```javascript
  function verifyInstalled(dirPath, description) {
    if (!fs.existsSync(dirPath)) {
      console.error(`Failed to install ${description}`);
      return false;
    }
    // Check directory has files...
  }
  ```

## Mocking

**Framework:** Not applicable

**Patterns:**
Not applicable

**What to Mock:**
- No established patterns

**What NOT to Mock:**
- No established patterns

## Fixtures and Factories

**Test Data:**
Not applicable

**Location:**
- Templates exist in `get-shit-done/templates/` for runtime use
- Config template at `get-shit-done/templates/config.json`

## Coverage

**Requirements:** None enforced

**View Coverage:**
```bash
# No coverage tooling configured
```

## Test Types

**Unit Tests:**
- Not present

**Integration Tests:**
- Not present

**E2E Tests:**
- Not present

## Common Patterns

**Validation in Production Code:**
The codebase uses inline validation patterns instead of separate tests:

**File Existence Checks:**
```javascript
if (!fs.existsSync(targetDir)) {
  console.log(`Directory does not exist`);
  return;
}
```

**JSON Parsing Safety:**
```javascript
try {
  return JSON.parse(fs.readFileSync(settingsPath, 'utf8'));
} catch (e) {
  return {};
}
```

**Installation Verification:**
```javascript
function verifyFileInstalled(filePath, description) {
  if (!fs.existsSync(filePath)) {
    console.error(`Failed to install ${description}`);
    return false;
  }
  return true;
}
```

## Quality Assurance Strategy

**Current Approach:**
- Manual testing during development
- Production validation via install verification functions
- User feedback from npm installations
- No automated test suite

**Verification Points:**
- Install script verifies each copied directory/file exists
- Tracks installation failures and exits with error code
- Hook scripts fail silently in background (don't break user experience)

**Future Testing Considerations:**
For contributors adding test infrastructure, consider:
- Install/uninstall workflow tests (verify file placement)
- Path transformation tests (Claude→OpenCode→Gemini conversions)
- Cross-platform path handling (Windows vs Unix)
- Settings.json merge logic
- Frontmatter conversion accuracy

---

*Testing analysis: 2026-02-02*
