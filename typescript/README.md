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

Template literal types string manipulation-এর জন্য TypeScript-এ আনা হয়েছে। এটি JavaScript-এর template literals-এর মতো, কিন্তু type-level-এ কাজ করে।

Real-world Analogy

এটা একটা URL router-এর মতো। তুমি pattern define করো (যেমন /users/:id/posts/:postId), এবং TypeScript সেই pattern-এর সাথে matching strings-এর types বুঝতে পারে।

Syntax Breakdown

```typescript
type Greeting = `Hello ${string}`;  // Any string starting with "Hello "
type Route = `/users/${string}/posts/${string}`;  // URL pattern
```

Multiple Practical Code Examples

Example 1: String Manipulation Utilities

```typescript
type UppercaseKeys<T> = {
  [K in keyof T as Uppercase<string & K>]: T[K];
};

type LowercaseKeys<T> = {
  [K in keyof T as Lowercase<string & K>]: T[K];
};

type CapitalizeKeys<T> = {
  [K in keyof T as Capitalize<string & K>]: T[K];
};

interface ApiEndpoints {
  getUser: string;
  createPost: string;
  updateSettings: string;
}

type UpperApiEndpoints = UppercaseKeys<ApiEndpoints>;
// {
//   GETUSER: string;
//   CREATEPOST: string;
//   UPDATESETTINGS: string;
// }
```

Example 2: Event System

```typescript
type EventMap = {
  user: {
    created: { userId: string; name: string };
    updated: { userId: string; changes: Partial<User> };
    deleted: { userId: string };
  };
  order: {
    created: { orderId: string; amount: number };
    completed: { orderId: string; status: string };
  };
};

type EventNames<T> = {
  [K in keyof T]: {
    [E in keyof T[K] as `${string & K}:${string & E}`]: T[K][E];
  }[keyof T[K]];
}[keyof T];

type AllEvents = EventNames<EventMap>;
// "user:created" | "user:updated" | "user:deleted" | "order:created" | "order:completed"

class TypedEventEmitter<T extends Record<string, Record<string, any>>> {
  private listeners: Map<string, Function[]> = new Map();
  
  on<E extends EventNames<T>>(
    event: E,
    handler: (data: E extends keyof T ? never : any) => void
  ) {
    // Implementation
  }
  
  emit<E extends EventNames<T>>(
    event: E,
    data: E extends keyof T ? never : any
  ) {
    // Implementation
  }
}
```

Example 3: CSS-in-JS Types

```typescript
type CssUnit = 'px' | 'rem' | 'em' | '%' | 'vh' | 'vw';
type CssValue<T extends number | string> = T extends number 
  ? `${T}${CssUnit}` 
  : T;

type Spacing = 'margin' | 'padding';
type Position = 'top' | 'right' | 'bottom' | 'left';
type SpacingProperty = `${Spacing}${Capitalize<Position>}`;
// "marginTop" | "marginRight" | "marginBottom" | "marginLeft" | 
// "paddingTop" | "paddingRight" | "paddingBottom" | "paddingLeft"

type CssProperties = {
  [K in SpacingProperty]?: CssValue<number>;
} & {
  color?: string;
  backgroundColor?: string;
  fontSize?: CssValue<number>;
};

const styles: CssProperties = {
  marginTop: '16px',  // ✅ Valid
  paddingLeft: '1rem',  // ✅ Valid
  fontSize: 14,  // ✅ Valid (infers as '14px' | '14rem' | ...)
  // marginTop: '10',  // ❌ Error: must include unit
};
```

Example 4: Type-Safe Query Builder

```typescript
type QueryOperator = 'eq' | 'ne' | 'gt' | 'lt' | 'gte' | 'lte' | 'like';
type QueryFilter<T> = {
  [P in keyof T as `${string & P}_${QueryOperator}`]?: T[P];
};

interface Product {
  id: number;
  name: string;
  price: number;
  category: string;
}

type ProductFilter = QueryFilter<Product>;
// {
//   id_eq?: number;
//   id_ne?: number;
//   id_gt?: number;
//   id_lt?: number;
//   name_eq?: string;
//   name_ne?: string;
//   name_like?: string;
//   price_eq?: number;
//   price_gt?: number;
//   // ... and so on
// }

function buildQuery<T>(filter: QueryFilter<T>): string {
  const conditions: string[] = [];
  
  for (const [key, value] of Object.entries(filter)) {
    const [field, operator] = key.split('_') as [keyof T, QueryOperator];
    conditions.push(`${String(field)} ${operator} ${JSON.stringify(value)}`);
  }
  
  return conditions.join(' AND ');
}

// Usage
const query = buildQuery<Product>({
  price_gt: 100,
  category_eq: 'electronics',
  name_like: 'phone'
});
// "price gt 100 AND category eq 'electronics' AND name like 'phone'"
```

