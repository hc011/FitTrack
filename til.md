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



# Day 12 - `Promise.allSettled` and Resilient UI

**concept** - When building user interfaces, you rarely want an entire dashboard to crash because one minor endpoint failed. Promise.allSettled waits for all promises in the array to finish executing, regardless of whether they succeed or fail. Instead of throwing a fatal error, it returns an array of objects describing the exact outcome ("fulfilled" or "rejected") of each promise, allowing the UI to gracefully handle partial failures.

**code** - 

```javascript
// Simulating a failed endpoint
const fetchRecommendations = () => Promise.reject("Server 500 Error");

async function loadResilientDashboard() {
    const results = await Promise.allSettled([
        fetchUserProfile(),
        fetchRecommendations()
    ]);

    console.log(results);
    /* Output:
    [
      { status: "fulfilled", value: { id: 1, name: "power_renter" } },
      { status: "rejected", reason: "Server 500 Error" }
    ]
    */
}

loadResilientDashboard();
```

# Day 13 - Currying, partial application

**concept** - Currying is the architectural pattern of converting a function that takes multiple arguments into a sequence of nested functions that each take a single argument: `f(a, b, c)` becomes `f(a)(b)(c)`. 

The primary benefit is **Partial Application**. Because of closures, we can supply the first argument, lock it into memory, and save the returned function to a variable. This allows us to generate highly reusable, customized utility functions without duplicating logic. 

**code** - 

```typescript

const calculatePrice = (discountRate: number, basePrice: number, days: number) => {
        const total = basePrice * days;
        return total - (total * discountRate);
};

const curriedCalculatePrice = (discountRate: number) => (basePrice: number) => (days: number) => {
    const total = basePrice * days;
    return total - (total * discountRate);
}

// Partially applying the 15% discount. It returns a function expecting basePrice.
const applyPremiumDiscount = curriedCalculatePrice(0.15);

// Reusing our custom utility
console.log(applyPremiumDiscount(50)(3)); // Calculates 50 * 3 with 15% off
console.log(applyPremiumDiscount(20)(5)); // Calculates 20 * 5 with 15% off
```

# Day 14 - DOM architecture and Event delegation

**concept** - Event Bubbling is the DOM mechanic where an event triggered on a child element ripples up through all its parent elements. We leverage this for **Event Delegation**: instead of attaching thousands of memory-heavy event listeners to individual child elements, we attach a single listener to the parent. We then use `event.target` to determine exactly which child initiated the bubble.

JavaScript doesn't capture the click. The browser captures the click. JavaScript is just waiting to be notified.

