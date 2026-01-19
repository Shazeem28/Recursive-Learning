# TypeScript Circular Dependency Detection and Resolution

| Field | Value |
|-------|-------|
| Name | `typescript-circular-dependency` |
| Description | Detect and fix TypeScript circular dependency errors causing undefined imports, runtime errors, or "Cannot access before initialization" in Node.js/bundlers |
| Author | claudeception |
| Version | 1.0.0 |
| Date | 2024-01-15 |

## Problem

Circular dependencies occur when Module A imports Module B, and Module B imports Module A (directly or through a chain). TypeScript compiles fine, but at runtime you get `undefined` values or "Cannot access before initialization" errors because the module hasn't finished loading when it's accessed.

## Context / Trigger Conditions

- **Error Messages**:
  ```
  TypeError: Cannot read properties of undefined (reading 'someFunction')
  ```
  ```
  ReferenceError: Cannot access 'ClassName' before initialization
  ```
  ```
  TypeError: SomeClass is not a constructor
  ```
  ```
  Warning: Circular dependency detected (webpack/rollup/vite warning)
  ```

- **Symptoms**:
  - TypeScript compiles without errors
  - Runtime crashes with undefined or initialization errors
  - Errors happen inconsistently based on import order
  - Works sometimes, fails other times depending on which file loads first
  - Adding a new import suddenly breaks existing code

- **Environment**:
  - TypeScript projects
  - Any bundler (webpack, rollup, vite, esbuild)
  - Node.js ESM or CommonJS
  - Particularly common in large codebases with many cross-references

## Solution

### Step 1: Detect Circular Dependencies

Use tools to find circular import chains:

```bash
# Using madge (recommended)
npx madge --circular --extensions ts,tsx src/

# Using dpdm
npx dpdm --circular --tree false src/index.ts

# Webpack users - check build output for warnings
```

### Step 2: Identify the Cycle

Madge output shows the cycle:
```
Circular dependency detected:
  src/models/User.ts → src/models/Post.ts → src/models/User.ts
```

### Step 3: Break the Cycle

**Option A: Extract shared types to a separate file**

```typescript
// BEFORE: Circular
// models/User.ts
import { Post } from './Post'
export interface User { posts: Post[] }

// models/Post.ts
import { User } from './User'
export interface Post { author: User }

// AFTER: Extract to shared types
// models/types.ts (no imports from other model files)
export interface User { posts: Post[] }
export interface Post { author: User }

// models/User.ts
export { User } from './types'
export function createUser() { /* implementation */ }

// models/Post.ts
export { Post } from './types'
export function createPost() { /* implementation */ }
```

**Option B: Use type-only imports**

```typescript
// If you only need the type, not the runtime value
import type { User } from './User'  // This doesn't create runtime dependency
```

**Option C: Dependency injection**

```typescript
// BEFORE
// services/UserService.ts
import { EmailService } from './EmailService'
class UserService {
  private emailService = new EmailService()
}

// services/EmailService.ts
import { UserService } from './UserService'
class EmailService {
  private userService = new UserService()  // Circular!
}

// AFTER: Inject dependencies
// services/UserService.ts
class UserService {
  constructor(private emailService: EmailService) {}
}

// services/EmailService.ts
class EmailService {
  constructor(private userService: UserService) {}
}

// services/index.ts - wire up dependencies
const emailService = new EmailService(null as any)
const userService = new UserService(emailService)
emailService.userService = userService  // Set after construction
```

**Option D: Lazy imports (dynamic import)**

```typescript
// Delay the import until runtime when it's actually needed
async function getUser() {
  const { UserService } = await import('./UserService')
  return new UserService()
}
```

### Step 4: Add CI Check

Prevent future circular dependencies:

```json
// package.json
{
  "scripts": {
    "check:circular": "madge --circular --extensions ts,tsx src/",
    "lint": "eslint . && npm run check:circular"
  }
}
```

## Verification

1. [ ] Run `npx madge --circular src/` - should report no cycles
2. [ ] Build completes without circular dependency warnings
3. [ ] Runtime errors are resolved
4. [ ] All tests pass
5. [ ] Add circular dependency check to CI

## Example

### Scenario

Building a blog platform where User has Posts and Post has Author (User). Getting "Cannot access 'User' before initialization" at runtime.

### Before

```typescript
// models/User.ts
import { Post } from './Post'

export class User {
  id: string
  name: string
  posts: Post[]

  constructor(id: string, name: string) {
    this.id = id
    this.name = name
    this.posts = []
  }
}

// models/Post.ts
import { User } from './User'  // Creates cycle!

export class Post {
  id: string
  title: string
  author: User

  constructor(id: string, title: string, author: User) {
    this.id = id
    this.title = title
    this.author = author
  }
}
```

### After

```typescript
// models/types.ts - Shared interfaces (no circular imports)
export interface IUser {
  id: string
  name: string
  posts: IPost[]
}

export interface IPost {
  id: string
  title: string
  author: IUser
}

// models/User.ts
import type { IPost } from './types'

export class User implements IUser {
  id: string
  name: string
  posts: IPost[] = []

  constructor(id: string, name: string) {
    this.id = id
    this.name = name
  }
}

// models/Post.ts
import type { IUser } from './types'

export class Post implements IPost {
  id: string
  title: string
  author: IUser

  constructor(id: string, title: string, author: IUser) {
    this.id = id
    this.title = title
    this.author = author
  }
}
```

## Notes

- **Caveats**: `import type` only works if you don't need runtime access to the imported value
- **Related Skills**: TypeScript module resolution, barrel files best practices
- **Known Limitations**: Some patterns (like decorators that reference other classes) are harder to untangle
- **When NOT to Use**: If the bundler/runtime handles circular deps gracefully and you have no errors

## References

- [Madge - Module dependency analyzer](https://github.com/pahen/madge)
- [TypeScript import type](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-8.html#type-only-imports-and-export)