Common Mistakes & Pitfalls

1. Overly complex template literals
   ```typescript
   // ❌ Too complex, hard to debug
   type Complex = `${infer A extends string}${infer B extends string}${infer C}`;
   
   // ✅ Break into smaller pieces
   type ParseFirst<T extends string> = T extends `${infer First}${infer Rest}` 
     ? First 
     : never;
   type ParseSecond<T extends string> = T extends `${infer _}${infer Second}${infer _}` 
     ? Second 
     : never;
   ```
2. Not handling union types properly
   ```typescript
   // ⚠️ Be careful with unions in template literals
   type AllowedMethods = 'GET' | 'POST' | 'PUT';
   type ApiVersion = 'v1' | 'v2';
   
   type Routes = `${AllowedMethods} /api/${ApiVersion}/${string}`;
   // This creates 3 * 2 = 6 combinations, which is manageable
   // But if you have 10 unions × 10 unions = 100 types
   ```

Real-world Use Case

Type-safe internationalization (i18n):

```typescript
type TranslationKeys = 
  | 'common.save'
  | 'common.cancel'
  | 'common.delete'
  | 'user.profile.title'
  | 'user.profile.email'
  | 'user.profile.phone'
  | 'errors.required'
  | 'errors.invalid_email'
  | 'errors.min_length';

type NestedTranslation<T extends string> = {
  [K in T as K extends `${infer First}.${infer Rest}` 
    ? First 
    : K]: K extends `${infer First}.${infer Rest}`
      ? NestedTranslation<Rest>
      : string;
};

type Translations = NestedTranslation<TranslationKeys>;
// {
//   common: {
//     save: string;
//     cancel: string;
//     delete: string;
//   };
//   user: {
//     profile: {
//       title: string;
//       email: string;
//       phone: string;
//     };
//   };
//   errors: {
//     required: string;
//     invalid_email: string;
//     min_length: string;
//   };
// }

class I18n {
  constructor(private translations: Translations) {}
  
  t<K extends TranslationKeys>(key: K): string {
    const parts = key.split('.');
    let result: any = this.translations;
    
    for (const part of parts) {
      result = result[part];
      if (!result) return key;
    }
    
    return result;
  }
}

const i18n = new I18n({
  common: { save: 'Save', cancel: 'Cancel', delete: 'Delete' },
  user: { profile: { title: 'Profile', email: 'Email', phone: 'Phone' } },
  errors: { required: 'Required field', invalid_email: 'Invalid email', min_length: 'Minimum length' }
});

i18n.t('common.save');  // ✅ Type-safe
i18n.t('user.profile.email');  // ✅ Type-safe
// i18n.t('invalid.key');  // ❌ Type error!
```

Mini Exercise

```typescript
// Create a type-safe CSS class name generator
// That combines base classes with modifiers

type ClassName = string;
type Modifier = string;

type WithModifiers<Base extends string, Mod extends string> = 
  `${Base}--${Mod}` | `${Base}`;

// Example: Button with modifiers
type ButtonBase = 'btn';
type ButtonModifiers = 'primary' | 'secondary' | 'large' | 'disabled';

type ButtonClasses = WithModifiers<ButtonBase, ButtonModifiers>;
// Should be: 'btn' | 'btn--primary' | 'btn--secondary' | 'btn--large' | 'btn--disabled'

function getButtonClass<T extends ButtonBase, U extends ButtonModifiers>(
  base: T,
  modifiers?: U[]
): WithModifiers<T, U>[] {
  const classes: WithModifiers<T, U>[] = [base];
  if (modifiers) {
    classes.push(...modifiers.map(m => `${base}--${m}` as WithModifiers<T, U>));
  }
  return classes;
}
```

---

7. Type Guards

Simple Explanation

Type guards হল runtime-এ checking করার mechanism যা TypeScript-কে বলে দেয় কোন variable-এর type কী। এটি type narrowing-এর জন্য ব্যবহার করা হয়।

Real-world Analogy

