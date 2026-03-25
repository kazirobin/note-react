চমৎকার! আসুন TypeScript-এর গভীরে ডুব দিই। আমি একজন সিনিয়র ইঞ্জিনিয়ার হিসেবে প্র্যাকটিক্যাল দৃষ্টিভঙ্গি থেকে শেখাবো।

---

Part 1: TypeScript Compiler Deep Dive

1. tsconfig.json - The Heart of TypeScript Project

Simple Explanation

tsconfig.json হল TypeScript compiler-এর কনফিগারেশন ফাইল। এটি compiler-কে বলে দেয় কিভাবে আপনার code-কে compile করতে হবে, কোন ফাইলগুলো include করতে হবে, এবং কতটা strict হবে।

Real-world Analogy

এটা একটা রেস্টুরেন্টের kitchen-এর SOP (Standard Operating Procedure) এর মতো। যেখানে নির্ধারণ করা থাকে:

· কোন উপকরণ ব্যবহার করা যাবে (strictness)
· কিভাবে রান্না করতে হবে (module resolution)
· কাদের জন্য পরিবেশন করা যাবে (target environment)

Syntax Breakdown

```json
{
  "compilerOptions": {
    // Core
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["ES2020", "DOM"],
    
    // Strictness Flags
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    
    // Module Resolution
    "moduleResolution": "node",
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    },
    
    // Output
    "outDir": "./dist",
    "rootDir": "./src",
    
    // Declaration Files
    "declaration": true,
    "declarationDir": "./types",
    
    // Advanced
    "isolatedModules": true,
    "esModuleInterop": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

Key Flags Deep Dive

1. strict: true

· এটি একটি meta-flag যা নিচের সব flags একসাথে enable করে:
  · noImplicitAny: কোনো variable-এর type inferred না হলে error দেবে
  · strictNullChecks: null এবং undefined আলাদাভাবে handle করতে বাধ্য করে
  · strictFunctionTypes: function parameter-এ stricter checking
  · strictBindCallApply: bind, call, apply-এ type checking
  · strictPropertyInitialization: class properties অবশ্যই initialized হতে হবে

2. noImplicitAny

```typescript
// ❌ Bad - implicit any
function processData(data) {  // Error: Parameter 'data' implicitly has an 'any' type
  return data.value;
}

// ✅ Good - explicit type
function processData(data: { value: string }) {
  return data.value;
}
```

3. moduleResolution

· node: Node.js-style resolution (node_modules folder অনুসন্ধান করে)
· classic: পুরনো TypeScript resolution (rarely used)
· TypeScript কীভাবে import statements resolve করবে তা নির্ধারণ করে

Common Mistakes & Pitfalls

1. Not using strict: true
   ```typescript
   // ❌ Without strict: true
   const user = getUser();  // user: any (dangerous!)
   
   // ✅ With strict: true
   const user = getUser();  // Type error if getUser() doesn't have proper return type
   ```
2. Wrong target for production
   ```json
   // ❌ Too old
   { "target": "ES5" }  // Loses modern features, bloated output
   
   // ✅ Balance between compatibility and features
   { "target": "ES2020" }  // Modern, but check your deployment environment
   ```
3. Missing rootDir and outDir
   ```json
   // ❌ Results in messy output structure
   {
     "outDir": "./dist"
     // rootDir not specified
   }
   
   // ✅ Clean output structure
   {
     "rootDir": "./src",
     "outDir": "./dist"
   }
   ```

Real-world Use Case

একটি production-grade Express API project-এর tsconfig.json:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["ES2020"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "moduleResolution": "node",
    "allowSyntheticDefaultImports": true,
    "emitDecoratorMetadata": true,
    "experimentalDecorators": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"],
      "@models/*": ["./src/models/*"],
      "@utils/*": ["./src/utils/*"]
    }
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist", "**/*.test.ts"]
}
```

---

2. Declaration Files (.d.ts)

Simple Explanation

.d.ts ফাইল শুধুমাত্র type information ধারণ করে, কোনো executable code নয়। এটি JavaScript libraries-কে TypeScript-এ ব্যবহার করার bridge হিসেবে কাজ করে।

Real-world Analogy

এটা একটা ফোনবুকের মতো। তুমি জানো কার নামে কী নম্বর (types), কিন্তু ফোনবুকে সেই ব্যক্তির পুরো জীবনকথা (implementation) থাকে না।

