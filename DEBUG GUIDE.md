# Debugging TypeScript in Node.js Projects

This guide will help you set up and run a debug session for your TypeScript files using Visual Studio Code.

## Project Structure

A typical TypeScript Node.js project structure:

- **index.ts**: Main entry point that contains your application logic
- **config.json**: Contains configuration settings for your application
- **tsconfig.json**: TypeScript configuration specifying module settings and compilation options
- **launch.json**: Debug configuration for VS Code

## Prerequisites

- Ensure dependencies are installed by running:

  ```sh
  npm install
  ```

  _(or `pnpm install` or `yarn` depending on your package manager)_

- Make sure you have VS Code installed.

## Build Process

The project uses [esbuild](https://esbuild.github.io/) to compile and bundle the TypeScript source code. When you build the project, the following happens:

1. **Transpile & Bundle**  
   The build script compiles index.ts and outputs the bundled file into the dist directory.
2. **Static Files Copy**  
   A custom plugin in the build script copies static assets (files that are not TypeScript) from src to dist.

To build the project, run:

```sh
npm run build
```

## Key File Explanations

### index.ts

This file serves as the entry point for your application. It might look something like:

```typescript
class Application {
  constructor() {
    this.initialize();
  }

  public async initialize(): Promise<void> {
    console.log("Initializing application");
    // Your application initialization code
  }
}

const app = new Application();
```

### tsconfig.json

TypeScript configuration file that determines how your TypeScript code is compiled and what features are available.

```json
{
  "compilerOptions": {
    "module": "ESNext", // Use modern ECMAScript modules
    "target": "es2019", // Target ES2019 JavaScript features
    "moduleResolution": "node", // Use Node.js-style module resolution
    "sourceMap": true, // Generate source maps for debugging
    "resolveJsonModule": true, // Enable importing JSON files
    "esModuleInterop": true // Better interoperability with CommonJS modules
  }
}
```

### esbuild.js

A high-performance JavaScript bundler configuration that compiles your TypeScript code into JavaScript that can be executed in Node.js.

```js
await esbuild.build({
  entryPoints: ["src/index.ts"], // Entry file for your application
  bundle: true, // Bundle all dependencies into one file
  platform: "node", // Target Node.js runtime
  sourcemap: true, // Generate source maps for debugging
  sourcesContent: true, // Embed original source content in source maps
  format: "esm", // Output as ECMAScript module
  outfile: "lib/index.js", // Output file location
  plugins: [copyStaticPlugin], // Custom plugin to copy static assets
});
```

### launch.json

Debug configuration file that tells VS Code how to launch and debug your application, including how to map compiled code back to source code.

```json
{
  "version": "0.2.0", // The version of the debug configuration format
  "configurations": [
    {
      "type": "node", // Specifies that we're debugging a Node.js application
      "request": "launch", // Launches a new instance of the program (as opposed to "attach" to existing)
      "name": "Launch CommonJS Script", // The name that appears in the debug dropdown menu
      "program": "${workspaceFolder}/dist/index.cjs", // The path to the executable file to debug
      "preLaunchTask": "npm: build", // Task to run before launching the debugger (builds the project)
      "outFiles": [
        "${workspaceFolder}/dist/**/*.cjs", // Location of generated JavaScript files
        "${workspaceFolder}/node_modules/**/*.js" // Include node_modules for library debugging
      ],
      "sourceMaps": true, // Enable source maps for debugging TypeScript directly
      "stopOnEntry": false, // Don't pause execution at the start of the program
      "resolveSourceMapLocations": [
        "${workspaceFolder}/**", // Look for source maps in the project directory
        "${workspaceFolder}/node_modules/**" // Also look in node_modules for library source maps
      ],
      "skipFiles": ["<node_internals>/**"] // Don't step into Node.js internal code when debugging
    }
  ]
}
```

> **Annotation:**  
> - This configuration launches Node.js to debug the compiled CommonJS script (`index.cjs`) in the `dist` folder.  
> - The `preLaunchTask` ensures the project is built before debugging starts.  
> - `outFiles` specifies where VS Code should look for generated JavaScript and source maps.  
> - `sourceMaps` enables mapping back to the original TypeScript files for easier debugging.  
> - `resolveSourceMapLocations` helps VS Code find source maps in both your project and dependencies.  
> - `skipFiles` excludes Node.js internals from the debugger for a cleaner experience.

## Debug Configuration

The debug configuration in launch.json is set up to:

- Launch Node.js to run the compiled JavaScript in index.js
- Automatically build the project before debugging starts via the `preLaunchTask`
- Use source maps to map the compiled JavaScript back to the original TypeScript
- Configure source map resolution to work correctly with the project structure

## Starting a Debug Session

1. Open your project in Visual Studio Code.
2. Set breakpoints in your TypeScript files by clicking in the gutter next to line numbers.
3. Open the Debug panel (Ctrl+Shift+D or click the Debug icon in the Activity Bar).
4. Select the **Launch Program** configuration.
5. Click the green play button, or press F5 to start debugging.

## Summary

- **Install dependencies:** `npm install`
- **Build:** `npm run build`
- **Debug:** Use the **Launch Program** configuration in the Debug