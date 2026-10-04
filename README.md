# Node-Js-Fundamentals
I'll analyze the repository more deeply to create a comprehensive step-by-step learning guide. Let me gather the content of key files to understand the progression and concepts.Now I'll create a comprehensive step-by-step learning guide based on all the files in this repository.

---

# 📚 Node.js Step-by-Step Learning Guide

Based on **gopinav/Nodejs-Tutorials**, this guide organizes all tutorials from beginner to advanced concepts. Follow this progression to master Node.js fundamentals.

---

## **Phase 1: JavaScript Fundamentals (Prerequisites)**

### 1.1 **Basic Variables & Console Logging**
```bash
node batman.js
```
```javascript
// batman.js - Start here!
const superHero = "Batman";
console.log(superHero);
```
**Learn:** Basic variable declaration and console output.

---

### 1.2 **Immediately Invoked Function Expressions (IIFE)**
```bash
node iife.js
```
```javascript
// iife.js
(function (message) {
  const superHero = "Batman";
  console.log(message, superHero);
})("Hello");

(function (message) {
  const superHero = "Superman";
  console.log(message, superHero);
})("Hey");
```
**Learn:** IIFE pattern for creating isolated scopes. Useful for avoiding variable conflicts.

---

## **Phase 2: Node.js Module System**

### 2.1 **CommonJS: Exporting & Importing Modules**

#### Step A: Simple Function Export
```bash
node add.js
```
```javascript
// add.js - export a single function
const add = (a, b) => {
  return a + b;
};
module.exports = add;
```
**Learn:** Export a single function using `module.exports`.

#### Step B: Multiple Exports (Named Exports)
```javascript
// math.js - export multiple functions
const add = (a, b) => {
  return a + b;
};

const subtract = (a, b) => {
  return a - b;
};

module.exports = {
  add,
  subtract,
};
```
**Learn:** Export multiple functions as an object.

#### Step C: Class Export
```javascript
// super-hero.js - export a class
class SuperHero {
  constructor(name) {
    this.name = name;
  }
  getName() {
    return this.name;
  }
  setName(name) {
    this.name = name;
  }
}

module.exports = SuperHero;
```
**Learn:** Export classes for instantiation in other files.

---

### 2.2 **ES Modules (ESM) - Modern Syntax**

#### Step A: Define ES Module
```bash
# Create math-esm.mjs
```
```javascript
// math-esm.mjs - ES module syntax with .mjs extension
const add = (a, b) => {
  return a + b;
};

const subtract = (a, b) => {
  return a - b;
};

export default {
  add,
  subtract,
};
```

#### Step B: Import ES Module
```bash
node main.mjs
```
```javascript
// main.mjs - import using ES syntax
import math from "./math-esm.mjs";

console.log(math.add(16, 26));      // Output: 42
console.log(math.subtract(16, 26)); // Output: -10
```
**Learn:** Modern `import/export` syntax. Use `.mjs` extension for ES modules.

---

## **Phase 3: Built-in Node.js Modules**

### 3.1 **Path Module**
```bash
node built-in-path.js
```
```javascript
// built-in-path.js
const path = require("node:path");

// Get file/directory name
console.log(path.basename(__filename));    // current file name
console.log(path.basename(__dirname));     // parent directory name

// Get file extension
console.log(path.extname(__filename));     // .js
console.log(path.extname(__dirname));      // (empty)

// Parse path
console.log(path.parse(__filename));
// Output: { root: '/', dir: '...', base: 'file.js', ext: '.js', name: 'file' }

// Format path from parts
console.log(path.format(path.parse(__filename)));

// Check if absolute path
console.log(path.isAbsolute(__filename));           // true
console.log(path.isAbsolute("./data.json"));        // false

// Join paths (relative)
console.log(path.join("folder1", "folder2", "index.html"));
// Output: folder1/folder2/index.html

// Resolve paths (absolute)
console.log(path.resolve(__dirname, "data.json"));
// Output: /full/absolute/path/data.json
```
**Learn:** Working with file paths, joining, resolving, and parsing.

---

### 3.2 **File System (FS) Module - Synchronous & Asynchronous**

