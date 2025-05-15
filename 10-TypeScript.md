<style>
  h1 { font-size: 3em; }
  h2 { font-size: 2.5em; }
  h3 { font-size: 2em; }
  h4 { font-size: 1.5em; }
  h5 { font-size: 1.3em; }
  h6 { font-size: 1.2em; }
</style>

# Introduction

## What is TypeScript?

TypeScript is a superset of JavaScript that adds static typing to the language. It was developed by Microsoft and is designed to help developers write more maintainable and scalable code.

## Why use TypeScript?

TypeScript offers several benefits over plain JavaScript, including:

- **Static Typing**: TypeScript allows you to define the types of variables, function parameters, and return values. This helps catch errors at compile time rather than runtime.
- **Better Tooling**: TypeScript integrates well with modern development tools like editors, linters, and build systems.
- **Enhanced IDE Support**: TypeScript provides better autocompletion and error highlighting in your IDE.
- **Type Safety**: TypeScript enforces type safety, reducing the likelihood of runtime errors.
- **Progressive Adoption**: TypeScript can be used alongside JavaScript, allowing you to incrementally adopt it in your projects.

## How to get started with TypeScript?

To get started with TypeScript, you can follow these steps:

1. Install TypeScript globally using npm:
   ```bash
   npm install -g typescript
   ```

2. Create a new TypeScript file with a `.ts` extension:
   ```bash
   touch index.ts
   ```

3. Write your TypeScript code in the file:
   ```typescript
   function greet(name: string): string {
     return `Hello, ${name}!`;
   }

   console.log(greet('World'));
   ```

4. Compile your TypeScript code to JavaScript using the TypeScript compiler (`tsc`):
   ```bash
   tsc index.ts
   ```

5. Run the compiled JavaScript file:
   ```bash
   node index.js
   ```

## TypeScript Configuration

To configure TypeScript, you can create a `tsconfig.json` file in your project root. Here's an example configuration:

```json
{
  "compilerOptions": {
    "target": "es6",
    "module": "commonjs",
    "strict": true,
    "esModuleInterop": true
  },
  "include": ["src/**/*"]
}
```

**If you create a typescript project using create-next-app, cna automates all this for you.**


## React Component Definitions

Here are the various ways of defining React functional components:

| Pattern                           | Example                                                               | Pros                     |
|-----------------------------------|-----------------------------------------------------------------------|--------------------------|
| No arguments                      | export function MyComponent() {}                                      | Simple, but no props     |
| Single props object               | export function MyComponent(props) {}                                 | Works, but less readable |
| Destructured props                | export function MyComponent({ name }) {}                              | Cleaner, more readable   |
| Destructured + TypeScript utility | export function MyComponent({ ... }: React.ComponentProps<'form'>) {} | Type-safe, scalable      |

As you can see in the 4th pattern, we destructure and use TypeScript to define the props type.

Let's understand this pattern with an example.

### Example 1 :