Syntax Breakdown

```typescript
// math.d.ts
export declare function add(a: number, b: number): number;
export declare const PI: number;

export interface Calculator {
  add(a: number, b: number): number;
  subtract(a: number, b: number): number;
}

// Global declarations
declare global {
  interface Window {
    myCustomGlobal: string;
  }
}
```

Multiple Practical Code Examples

Example 1: Wrapping a JavaScript Library

```typescript
// lodash-magic.js (plain JavaScript)
export function magicTransform(arr) {
  return arr.map(x => x * 2).filter(x => x > 10);
}

// lodash-magic.d.ts
export declare function magicTransform(arr: number[]): number[];
export declare function magicTransform<T>(arr: T[], transform: (item: T) => T): T[];
```

Example 2: Augmenting Existing Types

```typescript
// express.d.ts
import { Request } from 'express';

declare module 'express' {
  interface Request {
    user?: {
      id: string;
      email: string;
      role: 'admin' | 'user';
    };
    startTime?: number;
  }
}
```

Example 3: Global Types

```typescript
// global.d.ts
declare namespace NodeJS {
  interface ProcessEnv {
    DATABASE_URL: string;
    REDIS_URL: string;
    JWT_SECRET: string;
    NODE_ENV: 'development' | 'production' | 'test';
  }
}

// Now process.env.DATABASE_URL is typed!
```

Common Mistakes & Pitfalls

1. Using declare when you don't need to
   ```typescript
   // ❌ Redundant
   declare interface User {  // declare not needed for interfaces
     id: number;
   }
   
   // ✅ Correct
   interface User {
     id: number;
   }
   ```
2. Not handling exports properly
   ```typescript
   // ❌ Missing export
   interface Config {  // This won't be accessible from other files
     apiUrl: string;
   }
   
   // ✅ Export properly
   export interface Config {
     apiUrl: string;
   }
   ```

Real-world Use Case

একটি API client library-এর জন্য declaration files:

```typescript
// api-client.d.ts
export type HttpMethod = 'GET' | 'POST' | 'PUT' | 'DELETE';

export interface ApiResponse<T = any> {
  data: T;
  status: number;
  message: string;
  timestamp: string;
}

export interface ApiError {
  code: string;
  message: string;
  details?: Record<string, any>;
}

export declare class ApiClient {
  constructor(baseUrl: string, options?: ClientOptions);
  
  request<T = any>(
    endpoint: string, 
    method: HttpMethod, 
    body?: any
  ): Promise<ApiResponse<T>>;
  
  get<T = any>(endpoint: string): Promise<ApiResponse<T>>;
  post<T = any>(endpoint: string, body: any): Promise<ApiResponse<T>>;
  
  setAuthToken(token: string): void;
  clearAuthToken(): void;
}

export interface ClientOptions {
  timeout?: number;
  retries?: number;
  headers?: Record<string, string>;
}
```

---

3. isolatedModules

Simple Explanation

isolatedModules flag compiler-কে বলে যে প্রতিটি ফাইল আলাদাভাবে compile হবে (যেমন Babel বা esbuild ব্যবহার করলে)। এটি কিছু TypeScript features disable করে দেয় যেগুলো single-file compilation-এ কাজ করে না।

Real-world Analogy

এটা একটা assembly line-এর মতো। যখন প্রতিটি worker আলাদাভাবে কাজ করে (isolated modules), তখন তাদের এমনভাবে কাজ করতে হবে যাতে অন্য workers-এর সাথে synchronization না লাগে। TypeScript-এর কিছু features এই isolated environment-এ কাজ করে না।

Why is this important?

Modern build tools (Babel, esbuild, swc) TypeScript code compile করে per-file basis-এ। কিন্তু TypeScript compiler-এর কিছু features জানতে হয় পুরো project সম্পর্কে। isolatedModules সেই features গুলো disable করে দেয়।

What gets disabled?

```typescript
// ❌ 1. Const enums (requires knowledge of other files)
const enum Colors {  // Error: 'const' enums cannot be used with isolatedModules
  Red,
  Green,
  Blue
}

// ✅ Use regular enums instead
enum Colors {
  Red,
  Green,
  Blue
}

// ❌ 2. Namespaces (global namespace merging)
namespace MyLib {  // Error: Cannot use namespace with isolatedModules
  export function helper() {}
}

// ✅ Use ES modules instead
export function helper() {}

// ❌ 3. Export assignments
declare module "my-module" {
  function something(): void;
  export = something;  // Error: Cannot use export = with isolatedModules
}

// ✅ Use export default instead
declare module "my-module" {
  function something(): void;
  export default something;
}
```

