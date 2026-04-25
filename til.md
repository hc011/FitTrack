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


