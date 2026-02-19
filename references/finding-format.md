# Finding Format Template

## Per-finding structure

Each finding MUST follow this exact structure:

```
N. Brief title

   **Status:**
   🔴 Critical

   **Files:**
   - `file.ts:42`
   - `other.ts:15`

   **Description:**
   Explanation text.
   ```lang
   problematicCode()
   ```

   **Fix:**
   Suggested fix text.
   ```lang
   betterCode()
   ```
```

## Format rules

- **Only the finding title is numbered** (`1.`, `2.`, etc.). Labels (**Status:**, **Files:**, **Description:**, **Fix:**) are NEVER numbered — they are plain bold text on their own line
- Each label is on its own line; value starts on the **next** line (not on the same line as the label)
- **Empty line between findings** — before every `N.` except the first one in a category
- Add an empty line before each label block (**Status:**, **Files:**, **Description:**, **Fix:**)
- Empty categories are **not rendered** (if no Security findings — no Security section)
- Numbering **restarts at 1 in each category**
- Within a category, findings are sorted by severity: 🔴 first, then 🟠, 🟡, 🟢
- Severity summary appears only in the Next Steps section (one line)
- Description MUST include actual code blocks showing the problematic fragment
- Fix MUST include a code block with the suggested change when applicable

## Common mistakes — DO NOT

```
WRONG — numbered labels:
1. Title
  1. Status:        ← WRONG: label must NOT be numbered
  🔴 Critical
  1. Files:          ← WRONG: label must NOT be numbered

WRONG — no gap between findings:
1. First finding
   ...
2. Second finding   ← WRONG: must have an empty line above

WRONG — value on same line as label:
   **Status:** 🔴 Critical         ← WRONG: value must be on the next line
   **Description:** Some text...   ← WRONG: value must be on the next line
```

## Examples by category

### 🛡 Security & Reliability

1. SQL injection via unsanitized input

   **Status:**
   🔴 Critical

   **Files:**
   - `api/users.ts:42`

   **Description:**
   User input passed directly to query without sanitization.
   ```ts
   db.query(`SELECT * FROM users WHERE id = ${req.params.id}`)
   ```

   **Fix:**
   Use parameterized query.
   ```ts
   db.query('SELECT * FROM users WHERE id = $1', [req.params.id])
   ```

### 🏗 Architecture & SOLID

1. God-hook violates ISP

   **Status:**
   🟠 High

   **Files:**
   - `hooks/useApp.ts:1`

   **Description:**
   `useApp()` returns 12 fields but consumers use 2-3. Violates ISP.
   ```ts
   useApp()
   ```

   **Fix:**
   Split into focused hooks.
   ```ts
   useAuth(), useProfile()
   ```

### ⚡ Performance

1. Nested iteration — **O(n²)**

   **Status:**
   🟡 Medium

   **Files:**
   - `utils/merge.ts:42`

   **Description:**
   Nested iteration degrades on large lists.
   ```ts
   items.forEach(() => list.find(...))
   ```

   **Fix:**
   Replace with Map lookup.
   ```ts
   const map = new Map(list.map(x => [x.id, x]))
   ```

### 🧹 Code Quality

1. Swallowed exception

   **Status:**
   🟢 Low

   **Files:**
   - `services/api.ts:42`

   **Description:**
   Silently swallows error — failures invisible in production.
   ```ts
   catch (e) {}
   ```

   **Fix:**
   Add logging.
   ```ts
   catch (e) { logger.error(e); }
   ```