এটা airport security-এর মতো। Security guard চেক করে দেখে তুমি passenger নাকি crew নাকি visitor, এবং তারপর সেই অনুযায়ী তোমাকে নির্দিষ্ট এলাকায় যেতে দেয়।

Multiple Practical Code Examples

Example 1: typeof Type Guard

```typescript
function processValue(value: string | number | boolean) {
  if (typeof value === 'string') {
    // TypeScript knows value is string here
    return value.toUpperCase();
  }
  
  if (typeof value === 'number') {
    // TypeScript knows value is number here
    return value.toFixed(2);
  }
  
  // value must be boolean here
  return value ? 'yes' : 'no';
}
```

Example 2: instanceof Type Guard

```typescript
class User {
  constructor(public name: string, public email: string) {}
  
  login() {
    console.log(`${this.name} logged in`);
  }
}

class Admin extends User {
  deleteUser(userId: string) {
    console.log(`Deleted user ${userId}`);
  }
}

function handleEntity(entity: User | Admin) {
  if (entity instanceof Admin) {
    // TypeScript knows entity is Admin here
    entity.deleteUser('123');
  } else {
    // entity is User here
    entity.login();
  }
}
```

Example 3: Custom Type Guard with is

```typescript
interface Dog {
  type: 'dog';
  bark(): void;
  breed: string;
}

interface Cat {
  type: 'cat';
  meow(): void;
  livesLeft: number;
}

type Pet = Dog | Cat;

// Custom type guard
function isDog(pet: Pet): pet is Dog {
  return pet.type === 'dog';
}

function isCat(pet: Pet): pet is Cat {
  return pet.type === 'cat';
}

function handlePet(pet: Pet) {
  if (isDog(pet)) {
    pet.bark();  // TypeScript knows it's Dog
    console.log(pet.breed);
  } else {
    pet.meow();  // TypeScript knows it's Cat
    console.log(pet.livesLeft);
  }
}
```

Example 4: Complex Type Guards with Discriminated Unions

```typescript
type ApiEvent = 
  | { type: 'loading'; requestId: string }
  | { type: 'success'; data: unknown; requestId: string }
  | { type: 'error'; error: Error; requestId: string }
  | { type: 'progress'; loaded: number; total: number };

// Discriminated union type guard
function isLoadingEvent(event: ApiEvent): event is Extract<ApiEvent, { type: 'loading' }> {
  return event.type === 'loading';
}

function isSuccessEvent(event: ApiEvent): event is Extract<ApiEvent, { type: 'success' }> {
  return event.type === 'success';
}

function isErrorEvent(event: ApiEvent): event is Extract<ApiEvent, { type: 'error' }> {
  return event.type === 'error';
}

class ApiHandler {
  handleEvent(event: ApiEvent) {
    if (isLoadingEvent(event)) {
      this.showLoadingSpinner(event.requestId);
    } else if (isSuccessEvent(event)) {
      this.handleSuccess(event.data, event.requestId);
    } else if (isErrorEvent(event)) {
      this.handleError(event.error, event.requestId);
    } else {
      // event is progress
      this.updateProgress(event.loaded, event.total);
    }
  }
  
  private showLoadingSpinner(requestId: string) {}
  private handleSuccess(data: unknown, requestId: string) {}
  private handleError(error: Error, requestId: string) {}
  private updateProgress(loaded: number, total: number) {}
}
```

Example 5: Assertion Functions

```typescript
// Assertion function (throws if condition is false)
function assert(condition: any, message?: string): asserts condition {
  if (!condition) {
    throw new Error(message || 'Assertion failed');
  }
}

function assertIsString(value: any): asserts value is string {
  if (typeof value !== 'string') {
    throw new Error('Value must be a string');
  }
}

function processInput(input: unknown) {
  assertIsString(input);
  // TypeScript knows input is string here
  console.log(input.toUpperCase());
  
  assert(input.length > 0, 'String cannot be empty');
  // TypeScript still knows input is string, but now with additional runtime check
}
```

Common Mistakes & Pitfalls

1. Forgetting to return the type predicate
   ```typescript
   // ❌ Missing type predicate
   function isUser(obj: any): boolean {
     return obj && typeof obj.id === 'number';
   }
   
   // ✅ Correct with type predicate
   function isUser(obj: any): obj is User {
     return obj && typeof obj.id === 'number';
   }
   ```
