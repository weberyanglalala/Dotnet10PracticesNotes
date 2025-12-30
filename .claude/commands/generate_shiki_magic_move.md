# Generate Shiki Magic Move

Generate Shiki Magic Move code blocks for Slidev presentations. Shiki Magic Move enables granular transitions between code changes, similar to Keynote's Magic Move.

## Instructions

When the user requests a Shiki Magic Move block:

1. **Ask the user for**:
   - The programming language (e.g., js, ts, vue, py, etc.)
   - The number of steps/transitions they want to show
   - The code transformations or refactoring steps they want to demonstrate
   - Whether they want line numbers (`lines: true`)
   - Whether they want line highlighting

2. **Generate the code block using this structure**:

```markdown
````md magic-move {lines: true}
```<language> {highlighting-config}
// Step 1 code
```

```<language> {highlighting-config}
// Step 2 code
```

```<language> {highlighting-config}
// Step 3 code
```
````
```

## Syntax Reference

### Basic Structure

Use **four backticks** to wrap multiple code blocks:

````markdown
````md magic-move
```js
console.log(`Step ${1}`)
```
```js
console.log(`Step ${1 + 1}`)
```
```ts
console.log(`Step ${3}` as string)
```
````
````

### Configuration Options

- **Enable line numbers**: Add `{lines: true}` after `magic-move`
  ```markdown
  ````md magic-move {lines: true}
  ```

- **Set starting position**: Use `{at: N}` to control when animation starts
  ```markdown
  ````md magic-move {at: 4, lines: true}
  ```

### Line Highlighting

Each code block can have its own highlighting configuration:

- `{*}` - Highlight all lines
- `{1}` - Highlight line 1
- `{2-5}` - Highlight lines 2 through 5
- `{*|1|2-5}` - Multiple steps: all lines, then line 1, then lines 2-5
- `{lines: false}` - Disable line numbers for specific block

**Example:**
````markdown
````md magic-move {lines: true}
```js {*|1|2-5}
let count = 1
function add() {
  count++
}
```

```js {*}{lines: false}
let count = 1
const add = () => count += 1
```
````
````

## Example Templates

### Template 1: Vue Reactivity Evolution

````markdown
````md magic-move {lines: true}
```ts {*|2|*}
// step 1
const author = reactive({
  name: 'John Doe',
  books: [
    'Vue 2 - Advanced Guide',
    'Vue 3 - Basic Guide',
    'Vue 4 - The Mystery'
  ]
})
```

```ts {*|1-2|3-4|3-4,8}
// step 2
export default {
  data() {
    return {
      author: {
        name: 'John Doe',
        books: [
          'Vue 2 - Advanced Guide',
          'Vue 3 - Basic Guide',
          'Vue 4 - The Mystery'
        ]
      }
    }
  }
}
```

```ts
// step 3
export default {
  data: () => ({
    author: {
      name: 'John Doe',
      books: [
        'Vue 2 - Advanced Guide',
        'Vue 3 - Basic Guide',
        'Vue 4 - The Mystery'
      ]
    }
  })
}
```

```vue
<!-- step 4 -->
<script setup>
const author = {
  name: 'John Doe',
  books: [
    'Vue 2 - Advanced Guide',
    'Vue 3 - Basic Guide',
    'Vue 4 - The Mystery'
  ]
}
</script>
```
````
````

### Template 2: Refactoring Example

````markdown
````md magic-move {lines: true}
```js {*|3-5}
// Before: Imperative
function processData(data) {
  const results = []
  for (let i = 0; i < data.length; i++) {
    results.push(data[i] * 2)
  }
  return results
}
```

```js {*|3}
// After: Functional
function processData(data) {
  return data.map(item => item * 2)
}
```
````
````

### Template 3: TypeScript Type Evolution

````markdown
````md magic-move {lines: true}
```ts
// Step 1: Basic type
type User = {
  name: string
  age: number
}
```

```ts {*|4}
// Step 2: Add optional field
type User = {
  name: string
  age: number
  email?: string
}
```

```ts {*|5-7}
// Step 3: Add union type
type User = {
  name: string
  age: number
  email?: string
  role: 'admin' | 'user' | 'guest'
}
```
````
````

## Important Notes

1. **Four backticks**: Must use ```````` (4 backticks), not ``` (3 backticks)
2. **Language consistency**: Can mix languages (js → ts → vue)
3. **Non-code blocks ignored**: Text between code blocks won't be displayed
4. **Click-based transitions**: Each code block represents one click/step
5. **Smooth morphing**: Shiki Magic Move animates line changes smoothly

## Common Use Cases

- Demonstrating refactoring steps
- Showing code evolution (Vue 2 → Vue 3, JS → TS)
- Illustrating different approaches to solving a problem
- Teaching progressive enhancement
- Showing before/after comparisons

## Reference

- Documentation: https://sli.dev/features/shiki-magic-move.html
- Shiki Magic Move Playground: https://shiki-magic-move.netlify.app/
- Available since Slidev v0.48.0
