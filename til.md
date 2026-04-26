# Today I learned

# Day _ - 

**concept** - 

**code** - 

```typescript


# Day 1 - Closures

**concept** - A clouser is the ability of the function in javascript to remember its lexical environment variables even after the function finishes execution, it also prevents the garbage collector to destroy that private data.

**code** - 

```typescript

type WorkoutSet = {
    weight: number,
    reps: number
}

function createWorkoutTracker() {
    let sets: WorkoutSet[] = [];

    function addSet(weight: number, reps: number) {
        sets.push({ weight, reps });
    }

    function getTotalVolume() {
        let totalVolume = 0;
        for (let set of sets) {
            totalVolume += set.reps * set.weight;
        }
        return totalVolume;
    }

    return { addSet, getTotalVolume };
}

let chestDay = createWorkoutTracker();

chestDay.addSet(40, 2);

console.log(chestDay.getTotalVolume());
```

# Day 2 - Event Loop    

**concept** - Synchronous code $\rightarrow$ All Microtasks (Promises) $\rightarrow$ Render $\rightarrow$ ONE Macrotask (setTimeout) $\rightarrow$ Repeat 

# Day 3 - Promises and async/await 

**concept** - To use `await` with a callback-based API like `setTimeout`, you must wrap it in a `new Promise` constructor. The `await` keyword pauses the execution of that specific function until `resolve()` is called, yielding the thread back to the Event Loop.

**code** - 

```typescript
function sleep(ms: number) {
    return new Promise<void>((resolve) => {
        setTimeout(() => resolve(), ms);
    });
}

async function fetchWorkoutHistory(userId: number) {
    await sleep(2000);
    return `history array for user ${userId}`;
}

fetchWorkoutHistory(2).then(data => console.log(data));
```




# Day 4 - Typescript Generics

**concept** - Generics act as variables for types, allowing us to pass a specific type (like `<T>`) into an interface or function to make it highly reusable without losing strict type safety.

**code** - 

```typescript
interface Workout {
    id: number;
    exercise: string;
}

interface PaginatedResponse<T> {
    totalItems: number;
    currentPage: number;
    data: T[];
}

let myHistory: PaginatedResponse<Workout>;
```

# Day 5 - Interfaces vs types

**concept** - Both define data shapes, but an `interface` is restricted to objects and is "open" (supports declaration merging). A `type` alias can represent any data structure (including Unions like `"success" | "error"`) and is "closed" (throws an error on duplicates).

**code** - 

```typescript
// 1. Interfaces are best for Object Contracts (and can merge)
interface UserProfile {
    id: number;
    username: string;
}

// 2. Types are required for Unions and complex logic
type FetchState = "loading" | "success" | "failed";
```


# Day 6 - Debouncing

**concept** - debounce is used to delay a function call and just execute the operation after a specific delay

**code** - 

```typescript
function debounce(func: (...args:any[]) => void, delay: number) {
        let timerId: ReturnType<typeof setTimeout>

        return function (...args:any[]) {
            clearTimeout(timerId);
            timerId = setTimeout(() => func(...args), delay);
        }

    }

    const searchExercise = () => console.log("Fetching: Bench Press");
    const debouncedSearch = debounce(searchExercise, 500);

    // Simulating a user typing fast:
    debouncedSearch(); // Timer starts
    debouncedSearch(); // Timer resets
    debouncedSearch();
```


# Day 7 - Throttling 

**concept** - Throttling is a performance optimization technique that forces a strict speed limit on a function. Unlike debouncing (which waits for a pause), throttling ensures a function executes at a steady, consistent rhythm (e.g., exactly once every 500ms) by using a boolean lock trapped inside a Closure.

**code** - 

```typescript
function throttle(func: (...args: any[]) => void, delay: number) {
        let isWaiting = false;

        return function (...args: any[]) {
            if (isWaiting) return;

            func(...args);
            isWaiting = true;

            setTimeout(() => {
                isWaiting = false;
            }, delay);
        }
    }
```

# Day 8 - The 'this' keyword and context 

**concept** -  In standard functions, `this` is dynamic and depends on *how* the function is called. Passing a method as a callback (like to `setTimeout`) strips its context, making `this` undefined. We fix this by explicitly locking the context with `.bind()`, or by using an ES6 Arrow Function, which natively inherits `this` from its surrounding lexical scope.

**code** - 

```javascript
const waitingRoom = {
    clinicName: "City Health Clinic",
    
    // Fix 1: Using .bind()
    announcePatient: function() {
        console.log(`Welcome to ${this.clinicName}`);
    },

    // Fix 2: Using an Arrow Function to inherit lexical scope
    triggerDelayedAnnouncement: function() {
        setTimeout(() => {
            console.log(`Welcome to ${this.clinicName}`);
        }, 2000);
    }
};

// Execution
const boundFunc = waitingRoom.announcePatient.bind(waitingRoom);
setTimeout(boundFunc, 2000);

waitingRoom.triggerDelayedAnnouncement();
```

# Day 9 - Explicit binding 'call' and 'apply'

**concept** - Method borrowing allows one object to use another object's method without duplicating code. We use `.call()` and `.apply()` to execute a function immediately while forcing its `this` context to a specific object.
* `.call()` passes arguments separated by **C**ommas.
* `.apply()` passes arguments inside an **A**rray.

**code** - 

```javascript
const premiumUser = {
    username: "power_renter",
    discountRate: 0.10,
    calculateCheckout: function(itemName, basePrice, days) {
        // ... calculation logic ...
    }
};

const standardUser = { username: "guest_user", discountRate: 0.00 };

// Borrowing with call (Comma separated)
premiumUser.calculateCheckout.call(standardUser, "Camera", 50, 3);

let bfunc = premiumUser.calculateCheck.bind(standardUser);

// Borrowing with apply (Array)
premiumUser.calculateCheckout.apply(standardUser, ["Guitar", 20, 5]);
```

# Day 10 - prototypal inheritence

**concept** - JavaScript does not have traditional classes. Instead, objects inherit directly from other objects via a hidden `[[Prototype]]` chain. When a property or method is not found on an object, the engine delegates the lookup up the chain. Using `Object.create(parentObject)` is the native way to build this link.

**code** - 

```javascript
const baseUser = {
        isLoggedIn: false,
        login: function () {
            this.isLoggedIn = true;
            console.log("User successfully logged in.");
        }
    };

    const premiumUser = Object.create(baseUser);

    premiumUser.discountRate = 0.15;

    premiumUser.login();
```

# Day 11 - `Promise.all` and Concurrent Execution

**concept** - When making multiple asynchronous calls that do not depend on each other, awaiting them sequentially creates a massive performance bottleneck. `Promise.all` takes an array of pending Promises and delegates them concurrently to the Web API/environment. It resolves only when every single Promise has finished, returning an array of results in the exact order they were requested.

**Warning:** `Promise.all` is "fail-fast." If even one promise rejects, the entire array rejects immediately.   

**code** - 

```javascript
const fetchUserProfile = () => new Promise(resolve => setTimeout(() => resolve({ id: 1, name: "power_renter" }), 1000));
const fetchActiveRentals = () => new Promise(resolve => setTimeout(() => resolve(["Camera", "Guitar"]), 1500));

async function loadDashboard() {
    // Both requests are handed to the Web API simultaneously. Total wait time: 1.5s
    let [userProfiles, activeRentals] = await Promise.all([fetchUserProfile(), fetchActiveRentals()]);
    console.log({ userProfiles, activeRentals });
}

loadDashboard();
```