2. Too broad type guards
   ```typescript
   // ❌ Too broad, accepts anything with id
   function isUser(obj: any): obj is User {
     return obj && 'id' in obj;  // Any object with id passes
   }
   
   // ✅ More specific
   function isUser(obj: any): obj is User {
     return obj 
       && typeof obj.id === 'number'
       && typeof obj.name === 'string'
       && typeof obj.email === 'string';
   }
   ```

Real-world Use Case

Form validation system:

```typescript
interface ValidationError {
  field: string;
  message: string;
}

type ValidationResult<T> = 
  | { success: true; data: T }
  | { success: false; errors: ValidationError[] };

function isSuccess<T>(result: ValidationResult<T>): result is { success: true; data: T } {
  return result.success === true;
}

function isError<T>(result: ValidationResult<T>): result is { success: false; errors: ValidationError[] } {
  return result.success === false;
}

class FormValidator<T extends Record<string, any>> {
  private validators: Map<keyof T, (value: any) => string | null> = new Map();
  
  registerValidator<K extends keyof T>(field: K, validator: (value: T[K]) => string | null) {
    this.validators.set(field, validator);
  }
  
  validate(data: Partial<T>): ValidationResult<T> {
    const errors: ValidationError[] = [];
    
    for (const [field, validator] of this.validators) {
      const value = data[field];
      const error = validator(value);
      
      if (error) {
        errors.push({ field: field as string, message: error });
      }
    }
    
    if (errors.length > 0) {
      return { success: false, errors };
    }
    
    return { success: true, data: data as T };
  }
}

// Usage
interface SignupForm {
  email: string;
  password: string;
  confirmPassword: string;
  age: number;
}

const validator = new FormValidator<SignupForm>();

validator.registerValidator('email', (email) => {
  if (!email) return 'Email is required';
  if (!email.includes('@')) return 'Invalid email format';
  return null;
});

validator.registerValidator('password', (password) => {
  if (!password) return 'Password is required';
  if (password.length < 8) return 'Password must be at least 8 characters';
  return null;
});

const result = validator.validate({
  email: 'invalid',
  password: '123'
});

if (isSuccess(result)) {
  // TypeScript knows result.data is SignupForm
  console.log('Form submitted:', result.data);
} else if (isError(result)) {
  // TypeScript knows result.errors is ValidationError[]
  result.errors.forEach(error => {
    console.log(`${error.field}: ${error.message}`);
  });
}
```

---

8. infer Keyword

Simple Explanation

infer keyword conditional types-এর মধ্যে ব্যবহার করে type-কে extract করা হয়। এটি JavaScript-এর destructuring-এর মতো, কিন্তু type-level-এ।

Real-world Analogy

এটা একটা document parser-এর মতো। তুমি একটা নির্দিষ্ট pattern খুঁজো (যেমন Promise<T>), এবং pattern-এর মধ্যে থাকা T-কে extract করো।

Multiple Practical Code Examples

Example 1: Extracting Return Type

```typescript
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

function getUser() {
  return { id: 1, name: 'John' };
}

type User = ReturnType<typeof getUser>;  // { id: number; name: string }

// Async functions
async function fetchUser() {
  return { id: 1, name: 'John' };
}

type AsyncUser = ReturnType<typeof fetchUser>;  // Promise<{ id: number; name: string }>
```

Example 2: Extracting Promise Type

```typescript
type Awaited<T> = T extends Promise<infer U> ? U : T;

type PromiseString = Awaited<Promise<string>>;  // string
type NestedPromise = Awaited<Promise<Promise<number>>>;  // Promise<number>

// Recursive version
type DeepAwaited<T> = T extends Promise<infer U> ? DeepAwaited<U> : T;

type DeepNested = DeepAwaited<Promise<Promise<Promise<string>>>>;  // string
```

Example 3: Function Parameter Types

```typescript
type Parameters<T> = T extends (...args: infer P) => any ? P : never;

type MyParams = Parameters<(name: string, age: number) => void>;  // [string, number]

// Extract first parameter
type FirstParameter<T> = T extends (first: infer F, ...args: any[]) => any ? F : never;

type First = FirstParameter<(name: string, age: number) => void>;  // string

// Extract constructor parameters
type ConstructorParameters<T> = T extends abstract new (...args: infer P) => any ? P : never;

class Person {
  constructor(public name: string, public age: number) {}
}

type PersonParams = ConstructorParameters<typeof Person>;  // [string, number]
```

Example 4: Extracting Array Element Type