#### Step A: Synchronous vs Asynchronous
```bash
node built-in-fs.js
```
```javascript
// built-in-fs.js
const fs = require("node:fs");

console.log("First");

// Synchronous read - BLOCKS execution
const fileContents = fs.readFileSync("./file.txt", "utf8");
console.log(fileContents);

console.log("Second");

// Asynchronous read - NON-BLOCKING
fs.readFile("./file.txt", "utf-8", (err, data) => {
  if (err) {
    console.log(err);
  } else {
    console.log(data);
  }
});

console.log("Third");

// Synchronous write
fs.writeFileSync("./greet.txt", "Hello World");

// Asynchronous write
fs.writeFile(
  "./greet.txt",
  " Hello Vishwas",
  {
    flag: "a",  // append mode
  },
  (err) => {
    if (err) {
      console.log(err);
    } else {
      console.log("File written");
    }
  }
);
```
**Output Order:** First → Second → Third → file contents → "File written"

**Learn:** 
- Synchronous blocks execution
- Asynchronous is non-blocking (callbacks execute later)
- Always prefer async for production apps

---

#### Step B: Promise-based FS (Modern Approach)
```bash
node built-in-fs-promises.js
```
```javascript
// built-in-fs-promises.js
const fs = require("node:fs/promises");

console.log("First");

async function readFile() {
  try {
    const data = await fs.readFile("file.txt", "utf8");
    console.log(data);
  } catch (err) {
    console.log(err);
  }
}

readFile();

// Alternative: Promise .then() syntax
// fs.readFile("file.txt", "utf8")
//   .then((data) => console.log(data))
//   .catch((err) => console.log(err));

console.log("Second");
```
**Output Order:** First → Second → file contents

**Learn:** `async/await` is cleaner than callbacks. `fs/promises` is the modern standard.

---

### 3.3 **Buffers**
```bash
node buffer.js
```
```javascript
// buffer.js - Working with binary data
const buffer = new Buffer.from("Vishwas", "utf-8");
buffer.write("Codevolution");  // Overwrites the buffer

console.log(buffer);           // Raw buffer data
console.log(buffer.toString()); // Convert to string
console.log(buffer.toJSON());   // Convert to JSON
```
**Learn:** Buffers handle binary data. Always convert to readable formats.

---

### 3.4 **Streams**
```bash
node streams.js
```
```javascript
// streams.js - Efficient large file handling
const fs = require("fs");
const zlib = require("zlib");

// Create readable stream (small chunks)
const readableStream = fs.createReadStream("./file.txt", {
  encoding: "utf8",
  highWaterMark: 2,  // 2 bytes per chunk (demo purposes)
});

// Create writable stream
const writeableStream = fs.createWriteStream("./file2.txt");

// Pipe readable → writable
readableStream.pipe(writeableStream);

// Alternative: Manual event handling
// readableStream.on("data", (chunk) => {
//   console.log(chunk);
//   writeableStream.write(chunk);
// });

// Compress while piping
const gzip = zlib.createGzip();
readableStream.pipe(gzip).pipe(fs.createWriteStream("./file2.txt.gz"));

// Listen to events
readableStream.on("end", () => {
  console.log("Done reading");
});

readableStream.on("error", (err) => {
  console.log(err);
});
```
**Learn:** Streams handle large files efficiently. Pipe chains operations elegantly.

---

## **Phase 4: Events & Event Emitters**

### 4.1 **Built-in EventEmitter**
```bash
node built-in-events.js
```
```javascript
// built-in-events.js
const EventEmitter = require("node:events");
const emitter = new EventEmitter();

// Register listener
emitter.on("order-pizza", (size, topping) => {
  console.log(`Order received! Baking a ${size} pizza with ${topping}`);
});

// Register another listener for same event
emitter.on("order-pizza", (size) => {
  if (size === "large") {
    console.log("Serving complimentary drink");
  }
});

// Emit event
emitter.emit("order-pizza", "large", "mushrooms");
```
**Output:**
```
Order received! Baking a large pizza with mushrooms
Serving complimentary drink
```
**Learn:** Events decouple code logic. Multiple listeners can respond to one event.

---