1. The Physical Click (The Browser's Job)
When you press your mouse button, your operating system tells the web browser: "The user clicked at coordinates X: 450, Y: 800."

The browser looks at its rendering engine, maps those coordinates to the screen, and determines that the deepest HTML element at that exact pixel is your <li class="patient-card">.

At this exact millisecond, an Event Object is born. The browser stamps this object with an unchangeable property: target: li. The target is forever recorded as the element that physically received the click.

2. The Target Phase (Checking for Listeners)
The browser now asks the li: "Hey, I have a click event for you. Do you have a JavaScript addEventListener attached to you?"

Because you didn't write one, the li effectively says: "No, I don't care about this click. Pass it on." If Event Bubbling didn't exist, the event would die right here. But the DOM is built to bubble.

3. The Bubbling Phase (The Elevator Ride)
Because the li didn't handle the event, the Event Object gets in the DOM elevator and goes up one floor to the parent element: the <ul id="waiting-room-list">.

The browser asks the ul: "Hey, a click just happened inside you on one of your children. Do you have an addEventListener attached?"

This time, the ul says: "Yes! I have a JavaScript function waiting for clicks!"

4. The Execution (target vs currentTarget)
The browser hands the Event Object over to your JavaScript function. This is where the magic of Event Delegation happens.

Inside your function, you have access to two very distinct properties:

event.currentTarget (The Listener): This is the element that currently holds the event listener. In this case, it is the <ul>. (If you use the this keyword inside a standard function here, it points to the ul).

event.target (The Origin): This is the element where the event was originally born before it started bubbling up. It still points exactly to the <li>.

Here are the three reasons why we don't just attach one massive document.addEventListener('click') for everything:

1. The "God Function" (Spaghetti Code)
If you attach one listener to the top of the document, that single function has to handle every possible click on your entire website.

Imagine the if/else statement inside that function:

JavaScript
document.addEventListener('click', (event) => {
    if (event.target.classList.contains('patient-card')) { /* open profile */ }
    else if (event.target.id === 'submit-btn') { /* submit form */ }
    else if (event.target.classList.contains('nav-link')) { /* change page */ }
    else if (event.target.classList.contains('modal-close')) { /* close modal */ }
    // ... 500 more if statements ...
});
This becomes an unmaintainable nightmare. By attaching listeners to specific "container" parents (like the ul for the patient list, or a form tag for the inputs), you keep your logic modular and separated.

2. The stopPropagation Roadblock
Sometimes, you explicitly want to break the Event Bubbling chain.

Imagine you have a dropdown menu. You want the menu to close if the user clicks anywhere else on the screen, but you don't want it to close if they click inside the menu itself. Developers often use event.stopPropagation() on the dropdown to kill the bubble right there.

If your only event listener is at the very top of the DOM, and a child component uses stopPropagation(), the event will never reach the top. Your top-level listener will be completely blind to that click.

3. High-Frequency Performance Killers
Click events are relatively rare. But what about mousemove or scroll events? These fire hundreds of times per second.

If you delegate a mousemove event to the top of the document, your browser is forcing the JavaScript engine to evaluate your if/else logic hundreds of times a second, no matter where the mouse is on the screen. This will instantly cause the browser to drop frames and feel sluggish. It is much safer to attach high-frequency events only to the specific elements that need them.

The Engineering Sweet Spot
The rule of thumb for senior engineers is the Container Strategy:
Don't put a listener on every single child, and don't put one listener on the entire universe. Put the listener on the closest common parent that groups the data together (like the <ul> containing the <li>s, or a <section> containing a specific widget).

React solves the "God Function" spaghetti code problem by completely intercepting the browser's event system and recreating it inside its own JavaScript memory. This is called the Synthetic Event System.

Here is the exact step-by-step engine execution of how React manages this without creating a giant if/else nightmare.

1. The Switchboard (The Fiber Tree)
When your React app loads, React builds its Virtual DOM (the Fiber tree).

When it sees your onClick={handleCheckout} prop, it doesn't touch the real DOM. Instead, it stores handleCheckout inside a giant internal JavaScript dictionary (a hash map), linking that function to that specific component's internal ID.

2. The Single Root Listener
Since React 17, React attaches exactly one native addEventListener for clicks at the very root of your app: the <div id="root">. (It used to attach it to the document, but they changed it so you could run multiple React apps on the same page).

3. The Interception
You click the button.
Because there is no listener on the button, the native DOM event naturally bubbles all the way up to <div id="root">. React's master listener catches it.

4. The Wrapper (Synthetic Event)
React immediately takes the native browser event and wraps it in its own custom object called a SyntheticEvent. It does this to ensure the event behaves exactly the same way whether you are on Chrome, Safari, or an old version of Internet Explorer.

5. The Simulated Bubble (The Magic Step)
This is where React solves the "God Function" problem.

React's master listener looks at the event.target (the button). It then goes to its internal Virtual DOM dictionary and says:
"Did this button have an onClick prop registered?" -> Yes.
"Did its parent div have an onClick?" -> No.
"Did the parent section have an onClick?" -> Yes.

React gathers all the matching functions from its dictionary and puts them into an array. It then loops through that array and executes them one by one, perfectly simulating the bubbling phase entirely in JavaScript memory.



**code** - 
```javascript
// <ul id="waiting-room-list" >
//     <li class="patient-card" data - id="101" > John Doe </li>
//         < li class="patient-card" data - id="102" > Jane Smith </li>
//     < li class="patient-card" data - id="103" > Michael Johnson </li>
// </ul>

// const list = document.getElementById("waiting-room-list");

// const onClick = (event: any) => {
//     if (event.target.classList.contains("patient-card")) {
//         console.log(`opening the profile for ${event.target.dataset.id}`);
//     }
// }

// list?.addEventListener('click', (event) => onClick(event));

const list = document.getElementById("waiting-room-list");

const onClick = (event) => {
    // 1. Verify the exact child that was clicked
    if (event.target.classList.contains("patient-card")) {

        // 2. Extract the data-id from the dataset API
        const patientId = event.target.dataset.id;

        console.log(`Opening profile for patient ID: ${patientId}`);
    }
}

// 3. Attach a single listener to the parent. 
// The browser will automatically pass the 'event' argument to onClick.
list?.addEventListener('click', onClick);
```