```typescript
type ArrayElement<T> = T extends (infer U)[] ? U : never;

type StringArrayElement = ArrayElement<string[]>;  // string
type MixedArrayElement = ArrayElement<(string | number)[]>;  // string | number

// For tuples
type TupleElement<T> = T extends [infer First, ...infer Rest] 
  ? { first: First; rest: Rest }
  : never;

type MyTuple = [string, number, boolean];
type Extracted = TupleElement<MyTuple>;  // { first: string; rest: [number, boolean] }
```

Example 5: Complex Type Extraction

```typescript
// Extract function type from object method
type MethodType<T, K extends keyof T> = T[K] extends (...args: infer P) => infer R
  ? (...args: P) => R
  : never;

class Service {
  getUser(id: number): { id: number; name: string } {
    return { id, name: 'John' };
  }
  
  saveUser(user: { name: string }): Promise<void> {
    return Promise.resolve();
  }
}

type GetUserMethod = MethodType<Service, 'getUser'>;  // (id: number) => { id: number; name: string }
type SaveUserMethod = MethodType<Service, 'saveUser'>;  // (user: { name: string }) => Promise<void>

// Extract nested type
type NestedType<T> = T extends { data: infer D } 
  ? D extends { items: infer I }
    ? I[]
    : never
  : never;

interface ApiResponse {
  data: {
    items: {
      id: number;
      name: string;
    };
    total: number;
  };
}

type Items = NestedType<ApiResponse>;  // { id: number; name: string }[]
```

Common Mistakes & Pitfalls

1. Using infer outside conditional types
   ```typescript
   // ❌ Can't use infer directly
   type Wrong = infer T;  // Error
   
   // ✅ Must be inside conditional type
   type Correct<T> = T extends infer U ? U : never;
   ```
2. Not handling multiple infer possibilities
   ```typescript
   // ❌ Only works with single function overload
   type OverloadedReturn<T> = T extends (...args: any[]) => infer R ? R : never;
   
   // ✅ Better to use built-in ReturnType which handles overloads
   // Or create union of all possibilities
   ```

Real-world Use Case

Dependency Injection container:

```typescript
// Extract dependencies from constructor
type ConstructorDeps<T> = T extends new (...args: infer D) => any ? D : never;

class Logger {
  log(message: string) {
    console.log(message);
  }
}

class Database {
  constructor(private url: string) {}
  
  query(sql: string) {
    // implementation
  }
}

class UserService {
  constructor(
    private logger: Logger,
    private db: Database
  ) {}
  
  async getUser(id: number) {
    this.logger.log(`Fetching user ${id}`);
    return this.db.query(`SELECT * FROM users WHERE id = ${id}`);
  }
}

class Container {
  private instances = new Map<any, any>();
  
  register<T>(token: new (...args: any[]) => T, ...deps: any[]) {
    const depsInstances = deps.map(dep => this.get(dep));
    const instance = new token(...depsInstances);
    this.instances.set(token, instance);
    return instance;
  }
  
  get<T>(token: new (...args: any[]) => T): T {
    if (!this.instances.has(token)) {
      // Auto-resolve dependencies
      const deps = this.getConstructorDeps(token);
      const depsInstances = deps.map(dep => this.get(dep));
      const instance = new token(...depsInstances);
      this.instances.set(token, instance);
    }
    return this.instances.get(token);
  }
  
  private getConstructorDeps<T>(ctor: new (...args: any[]) => T): any[] {
    // In real implementation, you'd use reflection/metadata
    return [];
  }
}

// Type-safe DI
const container = new Container();
const userService = container.get(UserService);
// TypeScript knows userService is UserService
```

---

9. satisfies Operator

Simple Explanation

satisfies operator TypeScript 4.9-এ আসা একটি feature যা type safety নিশ্চিত করে কিন্তু type widening করে না। এটি বলে "এই expression এই type-কে satisfies করে" কিন্তু exact type retain করে।

Real-world Analogy

এটা job interview-এর মতো। তুমি বলো "আমি software engineer position-এর requirements satisfy করি" (সব skills আছি), কিন্তু তুমি exact যা তুমি (সব specific skills) সেটাও জানতে চাও। satisfies বলে যে value একটি type-কে satisfy করে, কিন্তু তার own type সংরক্ষণ করে।

Multiple Practical Code Examples

Example 1: Preserving Exact Types