### 4.2 **Creating Custom Event Emitters**
```bash
node pizza-shop.js
```
```javascript
// pizza-shop.js - Extend EventEmitter
const EventEmitter = require("events");

class PizzaShop extends EventEmitter {
  constructor() {
    super();
    this.orderNumber = 0;
  }

  order(size, topping) {
    this.orderNumber++;
    this.emit("order", size, topping);  // Emit custom event
  }

  displayOrderNumber() {
    console.log(`Current order number: ${this.orderNumber}`);
  }
}

class DrinkMachine {
  serveDrink(size) {
    if (size === "large") {
      console.log("Serving complimentary drink");
    }
  }
}

const pizzaShop = new PizzaShop();
const drinkMachine = new DrinkMachine();

// Listen for custom events
pizzaShop.on("order", (size, topping) => {
  console.log(`Order received! Baking a ${size} pizza with ${topping}`);
  drinkMachine.serveDrink(size);
});

pizzaShop.order("large", "mushrooms");
```
**Learn:** Extend EventEmitter to create custom event-driven classes.

---

## **Phase 5: Building HTTP Servers**

### 5.1 **Simple HTTP Server**
```bash
node index.js
# Visit: http://localhost:3000
```
```javascript
// index.js - Minimal HTTP server
const http = require("http");

const server = http.createServer((req, res) => {
  res.writeHead(200, { "Content-Type": "text/plain" });
  res.end("Hello world!");
});

const PORT = process.env.PORT || 3000;
server.listen(PORT, () => console.log("Server is running on port 3000"));
```

---

### 5.2 **Routing & Different Response Types**
```bash
node built-in-http.js
# Visit: http://localhost:3000
#        http://localhost:3000/about
#        http://localhost:3000/api
```
```javascript
// built-in-http.js
const http = require("node:http");
const fs = require("node:fs");

const server = http.createServer((req, res) => {
  if (req.url === "/") {
    res.writeHead(200, { "Content-Type": "text/plain" });
    res.end("Home page");
  } else if (req.url === "/about") {
    res.writeHead(200, { "Content-Type": "text/plain" });
    res.end("About Page");
  } else if (req.url === "/api") {
    res.writeHead(200, { "Content-Type": "application/json" });
    res.end(
      JSON.stringify({
        firstName: "Bruce",
        lastName: "Wayne",
      })
    );
  } else {
    res.writeHead(404);
    res.end("Page not found");
  }
});

// Commented examples:
// HTML template response
// const server = http.createServer((req, res) => {
//   res.writeHead(200, { "Content-Type": "text/html" });
//   const name = "Vishwas";
//   let html = fs.readFileSync(`${__dirname}/index.html`, "utf8");
//   html = html.replace("{{name}}", name);
//   res.end(html);
// });

// Streaming HTML response
// const server = http.createServer((req, res) => {
//   res.writeHead(200, { "Content-Type": "text/html" });
//   fs.createReadStream(__dirname + "/index.html").pipe(res);
// });

server.listen(3000, () => {
  console.log("Server running on port 3000");
});
```
**Learn:** Route requests, serve different content types (plain text, JSON, HTML).

---

## **Phase 6: Advanced Concurrency Patterns**

### 6.1 **Event Loop Understanding**
```bash
node event-loop.js
```
The file contains 14 commented experiments. **Uncomment each to learn event loop order:**

```javascript
// Experiment 1: Synchronous code runs first
console.log("console.log 1");
process.nextTick(() => console.log("process.nextTick 1"));
console.log("console.log 2");
// Output: console.log 1 → console.log 2 → process.nextTick 1

// Experiment 2: nextTick queue executes before promise queue
process.nextTick(() => console.log("nextTick 1"));
Promise.resolve().then(() => console.log("Promise 1"));
// Output: nextTick 1 → Promise 1

// Experiment 3: Microtask queues run before timer queue
setTimeout(() => console.log("setTimeout 1"), 0);
process.nextTick(() => console.log("nextTick 1"));
Promise.resolve().then(() => console.log("Promise 1"));
// Output: nextTick 1 → Promise 1 → setTimeout 1
```

**Event Loop Order (Priority):**
1. **Synchronous code** (main script)
2. **Microtask Queue** (process.nextTick, Promises)
3. **Timer Queue** (setTimeout, setInterval)
4. **I/O Queue** (file system, network)
5. **Check Queue** (setImmediate)
6. **Close Queue** (close events)

**Learn:** Understanding this prevents race conditions and debugging nightmares.

---