```js
export function ChatForm({ className, ...props }: React.ComponentProps<"form">) {
  // component logic here
}
```
We already read about prop-destructuring [here](./4-React.md#v-destructuring-props-in-function-parameters). Infact, destructuring props in the function's parameter list, (rather than using props.propname within the function body) is the modern practice. In the above example, `className` is destructured out as a variable and `...props` destructures the rest of the properties into an object called `props`.

The next concept is assigning a type to the props. In order to understand the above Example 1, let's understand type assignment first.


## Assigning Types to Props within the Component's Parameter List

We didn't need this in JS. But in Typescript, we explicitly tell the compiler that a component expects props of a certain type. 

We saw [here](4-React.md#11-passing-props-to-react-components) that React components can take 9 different `types` of props. Let's learn to assign TS `types` those `destructured` props using the `colon :` operator :

### 1. **String** (Text)
```tsx
type Props = {
  playerName: string; // Simple text
};

function PlayerTag({ playerName }: Props) {
  return <h1>{playerName}</h1>;
}
// Usage: <PlayerTag playerName="NinjaSlayer99" />
```

### 2. **Number** 
```tsx
type Props = {
  score: number;
  lives: number;
};

function GameStats({ score, lives }: Props) {
  return <div>Score: {score} | Lives: {lives}</div>;
}
// Usage: <GameStats score={420} lives={3} />
```

### 3. **Boolean** (True/False)
```tsx
type Props = {
  isOnline: boolean;
};

function StatusLight({ isOnline }: Props) {
  return <div>{isOnline ? '🟢 Online' : '🔴 Offline'}</div>;
}
// Usage: <StatusLight isOnline={true} />
```

### 4. **Variable** (Using existing values)
```tsx
type Props = {
  difficulty: 'easy' | 'medium' | 'hard'; // Specific options
};

function LevelSelect({ difficulty }: Props) {
  return <button>Start {difficulty} level</button>;
}
// Usage: <LevelSelect difficulty="hard" />
```

### 5. **Object** (Key-value pairs)
```tsx
type Player = {
  id: number;
  username: string;
  level: number;
};

type Props = {
  character: Player; // Custom object type
};

function CharacterCard({ character }: Props) {
  return (
    <div>
      <h2>{character.username}</h2>
      <p>Level {character.level}</p>
    </div>
  );
}
// Usage: <CharacterCard character={{ id: 1, username: "DarkMage", level: 99 }} />
```

### 6. **Array** (List of items)
```tsx
type Props = {
  inventory: string[]; // Array of strings
  powerUps: Array<{ name: string; value: number }>; // Complex array
};

function Inventory({ inventory, powerUps }: Props) {
  return (
    <div>
      <h3>Items: {inventory.join(', ')}</h3>
      <h3>Powerups: {powerUps.length}</h3>
    </div>
  );
}
// Usage: 
// <Inventory 
//   inventory={["Sword", "Potion", "Key"]} 
//   powerUps={[{name: "Speed Boost", value: 2}]} 
// />
```

### 7. **Function** (Callback)
```tsx
type Props = {
  onAttack: () => void; // No parameters, returns nothing
  onHeal: (amount: number) => boolean; // Takes number, returns boolean
};

function Controls({ onAttack, onHeal }: Props) {
  return (
    <div>
      <button onClick={onAttack}>⚔️ Attack</button>
      <button onClick={() => onHeal(10)}>❤️ Heal</button>
    </div>
  );
}
// Usage: 
// <Controls 
//   onAttack={() => console.log("Punch!")} 
//   onHeal={(amt) => { console.log(`Healed ${amt}`); return true; }} 
// />
```

### 8. **Children** (Nested content)
```tsx
import { ReactNode } from 'react';

type Props = {
  children: ReactNode; // Most flexible type for children
};

function Tooltip({ children }: Props) {
  return <div className="tooltip">{children}</div>;
}
// Usage: 
// <Tooltip>
//   <span>Hover over me!</span>
//   <img src="icon.png" alt="Hint" />
// </Tooltip>
```

Note that when you need to pass other props (of some type say CardProps) in addition to children we can use this type helper syntax in the Parameter List: 

```tsx
{ ...CardProps, children?: ReactNode }
```
Or even better
```tsx
(props: PropsWithChildren<CardProps>)
```

### 9. **ComponentType** (Passing entire components)
```tsx
type Props = {
  Icon: React.ComponentType<{ size?: number }>; // Component that takes size prop
};

function ActionButton({ Icon }: Props) {
  return (
    <button>
      <Icon size={24} /> Click me!
    </button>
  );
}
// Usage: 
// const HeartIcon = () => <span>❤️</span>;
// <ActionButton Icon={HeartIcon} />
```

### 10. **ComponentProps** (Reusing Props)

Just like `ComponentType` above defines the type of the entire propped-component, `ComponentProps` defines the type of the individual props of the propped-component. You can use it in 2 ways:

- **A. TO DEFINE TYPE OF THE PROPS WITHIN `COMPONENTTYPE` LIKE THIS**

```tsx
type Props = {
  Avatar: React.ComponentType<React.ComponentProps<'img'> & { rounded?: boolean }>;
};

function UserProfile({ Avatar }: Props) {
  return (
    <div className="profile">
      <Avatar 
        src="https://example.com/avatar.jpg" 
        alt="User avatar"
        rounded={true}
      />
      <h2>John Doe</h2>
    </div>
  );
}

// Usage:
// const MyAvatar = (props: React.ComponentProps<'img'> & { rounded?: boolean }) => (
//   <img 
//     {...props} 
//     style={{ borderRadius: props.rounded ? '50%' : '4px' }} 
//   />
// );
// <UserProfile Avatar={MyAvatar} />
```

In this example:
1. The `Avatar` prop is a component that accepts all the props of an HTML `img` element (via `ComponentProps<'img'>`) plus an additional `rounded` boolean prop
2. The `UserProfile` component passes both standard img props (`src`, `alt`) and the custom `rounded` prop to the `Avatar` component

or

- **B. TO DEFINE TYPE OF PROPS WITHIN THE PARAMETER LIST**

In this case, there's no propped-component. The props resemble the attributes of some standard html element like form, button, etc. for eg. onClick, onSubmit, etc. Here's an example:

```tsx
export function ChatForm({ className, ...props }: React.ComponentProps<"form">) {....}
```

Here `className` prop is undefined (possibly a string). All the remaining props are of those type which an html `<form>` element accepts, like `onSubmit`, `action`, etc. 

Check out this [short video](https://www.youtube.com/watch?v=Rf_k8kSAFU0).

### TypeScript Helpers Cheat Sheet:
| Prop Type       | TypeScript Type                          | Helper Needed?          |
|-----------------|------------------------------------------|-------------------------|
| String          | `string`                                 | No                      |
| Number          | `number`                                 | No                      |
| Boolean         | `boolean`                                | No                      |
| Variable        | Union types (`'a' | 'b' | 'c'`)          | No                      |
| Object          | Custom type/interface                    | Define your own         |
| Array           | `ItemType[]` or `Array<ItemType>`        | No                      |
| Function        | `(params) => returnType`                 | No                      |
| Children        | `ReactNode` (from 'react')               | `import { ReactNode }`  |
| Component       | `React.ComponentType<Props>`             | `import { ComponentType }` |
| Component Props     | `React.ComponentProps<html-element>`             | `import { ComponentProps }` |
---

Pro Tip: For components you'll reuse, put these types in a `types.ts` file next to your component!

After learning assigning Types to Props within the Component's Parameter List, the [Example 1](#example-1) should be clear now.





