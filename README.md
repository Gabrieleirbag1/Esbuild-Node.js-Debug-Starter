# Esbuild Node.js Debug Starter

## Setup

1. Install dependencies:

```sh
npm install
```

## Build

Build the TypeScript code using esbuild:

```sh
npm run build
```

This will:
- Compile the index.ts file
- Bundle it into index.js
- Generate source maps for debugging

## Run

You can run the compiled code with:

```sh
node lib/index.js
```

This will start a counter that increments every second and outputs to the console.

## Debug

To debug the application:

1. Open the project in VS Code
2. Set breakpoints in your index.ts file
3. Press F5 or use the Run and Debug panel to start debugging with the "Launch Script" configuration
4. The debugger will automatically stop at the `debugger` statement in the code

## Project Structure

- index.ts - Main TypeScript source code
- index.js - Compiled JavaScript output
- esbuild.js - Build configuration
- launch.json - Debug configuration