### 6.2 **Thread Pool (libuv)**
```bash
node thread-pool.js
```
```javascript
// thread-pool.js - Understanding Node's thread pool
const crypto = require("crypto");
const https = require("https");

// Uncomment to set custom thread pool size (default: 4)
// process.env.UV_THREADPOOL_SIZE = 16;

const start = Date.now();
const MAX_CALLS = 12;

// Heavy cryptographic operations use the thread pool
// for (let i = 0; i < MAX_CALLS; i++) {
//   crypto.pbkdf2("a", "b", 100000, 512, "sha512", () => {
//     console.log(`Hash: ${i + 1}`, Date.now() - start);
//   });
// }

// Network requests (https) don't use thread pool
// They're handled by the OS
for (let i = 0; i < MAX_CALLS; i++) {
  https
    .request("https://www.google.com", (res) => {
      res.on("data", () => {});
      res.on("end", () => {
        console.log(`Request: ${i + 1}`, Date.now() - start);
      });
    })
    .end();
}
```
**Learn:** 
- CPU-heavy operations (crypto, compression) use the thread pool
- I/O operations (network, files) use OS-level async
- Default pool size is 4; adjust for CPU-bound work

---

### 6.3 **Worker Threads - Running CPU-Intensive Code**

#### Step A: Worker Script
```javascript
// worker-thread.js - Runs in separate thread
const { parentPort } = require("worker_threads");

let j = 0;
for (let i = 0; i < 6000000000; i++) {
  j++;
}

parentPort.postMessage(j);  // Send result back to main thread
```

#### Step B: Main Thread Using Worker
```bash
node main-thread.js
# Visit: http://localhost:8000
#        http://localhost:8000/slow-page (uses worker)
```
```javascript
// main-thread.js - Delegate CPU work to worker
const { Worker } = require("worker_threads");
const http = require("http");

const server = http.createServer((req, res) => {
  if (req.url === "/") {
    res.writeHead(200, { "Content-Type": "text/plain" });
    res.end("Home page");
  } else if (req.url === "/slow-page") {
    // Spawn worker for heavy computation
    const worker = new Worker("./worker-thread.js");

    worker.on("message", (j) => {
      res.writeHead(200, { "Content-Type": "text/plain" });
      res.end(`Slow Page ${j}`);
    });

    worker.on("error", (err) => {
      console.log(err);
    });
  }
});

server.listen(8000, () => console.log("Server is running on port 8000"));
```

**Learn:** Worker threads prevent blocking the event loop for CPU-heavy operations.

---

### 6.4 **Cluster Module - Multiple Processes**

#### Comparison: No Clustering
```bash
node no-cluster.js
# Try: http://localhost:8000/slow-page
# HOME request will block until slow-page finishes
```
```javascript
// no-cluster.js - Single process, blocking
const http = require("http");

const server = http.createServer((req, res) => {
  if (req.url === "/") {
    res.writeHead(200, { "Content-Type": "text/plain" });
    res.end("Home page");
  } else if (req.url === "/slow-page") {
    for (let i = 0; i < 6000000000; i++) {} // Blocks everything!
    res.writeHead(200, { "Content-Type": "text/plain" });
    res.end("Slow Page");
  }
});

server.listen(8000, () => console.log("Server is running on port 8000"));
```

#### With Clustering
```bash
node cluster.js
# Each request gets its own worker process
```
```javascript
// cluster.js - Multiple worker processes
const cluster = require("cluster");
const http = require("http");
const numOfCPUs = require("os").cpus().length;

if (cluster.isMaster) {
  console.log(`Master process ${process.pid} is running`);
  
  // Fork one worker per CPU core
  for (let i = 0; i < numOfCPUs; i++) {
    console.log(`Forking process number ${i}...`);
    cluster.fork();
  }
  
  // Restart workers if they crash
  cluster.on("exit", (worker, code, signal) => {
    console.log(`worker ${worker.process.pid} died`);
    cluster.fork();
  });
} else {
  const server = http.createServer((req, res) => {
    if (req.url === "/") {
      res.writeHead(200, { "Content-Type": "text/plain" });
      res.end("Home page");
    } else if (req.url === "/slow-page") {
      for (let i = 0; i < 6000000000; i++) {}
      res.writeHead(200, { "Content-Type": "text/plain" });
      res.end("Slow Page");
    }
  });

  server.listen(8000, () => console.log("Server is running on port 8000"));
  console.log(`Worker ${process.pid} started`);
}
```