```typescript
// Without satisfies
const colors1 = {
  primary: 'red',
  secondary: 'blue',
  error: 'red'
} as const;  // Loses flexibility, everything becomes readonly

// With satisfies
const colors2 = {
  primary: 'red',
  secondary: 'blue',
  error: 'red'
} satisfies Record<string, string>;

// TypeScript knows:
// colors2.primary is 'red' (not just string)
// colors2.secondary is 'blue'
// colors2.error is 'red'
```

Example 2: Validating Object Structure

```typescript
type Route = {
  path: string;
  component: React.ComponentType;
  children?: Route[];
  requiresAuth?: boolean;
};

const routes = {
  home: {
    path: '/',
    component: HomePage,
    requiresAuth: false,
  },
  dashboard: {
    path: '/dashboard',
    component: DashboardPage,
    requiresAuth: true,
    children: {
      profile: {
        path: '/profile',
        component: ProfilePage,
        requiresAuth: true,
      }
    }
  }
} satisfies Record<string, Route>;

// TypeScript validates all routes have required fields
// But still knows exact types:
routes.home.path;  // Type: '/'
routes.dashboard.children.profile.path;  // Type: '/profile'
```

Example 3: URL Configuration

```typescript
type ApiEndpoint = {
  url: string;
  method: 'GET' | 'POST' | 'PUT' | 'DELETE';
  headers?: Record<string, string>;
};

const endpoints = {
  users: {
    url: '/api/users',
    method: 'GET',
  },
  createUser: {
    url: '/api/users',
    method: 'POST',
  },
  userDetails: (id: number) => ({
    url: `/api/users/${id}`,
    method: 'GET',
  }),
  updateUser: (id: number) => ({
    url: `/api/users/${id}`,
    method: 'PUT',
  }),
} satisfies Record<string, ApiEndpoint | ((...args: any[]) => ApiEndpoint)>;

// Type-safe usage
const usersEndpoint = endpoints.users;  // Type: ApiEndpoint
const userDetailsEndpoint = endpoints.userDetails(123);  // Type: ApiEndpoint

// TypeScript catches errors
// endpoints.wrong = { url: '/test' };  // ❌ Error: missing method
```

Example 4: Configuration Objects

```typescript
type DatabaseConfig = {
  host: string;
  port: number;
  username: string;
  password: string;
  database: string;
  ssl?: boolean;
  pool?: {
    min: number;
    max: number;
    idleTimeoutMillis: number;
  };
};

type CacheConfig = {
  host: string;
  port: number;
  password?: string;
  ttl: number;
};

type AppConfig = {
  database: DatabaseConfig;
  cache: CacheConfig;
  environment: 'development' | 'production' | 'test';
  logLevel: 'debug' | 'info' | 'warn' | 'error';
  features: {
    [K: string]: boolean;
  };
};

const config = {
  database: {
    host: process.env.DB_HOST,
    port: parseInt(process.env.DB_PORT || '5432'),
    username: process.env.DB_USER,
    password: process.env.DB_PASS,
    database: process.env.DB_NAME,
    ssl: process.env.NODE_ENV === 'production',
    pool: {
      min: 2,
      max: 10,
      idleTimeoutMillis: 30000,
    },
  },
  cache: {
    host: process.env.REDIS_HOST,
    port: parseInt(process.env.REDIS_PORT || '6379'),
    password: process.env.REDIS_PASS,
    ttl: 3600,
  },
  environment: process.env.NODE_ENV,
  logLevel: process.env.LOG_LEVEL,
  features: {
    analytics: true,
    notifications: process.env.ENABLE_NOTIFICATIONS === 'true',
    experimental: false,
  },
} satisfies AppConfig;

// TypeScript validates structure matches AppConfig
// But still knows exact values
if (config.environment === 'production') {
  // config.database.ssl is true
  console.log('SSL enabled:', config.database.ssl);
}

// config.features.analytics is boolean
console.log('Analytics enabled:', config.features.analytics);
```

Common Mistakes & Pitfalls

1. Using satisfies for type narrowing
   ```typescript
   // ❌ Not necessary
   const value = { x: 1, y: 2 } satisfies { x: number; y: number };
   
   // ✅ Just use type annotation if you don't need exact type
   const value: { x: number; y: number } = { x: 1, y: 2 };
   ```
