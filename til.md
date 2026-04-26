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