**Learn:**
- **Worker Threads:** Share memory, lighter, for CPU-bound work
- **Cluster:** Separate processes, share socket, for I/O-bound work
- Clustering scales to all CPU cores

---

## **Phase 7: NPM Packages & CLI Tools**

### 7.1 **Custom Reusable Package**
```bash
cd my-custom-package
npm install
```
```javascript
// my-custom-package/index.js - Reusable module
const upperCase = require("upper-case").upperCase;

function greet(name) {
  console.log(upperCase(`Hello ${name}, welcome to Codevolution`));
}

module.exports = greet;
```
```json
{
  "name": "my-custom-package-1",
  "version": "1.0.0",
  "main": "index.js",
  "dependencies": {
    "upper-case": "^2.0.2"
  }
}
```
**Learn:** Create and publish npm packages.

---

### 7.2 **CLI Application**
```bash
cd my-cli-package
npm install
node index.js
```
```javascript
// my-cli-package/index.js - Interactive CLI
#!/usr/bin/env node

const inquirer = require("inquirer");

const printFiveMoves = async (pokemonName) => {
  const response = await fetch(
    `https://pokeapi.co/api/v2/pokemon/${pokemonName}`
  );
  const pokemon = await response.json();
  const moves = pokemon.moves.map(({ move }) => move.name);
  console.log(moves.slice(0, 5));
};

const prompt = inquirer.createPromptModule();
prompt([
  {
    type: "input",
    name: "pokemon",
    message: "Enter a pokemon name to view its first 5 moves",
  },
]).then((answers) => {
  const pokemon = answers.pokemon;
  printFiveMoves(pokemon);
});
```
```json
{
  "name": "my-cli-package-1",
  "version": "1.0.0",
  "main": "index.js",
  "bin": {
    "my-cli-package-1": "index.js"
  },
  "dependencies": {
    "inquirer": "^8.2.5",
    "yargs": "^17.6.2"
  }
}
```
**Learn:** Create interactive CLI tools with `inquirer` for prompts.

---

## **Learning Path Summary**

| Phase | Topics | Files |
|-------|--------|-------|
| **1** | JS Basics | batman.js, superman.js, iife.js |
| **2** | Modules | add.js, math.js, super-hero.js, main.mjs, math-esm.mjs |
| **3** | Built-ins | built-in-path.js, built-in-fs.js, built-in-fs-promises.js, buffer.js, streams.js |
| **4** | Events | built-in-events.js, pizza-shop.js |
| **5** | HTTP | index.js, built-in-http.js |
| **6** | Concurrency | event-loop.js, thread-pool.js, worker-thread.js, main-thread.js, no-cluster.js, cluster.js |
| **7** | NPM | my-custom-package/, my-cli-package/ |

---

## **Quick Reference: Running Each File**

```bash
# Phase 1: Basics
node batman.js
node superman.js
node iife.js

# Phase 2: Modules
node add.js
node math.js
node super-hero.js
node main.mjs

# Phase 3: Built-ins
node built-in-path.js
node built-in-fs.js
node built-in-fs-promises.js
node buffer.js
node streams.js

# Phase 4: Events
node built-in-events.js
node pizza-shop.js

# Phase 5: HTTP
node index.js                    # Visit http://localhost:3000
node built-in-http.js            # Visit http://localhost:3000

# Phase 6: Concurrency
node event-loop.js               # Uncomment experiments to test
node thread-pool.js
node main-thread.js              # Visit http://localhost:8000
node no-cluster.js               # Visit http://localhost:8000
node cluster.js                  # Visit http://localhost:8000

# Phase 7: NPM
cd my-custom-package && npm install && node index.js
cd my-cli-package && npm install && node index.js
```

---

## **Tips for Learning**

1. **Run Each File:** Execute every script to see the output
2. **Modify & Experiment:** Change values and see what breaks
3. **Uncomment Code:** Many files have commented alternatives—study them
4. **Read Carefully:** The comments explain WHY, not just WHAT
5. **Connect Concepts:** See how modules → events → HTTP all interact
6. **Progress Gradually:** Don't skip phases—they build on each other

**Happy coding! 🚀**