Common Mistakes & Pitfalls

1. Using const enum in libraries
   ```typescript
   // ❌ Library with const enum
   export const enum Status {  // Consumers using isolatedModules will fail
     Active,
     Inactive
   }
   
   // ✅ Library should use regular enum or union types
   export enum Status {
     Active,
     Inactive
   }
   // OR
   export type Status = 'active' | 'inactive';
   ```
2. Assuming global types are available
   ```typescript
   // ❌ Relies on global namespace merging
   // file1.ts
   namespace App {
     export interface User { id: string; }
   }
   
   // file2.ts (with isolatedModules)
   const user: App.User = { id: '1' };  // Error: Cannot find namespace 'App'
   
   // ✅ Import/export properly
   // types.ts
   export interface User { id: string; }
   
   // file2.ts
   import type { User } from './types';
   const user: User = { id: '1' };
   ```

Real-world Use Case

Next.js বা Vite project-এ isolatedModules ব্যবহার করা হয়:

```json
// tsconfig.json for Next.js project
{
  "compilerOptions": {
    "isolatedModules": true,  // Required by Next.js
    "jsx": "preserve",
    "target": "ES2020",
    "module": "ESNext",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true
  }
}
```

---

Part 2: Advanced TypeScript

4. Conditional Types

Simple Explanation

Conditional types মানে হল "যদি এই condition true হয়, তাহলে এই type, নাহলে ঐ type"। এটি JavaScript-এর ternary operator-এর মতো, কিন্তু type-level-এ কাজ করে।

Real-world Analogy

এটা একটা smart thermostat-এর মতো। যদি temperature 20°C-এর নিচে হয়, তাহলে heating চালু করো; যদি বেশি হয়, cooling চালু করো। শর্তের উপর ভিত্তি করে output change হয়।

Syntax Breakdown

```typescript
T extends U ? X : Y
// যদি T কে U-তে assign করা যায়, তাহলে type হবে X, নাহলে Y
```

Multiple Practical Code Examples

Example 1: Basic Conditional Type

```typescript
type IsString<T> = T extends string ? true : false;

type A = IsString<string>;  // true
type B = IsString<number>;  // false
type C = IsString<"hello">; // true (string literal extends string)
```

Example 2: Function Return Type Based on Input

```typescript
type ApiResponse<T> = T extends { data: infer U }
  ? { success: true; data: U }
  : { success: false; error: string };

function processApi<T>(response: T): ApiResponse<T> {
  if ('data' in response) {
    return { success: true, data: response.data } as any;
  }
  return { success: false, error: 'Invalid response' } as any;
}

// Usage
const validResponse = processApi({ data: { id: 1, name: "John" } });
// Type: { success: true; data: { id: number; name: string } }

const invalidResponse = processApi({ error: "Not found" });
// Type: { success: false; error: string }
```

Example 3: Filtering Types

```typescript
type FilterStrings<T> = T extends string ? T : never;

type Mixed = string | number | boolean | "hello";
type OnlyStrings = FilterStrings<Mixed>;  // string | "hello"

// Real-world: Extract function types
type FunctionProperties<T> = {
  [K in keyof T]: T[K] extends (...args: any[]) => any ? K : never;
}[keyof T];

interface User {
  id: number;
  name: string;
  getName(): string;
  setName(name: string): void;
}

type MethodNames = FunctionProperties<User>;  // "getName" | "setName"
```

Example 4: Recursive Conditional Types

```typescript
type DeepReadonly<T> = {
  readonly [P in keyof T]: T[P] extends object 
    ? T[P] extends Function 
      ? T[P] 
      : DeepReadonly<T[P]>
    : T[P];
};

interface Config {
  database: {
    host: string;
    port: number;
    credentials: {
      username: string;
      password: string;
    };
  };
  cache: {
    ttl: number;
  };
}

type ReadonlyConfig = DeepReadonly<Config>;
// All properties become readonly recursively
```

Common Mistakes & Pitfalls

