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