2. Overusing satisfies
   ```typescript
   // ❌ Unnecessary satisfies
   const name = "John" satisfies string;  // Just "John" is fine
   
   // ✅ Use when you need validation + exact type
   const colors = {
     primary: "#ff0000",
     secondary: "#00ff00",
   } satisfies Record<string, `#${string}`>;
   ```

Real-world Use Case

Environment variables configuration:

```typescript
// Define expected env vars
type EnvVar = {
  value: string | undefined;
  required: boolean;
  parser?: (value: string) => any;
};

const envSchema = {
  PORT: {
    value: process.env.PORT,
    required: true,
    parser: (v: string) => parseInt(v, 10),
  },
  DATABASE_URL: {
    value: process.env.DATABASE_URL,
    required: true,
  },
  REDIS_URL: {
    value: process.env.REDIS_URL,
    required: false,
  },
  NODE_ENV: {
    value: process.env.NODE_ENV,
    required: true,
    parser: (v: string) => v as 'development' | 'production' | 'test',
  },
  LOG_LEVEL: {
    value: process.env.LOG_LEVEL,
    required: false,
    parser: (v: string) => v as 'debug' | 'info' | 'warn' | 'error',
  },
} satisfies Record<string, EnvVar>;

// Type-safe config extraction
type ParsedConfig = {
  [K in keyof typeof envSchema]: typeof envSchema[K] extends { parser: infer P }
    ? P extends (value: string) => infer R
      ? R
      : string | undefined
    : string | undefined;
};

const config = {} as ParsedConfig;

for (const [key, schema] of Object.entries(envSchema)) {
  if (schema.required && !schema.value) {
    throw new Error(`Missing required environment variable: ${key}`);
  }
  
  if (schema.value && schema.parser) {
    config[key as keyof ParsedConfig] = schema.parser(schema.value);
  } else if (schema.value) {
    config[key as keyof ParsedConfig] = schema.value;
  }
}

// Usage with full type safety
const port = config.PORT;  // number | undefined
const nodeEnv = config.NODE_ENV;  // 'development' | 'production' | 'test' | undefined
const logLevel = config.LOG_LEVEL;  // 'debug' | 'info' | 'warn' | 'error' | undefined
```

---

Final Project: Production-Grade API Framework

এখন আমরা সব কনসেপ্ট একসাথে ব্যবহার করে একটা production-ready API framework বানাবো:

```typescript
// types/index.ts
// Using conditional types, mapped types, template literals
export type HttpMethod = 'GET' | 'POST' | 'PUT' | 'DELETE' | 'PATCH';

export type RouteHandler<TReq = any, TRes = any> = (
  req: TReq,
  ctx: Context
) => Promise<TRes> | TRes;

export type RouteConfig<T extends string = string> = {
  path: T;
  method: HttpMethod;
  handler: RouteHandler;
  middleware?: Middleware[];
  validation?: ValidationSchema;
};

// Using template literal types for URL patterns
export type ExtractRouteParams<T extends string> = 
  T extends `${infer _Start}:${infer Param}/${infer Rest}`
    ? { [K in Param | keyof ExtractRouteParams<Rest>]: string }
    : T extends `${infer _Start}:${infer Param}`
      ? { [K in Param]: string }
      : {};

// Using conditional types
export type InferHandlerResponse<T> = T extends RouteHandler<any, infer R> ? R : never;

// Using mapped types for middleware chain
export type Middleware<T = any> = {
  (ctx: Context, next: () => Promise<void>): Promise<void>;
  __type?: T;
};

export type ValidationSchema = {
  body?: Record<string, any>;
  query?: Record<string, any>;
  params?: Record<string, any>;
};

// Using satisfies for context
export const createContext = (req: Request, res: Response) => ({
  req,
  res,
  state: {} as Record<string, any>,
  get<T>(key: string): T | undefined {
    return this.state[key] as T;
  },
  set(key: string, value: any): void {
    this.state[key] = value;
  },
}) satisfies Context;

// Using type guards
export function isValidationError(error: any): error is ValidationError {
  return error && typeof error === 'object' && 'field' in error && 'message' in error;
}

// Using infer in the router
export class Router<T extends Record<string, RouteConfig>> {
  private routes: Map<string, RouteConfig> = new Map();
  
  constructor(private routesConfig: T) {
    this.registerRoutes();
  }
  
  private registerRoutes() {
    for (const [name, config] of Object.entries(this.routesConfig)) {
      const key = `${config.method}:${config.path}`;
      this.routes.set(key, config);
    }
  }
  