1. Not handling never type
   ```typescript
   // ❌ Can lead to never being propagated
   type ExtractId<T> = T extends { id: infer Id } ? Id : never;
   
   // ✅ Better: Provide fallback
   type ExtractId<T, Fallback = unknown> = T extends { id: infer Id } 
     ? Id 
     : Fallback;
   ```
2. Overly complex conditional types
   ```typescript
   // ❌ Hard to read and maintain
   type Complex<T> = T extends string 
     ? T extends `${infer _}` 
       ? T extends `user-${infer Rest}` 
         ? { type: 'user'; id: Rest }
         : T extends `admin-${infer Rest}`
           ? { type: 'admin'; id: Rest }
           : never
       : never
     : never;
   
   // ✅ Break down into smaller types
   type ParseUserPrefix<T> = T extends `user-${infer Rest}` 
     ? { type: 'user'; id: Rest }
     : never;
     
   type ParseAdminPrefix<T> = T extends `admin-${infer Rest}`
     ? { type: 'admin'; id: Rest }
     : never;
     
   type ParsePrefix<T extends string> = 
     ParseUserPrefix<T> | ParseAdminPrefix<T>;
   ```

Real-world Use Case

API response handler:

```typescript
type ApiEndpoint<T> = T extends '/users'
  ? { data: User[]; total: number }
  : T extends `/users/${infer Id}`
    ? { data: User; cache: boolean }
    : T extends '/posts'
      ? { data: Post[]; nextPage?: string }
      : never;

async function fetchApi<T extends string>(
  endpoint: T
): Promise<ApiEndpoint<T>> {
  const response = await fetch(`/api${endpoint}`);
  return response.json();
}

// Usage with full type safety
const users = await fetchApi('/users');  // Type: { data: User[]; total: number }
const user = await fetchApi('/users/123');  // Type: { data: User; cache: boolean }
const posts = await fetchApi('/posts');  // Type: { data: Post[]; nextPage?: string }
```

Mini Exercise

```typescript
// Create a conditional type that extracts the promise resolved type
// Or returns the type itself if it's not a promise
type UnwrapPromise<T> = T extends Promise<infer U> ? U : T;

// Test cases:
type Test1 = UnwrapPromise<Promise<string>>;  // Should be string
type Test2 = UnwrapPromise<number>;           // Should be number
type Test3 = UnwrapPromise<Promise<Promise<boolean>>>;  // Should be Promise<boolean>
```

---

5. Mapped Types

Simple Explanation

Mapped types একটি existing type-এর properties-এর উপর iteration করে নতুন type তৈরি করে। এটি JavaScript-এর map() function-এর মতো, কিন্তু type-level-এ।

Real-world Analogy

এটা একটা manufacturing plant-এর মতো। একই raw material (existing type) নিয়ে বিভিন্ন process (transformation) করে বিভিন্ন product (new types) বানানো যায়।

Syntax Breakdown

```typescript
type MappedType<T> = {
  [P in keyof T]: Transform<T[P]>;
};
// P হল property name, T[P] হল property value
```

Multiple Practical Code Examples

Example 1: Readonly and Optional

```typescript
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};

type Partial<T> = {
  [P in keyof T]?: T[P];
};

type Required<T> = {
  [P in keyof T]-?: T[P];  // -? removes optional
};

type Mutable<T> = {
  -readonly [P in keyof T]: T[P];  // -readonly removes readonly
};

interface User {
  readonly id: number;
  name?: string;
  email: string;
}

type MutableUser = Mutable<User>;  // id becomes mutable
type RequiredUser = Required<User>;  // name becomes required
```

Example 2: Property Transformation

```typescript
type Getters<T> = {
  [P in keyof T & string as `get${Capitalize<P>}`]: () => T[P];
};

interface User {
  id: number;
  name: string;
  email: string;
}

type UserGetters = Getters<User>;
// {
//   getId: () => number;
//   getName: () => string;
//   getEmail: () => string;
// }

// Real-world: Creating a builder pattern
type Builder<T> = {
  [P in keyof T]: (value: T[P]) => Builder<T>;
} & {
  build(): T;
};

function createBuilder<T>(): Builder<T> {
  const state = {} as T;
  const builder = {} as Builder<T>;
  
  for (const key in {} as T) {
    builder[key as keyof T] = (value: any) => {
      state[key as keyof T] = value;
      return builder;
    };
  }
  
  builder.build = () => state;
  return builder;
}

const userBuilder = createBuilder<User>();
const user = userBuilder
  .id(123)
  .name("John")
  .email("john@example.com")
  .build();
```

