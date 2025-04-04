<div align='center'>
  <h1>TypeScript</h1>
</div>

# TypeScript

TypeScript is a superset of JavaScript, i.e., it includes all JavaScript features and extends its functionalities by providing static type annotations while enforcing type checking.
 
- **Pros:** static typing can flag errors at compile time before runtime. 
- **Cons:** TypeScript features can make the code more verbose. Code must be transpiled into JavaScript to run in the browser.

## Features

TypeScript introduced: 

- `Type annotations:` are used to provide hints about the data type of a variable, parameter, function, and other constructs.
- `Access modifiers:` can be **public**, **private**, **protected**, or **read-only**. Are used to define the visibility and accessibility of classes, methods, and member variables.
- `Interfaces:` are used to define the structure of an object, i.e., the data type of the properties of an object. In VsCode, TypeScript interfaces are implemented to enable code hints.

# Examples

To run `.ts` files with Node.js, one should install the TypeScript compiler:
```bash
npm install -g typescript
```

Since JavaScript is dynamically typed, when using a JavaScript runtime environment (e.g. node.js), trying to reassign a variable that has already been declared does not render a compile-time error.

```javascript
// node message.js
let message = 'message';
message = 10;
```

In TypeScript, it is possible to reassign a variable that has already been assigned, but the new value must match the type of the variable, otherwise it will render a compile-time error:

```typescript
// tsc message.ts
let message: string = 'message';
message = 10; // ERROR: Type 'number' is not assignable to type 'string'.
```

It is also possible to write JavaScript code and still leverage TypeScript functionality. To achieve this, use `.ts` file extension and run the code through a TypeScript compiler, which will infer the data type of the variable based on the value assigned to it.

```javascript
// tsc message.ts
let message = 'message';
message = 10; // ERROR: Type 'number' is not assignable to type 'string'.
```

# Summary

TypeScript can be gradually integrated into a JavaScript workflow as the project becomes more complex. Make sure to change file extensions to .ts and use a TypeScript compiler.

- JavaScript is recommended for small-scale applications that require fast prototyping. 
- TypeScript is recommended for code maintainability of complex large-scale enterprise applications (such as VsCode) with many potential contributors.