  async handle(req: Request, res: Response): Promise<void> {
    const method = req.method as HttpMethod;
    const url = req.url || '';
    
    // Find matching route
    for (const [key, route] of this.routes) {
      const [routeMethod, routePath] = key.split(':');
      
      if (routeMethod !== method) continue;
      
      // Parse params from URL
      const params = this.parseParams(routePath, url);
      if (!params) continue;
      
      // Create context
      const ctx = createContext(req, res);
      ctx.set('params', params);
      
      try {
        // Run middleware chain
        await this.runMiddleware(route.middleware || [], ctx);
        
        // Validate request
        if (route.validation) {
          await this.validateRequest(req, route.validation, params);
        }
        
        // Execute handler
        const result = await route.handler(req, ctx);
        
        // Send response
        res.json(result);
      } catch (error) {
        this.handleError(error, res);
      }
      
      return;
    }
    
    res.status(404).json({ error: 'Not found' });
  }
  
  private parseParams(routePath: string, actualUrl: string): Record<string, string> | null {
    const routeParts = routePath.split('/');
    const urlParts = actualUrl.split('/');
    
    if (routeParts.length !== urlParts.length) return null;
    
    const params: Record<string, string> = {};
    
    for (let i = 0; i < routeParts.length; i++) {
      if (routeParts[i].startsWith(':')) {
        const paramName = routeParts[i].slice(1);
        params[paramName] = urlParts[i];
      } else if (routeParts[i] !== urlParts[i]) {
        return null;
      }
    }
    
    return params;
  }
  
  private async runMiddleware(middleware: Middleware[], ctx: Context): Promise<void> {
    let index = -1;
    
    const next = async (): Promise<void> => {
      index++;
      if (index < middleware.length) {
        await middleware[index](ctx, next);
      }
    };
    
    await next();
  }
  
  private async validateRequest(req: Request, schema: ValidationSchema, params: Record<string, string>): Promise<void> {
    // Validation logic
    if (schema.params) {
      // Validate params
    }
    if (schema.body) {
      // Validate body
    }
    if (schema.query) {
      // Validate query
    }
  }
  
  private handleError(error: any, res: Response): void {
    if (isValidationError(error)) {
      res.status(400).json({ error: error.message, field: error.field });
    } else {
      res.status(500).json({ error: 'Internal server error' });
    }
  }
}

// Usage
const app = new Router({
  getUser: {
    path: '/users/:id',
    method: 'GET',
    handler: async (req, ctx) => {
      const params = ctx.get<{ id: string }>('params');
      const userId = params?.id;
      
      return {
        id: userId,
        name: 'John Doe',
        email: 'john@example.com',
      };
    },
    validation: {
      params: {
        id: { type: 'string', required: true, pattern: /^\d+$/ }
      }
    }
  },
  createUser: {
    path: '/users',
    method: 'POST',
    handler: async (req, ctx) => {
      const body = req.body;
      // Create user logic
      return { id: Date.now(), ...body };
    },
    validation: {
      body: {
        name: { type: 'string', required: true, minLength: 2 },
        email: { type: 'string', required: true, pattern: /^[\w-]+@([\w-]+\.)+[\w-]+$/ },
        age: { type: 'number', required: false, min: 18, max: 120 }
      }
    },
    middleware: [
      async (ctx, next) => {
        console.log('Request started');
        const start = Date.now();
        await next();
        console.log(`Request completed in ${Date.now() - start}ms`);
      },
      async (ctx, next) => {
        // Authentication middleware
        const token = ctx.req.headers.authorization;
        if (!token) {
          throw new Error('Unauthorized');
        }
        await next();
      }
    ]
  }
} satisfies Record<string, RouteConfig>);
```

Interview Perspective

Interview-এ এই কনসেপ্টগুলো আসলে কীভাবে আসে:

1. Conditional Types: "How would you implement a type that extracts the return type of a function?"
2. Mapped Types: "Create a type that makes all properties of an object readonly recursively"
3. Template Literal Types: "Design a type-safe event system using template literals"
4. Type Guards: "How do you narrow union types in TypeScript?"
5. infer: "Explain how ReturnType utility type works internally"
6. satisfies: "What's the difference between as const and satisfies?"

Next Steps

1. Practice each concept with the mini exercises
2. Build the final project and extend it with features
3. Read TypeScript's source code for utility types
4. Contribute to open-source TypeScript projects
5. Try converting a JavaScript project to TypeScript using these patterns

প্রশ্ন থাকলে জানাবেন! Happy coding! 🚀