Example 3: Filtering Properties

```typescript
// Pick specific types
type PickByType<T, U> = {
  [P in keyof T as T[P] extends U ? P : never]: T[P];
};

interface Entity {
  id: number;
  name: string;
  createdAt: Date;
  updatedAt: Date;
  isActive: boolean;
}

type StringProperties = PickByType<Entity, string>;  // { name: string }
type DateProperties = PickByType<Entity, Date>;  // { createdAt: Date; updatedAt: Date }
type BooleanProperties = PickByType<Entity, boolean>;  // { isActive: boolean }

// Exclude specific types
type ExcludeType<T, U> = {
  [P in keyof T as T[P] extends U ? never : P]: T[P];
};

type NonDateProperties = ExcludeType<Entity, Date>;  // { id: number; name: string; isActive: boolean }
```

Example 4: Key Remapping (TypeScript 4.1+)

```typescript
// Add prefix/suffix to property names
type WithPrefix<T, Prefix extends string> = {
  [P in keyof T & string as `${Prefix}${Capitalize<P>}`]: T[P];
};

interface Config {
  host: string;
  port: number;
  timeout: number;
}

type WithEnvPrefix = WithPrefix<Config, 'env'>;
// {
//   envHost: string;
//   envPort: number;
//   envTimeout: number;
// }

// Remove specific keys
type Without<T, K extends keyof T> = {
  [P in keyof T as P extends K ? never : P]: T[P];
};

type UserWithoutId = Without<User, 'id'>;  // { name: string; email: string }
```

Common Mistakes & Pitfalls

1. Not handling symbol properties
   ```typescript
   // ❌ Can miss symbol keys
   type Mapped<T> = {
     [P in keyof T]: T[P];
   };
   
   // ✅ Better: Explicitly include symbols if needed
   type Mapped<T> = {
     [P in keyof T as P extends string | number | symbol ? P : never]: T[P];
   };
   ```
2. Losing property modifiers
   ```typescript
   // ❌ Loses readonly and optional modifiers
   type NaiveCopy<T> = {
     [P in keyof T]: T[P];
   };
   
   // ✅ Preserve modifiers
   type BetterCopy<T> = {
     readonly [P in keyof T]?: T[P];
   };
   // But this makes everything readonly optional - need to map carefully
   ```

Real-world Use Case

ORM (Object-Relational Mapping) system:

```typescript
// Automatically generate CRUD operations from model
type CrudOperations<T, IdType = number> = {
  // Create
  create(data: Omit<T, 'id' | 'createdAt' | 'updatedAt'>): Promise<T>;
  
  // Read
  findById(id: IdType): Promise<T | null>;
  findAll(filter?: Partial<T>): Promise<T[]>;
  
  // Update
  update(id: IdType, data: Partial<Omit<T, 'id' | 'createdAt' | 'updatedAt'>>): Promise<T>;
  
  // Delete
  delete(id: IdType): Promise<boolean>;
  
  // Batch operations
  bulkCreate(data: Array<Omit<T, 'id' | 'createdAt' | 'updatedAt'>>): Promise<T[]>;
  bulkUpdate(updates: Array<{ id: IdType; data: Partial<T> }>): Promise<T[]>;
};

interface UserModel {
  id: number;
  name: string;
  email: string;
  age: number;
  createdAt: Date;
  updatedAt: Date;
}

type UserCrud = CrudOperations<UserModel>;
// Automatically typed CRUD operations for User
```

Mini Exercise

```typescript
// Create a mapped type that makes all properties nullable
// And another that makes them all non-nullable

type Nullable<T> = {
  [P in keyof T]: T[P] | null;
};

type NonNullable<T> = {
  [P in keyof T]: NonNullable<T[P]>;
};

// Test with:
interface Product {
  id: number;
  name: string;
  price: number;
  description?: string;
}

type NullableProduct = Nullable<Product>;
type NonNullableProduct = NonNullable<Product>;
```

---

6. Template Literal Types

Simple Explanation

Template literal types string manipulation-এর জন্য TypeScript-এ আনা হয়েছে। এটি JavaScript-এর template literals-এর মতো, কিন্তু type-level-এ কা