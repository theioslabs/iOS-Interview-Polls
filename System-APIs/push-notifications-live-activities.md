# 🍎 Push Notifications × Live Activities — Complete Breakdown

Yesterday's iOS Labs poll was around a very interesting production scenario:

> **How would you architect Push Notifications + Live Activities for something like a food-delivery application?**

Imagine the order moving through:

```text
Order Confirmed
      ↓
Preparing
      ↓
Rider Assigned
      ↓
On the Way
      ↓
Arriving
      ↓
Delivered
```

But there was a catch.

The app could be:

- 📱 In the foreground
- 🌙 In the background
- ❌ Not actively running
- ☁️ While the backend continues receiving order updates
- 🚀 And in some scenarios, the backend may need to **start the Live Activity remotely**

So let's break down every option.

Interestingly, not every "wrong" option is actually useless.

It depends entirely on the requirement.

---

# 🅰️ Option A — Regular APNs + App Handles Updates

At first, this sounds reasonable.

The backend receives:

```text
Rider Assigned
```

and sends a regular push notification.

The app receives that event and updates the Live Activity:

```swift
await activity.update(...)
```

## Would this work?

In some situations, yes.

But there is an architectural problem.

We are making the **application process responsible for translating remote events into Live Activity updates**.

We shouldn't design the entire feature assuming:

> "Every normal push notification will wake my application and my code will definitely execute."

That isn't a reliable foundation for continuously updating a Live Activity.

If the backend already knows:

```text
Rider is 2 minutes away
```

why design the flow as:

```text
Backend
   ↓
Regular Push Notification
   ↓
Application
   ↓
ActivityKit
   ↓
Live Activity
```

when ActivityKit supports server-driven Live Activity updates through APNs?

The architecture can instead become:

```text
Backend
   ↓
APNs
   ↓
Live Activity
```

## Where is Option A still useful?

Regular notifications still make complete sense when we actually want to **notify the user**.

For example:

> 🔔 "Your rider has arrived."

That's different from continuously keeping the Live Activity's state current.

### Key takeaway

**Notification ≠ Live Activity Update**

They may share APNs infrastructure, but they solve different UX problems.

---

# 🅱️ Option B — Local ActivityKit + Polling

Now imagine the user places an order while using the application.

We start the Live Activity locally:

```swift
let activity = try Activity.request(...)
```

Then periodically fetch:

```text
GET /order/123
```

and update the Live Activity locally.

## Does this work?

Again...

**Yes, under the right circumstances.**

Local updates are completely valid.

For example, imagine a workout application where information is being generated locally:

- ❤️ Heart rate
- ⏱️ Workout duration
- 🏃 Current progress
- 🔥 Calories
- 📍 Locally calculated workout information

The application may legitimately update its own Live Activity.

But our food-delivery example is different.

The **backend owns the delivery state**.

If the backend already knows that the rider has moved from:

```text
Preparing
```

to:

```text
Rider Assigned
```

why should the application continuously ask:

> "Anything new?"

The architecture becomes:

```text
Backend already has update
          ↓
App needs to fetch it
          ↓
App processes response
          ↓
App updates ActivityKit
```

And once the application isn't actively executing, relying on repeated application-side polling becomes a poor architecture for keeping server-owned state fresh.

### Key takeaway

**Local ActivityKit updates ≠ bad**

But:

**Local polling for server-owned Live Activity state ≠ necessarily the right architecture**

The important question is:

> **Where does the information originate?**

---

# 🅲 Option C — Live Activity Push Token + APNs 🔥

Now we're getting much closer to our food-delivery requirement.

Suppose the user places an order while inside the application.

The app starts the Live Activity and requests push updates.

Conceptually:

```swift
Activity.request(
    attributes: attributes,
    content: content,
    pushType: .token
)
```

ActivityKit provides a **Live Activity push token asynchronously**.

The application sends this token to the backend.

The architecture becomes:

```text
User places order
        ↓
App starts Live Activity
        ↓
ActivityKit provides push token
        ↓
App sends token to Backend
        ↓
Backend stores token
        ↓
Order state changes
        ↓
Backend sends ActivityKit push through APNs
        ↓
Live Activity updates
```

🔥 Much better for our server-driven use case.

Now the backend can drive:

```text
Preparing
    ↓
Rider Assigned
    ↓
On the Way
    ↓
Arriving
    ↓
Delivered
```

without designing the feature around the main application being actively running.

---

## 🔑 Important — Don't Treat the Token as Permanent

A common mistake would be:

> "I received the token once. I'll save it forever."

Live Activity push tokens **can change**.

So the application should observe:

```swift
activity.pushTokenUpdates
```

and synchronize token changes with the backend.

Conceptually:

```text
Order ID
    ↕
Live Activity / Business Context
    ↕
Current Activity Push Token
```

This means Live Activities are not purely an iOS implementation problem.

They become a:

### Client + Backend Architecture Problem

---

# 🅳 Option D — Push-to-Start 🚀

Now let's slightly change the requirement.

Imagine the relevant event happens later.

Examples:

- 🏏 A cricket match begins
- ✈️ Flight tracking begins
- 📦 Delivery reaches an important stage
- 🚕 Ride becomes active
- 🛒 Order processing begins

And the product requirement says:

> **"The backend should be able to START the Live Activity remotely."**

This is fundamentally different.

ActivityKit supports **Push-to-Start**.

Conceptually, the application receives a Push-to-Start token and synchronizes it with the backend.

```text
Application
     ↓
Push-to-Start Token
     ↓
Backend stores token
```

Later:

```text
Backend determines
Live Activity should start
          ↓
         APNs
          ↓
    ActivityKit
          ↓
Live Activity STARTS
```

🔥

The application didn't need to manually create that specific Live Activity at that exact moment.

---

# 🧠 Option C vs Option D

This distinction is extremely important.

### Option C asks:

> **"The Live Activity already exists. How can my backend UPDATE it?"**

### Option D asks:

> **"How can my backend remotely START a Live Activity?"**

These are two different requirements.

So:

```text
C → Remote UPDATE

D → Remote START
```

Don't think of C and D as competing technologies.

They solve different lifecycle problems.

---

# 🅴 Option E — Hybrid Architecture 🏆

For yesterday's exact scenario, this was the most complete architectural direction.

Not because:

> "Hybrid sounds more senior." 😂

But because the requirement contains **multiple initiation flows**.

---

## Scenario 1 — User Starts the Experience

Imagine the user orders food from inside the application.

```text
User places order
       ↓
App starts Live Activity locally
       ↓
ActivityKit provides push token
       ↓
Token sent to backend
       ↓
Backend receives order updates
       ↓
APNs
       ↓
Live Activity updates
```

This is a natural use case for a **locally started Live Activity + remote push updates**.

---

## Scenario 2 — Backend Starts the Experience

Now imagine some server-side event determines that a Live Activity should begin.

```text
Push-to-Start Token
       ↓
Backend
       ↓
Relevant event occurs
       ↓
APNs
       ↓
Live Activity starts remotely
       ↓
Backend continues sending updates
```

This is where **Push-to-Start** becomes relevant.

Therefore:

### C and D aren't competitors.

They're solving different initiation requirements.

That's why **Option E — Hybrid** was the strongest architectural direction for yesterday's scenario.

---

# 😈 Bonus Scenario — Out-of-Order Updates

Remember yesterday's bonus question?

Imagine the backend generates:

```text
10:01 → Rider Assigned

10:03 → On The Way
```

But because distributed systems enjoy ruining our peaceful lives... 😂

the older information arrives after the newer information.

Should the UI suddenly become:

```text
On The Way
    ↓
Rider Assigned
```

💀

Obviously, that's undesirable.

This introduces another important concept:

## `timestamp`

ActivityKit push payloads include a timestamp.

Correct temporal ordering matters when sending Live Activity updates.

More broadly, this reinforces an important architectural idea:

> **For server-owned state, the backend should be authoritative about the progression of that state.**

---

# ⏰ What Is `stale-date`?

This is another interesting part of Live Activity architecture.

Imagine the Live Activity currently displays:

> **Rider arriving in 2 minutes**

Then the device loses connectivity.

Twenty minutes later...

the Live Activity is still showing:

> **Rider arriving in 2 minutes**

😂💀

Technically the UI exists.

But semantically the information is no longer trustworthy.

This is where:

```text
stale-date
```

becomes useful.

Conceptually, we're communicating:

> **"After this point, don't assume this information is still current."**

The lifecycle becomes:

```text
Fresh Content
      ↓
Expected update doesn't arrive
      ↓
stale-date passes
      ↓
Activity becomes stale
```

The UI can then react appropriately instead of confidently displaying outdated information.

### Important takeaway

**Freshness is part of the data model.**

It's not only about displaying the latest value.

We also need to understand:

> **How long is this value trustworthy?**

---

# 🏁 How Should We End a Live Activity?

Our rider finally arrives.

Pizza saved. 🍕😂

The backend can send an ActivityKit push containing:

```json
{
    "event": "end"
}
```

along with the final content state.

For example:

```text
DELIVERED ✓
```

## Why send the final state?

Because ending the Live Activity doesn't necessarily mean the user instantly stops seeing it.

The final state may remain visible for some time.

Therefore the final content should accurately represent the completed experience.

---

# 🗑️ `dismissal-date`

Another important distinction:

### Ending ≠ Immediately Removing

Maybe after delivery we want:

```text
Delivered ✓
```

to remain visible briefly.

Or perhaps our product wants the completed activity removed quickly.

This is where:

```text
dismissal-date
```

becomes relevant.

It helps control when an ended Live Activity should disappear from the Lock Screen.

So think about the lifecycle as:

```text
Active
   ↓
Updated
   ↓
Completed
   ↓
Ended
   ↓
Final state remains visible
   ↓
Dismissed
```

---

# 🔑 What If the Push Token Changes?

Here's another classic production bug:

```text
App receives Token A
        ↓
Backend stores Token A
        ↓
ActivityKit provides Token B
        ↓
App ignores the change
        ↓
Backend continues sending to Token A
        ↓
Updates stop working
        ↓
Developer:
"APNs is broken." 😂
```

Maybe APNs isn't broken.

Maybe our **token lifecycle** is broken. 😈

For an existing Live Activity, observe:

```swift
activity.pushTokenUpdates
```

For remote-start scenarios, observe the relevant Push-to-Start token updates as well.

Then synchronize those changes with your backend.

---

# 🏗️ Bigger Architecture

For our food-delivery example, think about the system approximately like this:

```text
                ┌──────────────────────┐
                │       Backend        │
                │   Source of Truth    │
                └──────────┬───────────┘
                           │
                    Order State Changes
                           │
                           ▼
                         APNs
                           │
                           ▼
                  ActivityKit Push
                           │
                           ▼
                ┌────────────────────┐
                │   Live Activity    │
                │ Lock Screen / etc. │
                └────────────────────┘
```

Meanwhile, the application handles responsibilities such as:

```text
Activity lifecycle
        +
Token registration
        +
Token changes
        +
Availability / permissions
        +
Local starts where appropriate
        +
Backend synchronization
```

And the backend handles responsibilities such as:

```text
Authoritative business state
        +
Push targeting
        +
Payload generation
        +
Ordering / timestamps
        +
Start / update / end decisions
        +
Token association
```

---

# 📊 Comparing All Five Options

| Option | Approach | Good For | Main Limitation |
|---|---|---|---|
| **A** | Regular APNs → App → ActivityKit | User notifications and app-handled events | Depends too much on app execution for server-driven updates |
| **B** | Local ActivityKit + polling | Locally generated or app-owned state | Poor fit when backend already owns rapidly changing state |
| **C** | Live Activity push token + APNs | Backend updating an existing Live Activity | Activity must already exist |
| **D** | Push-to-Start | Backend remotely initiating a Live Activity | Needed only when remote initiation is actually a requirement |
| **E** | Hybrid C + D | Products with both local and remote initiation flows | More lifecycle/backend complexity to design correctly |

---

# 🎯 So What Was the Answer?

For **yesterday's exact scenario**, the strongest architectural direction was:

## 🏆 Option E — Hybrid

Use **C or D depending on how the experience begins**.

But please don't memorize:

```text
Live Activity Question
        ↓
Answer E
        ↓
Interview cleared 😂
```

That's completely missing the point.

The actual learning is understanding the responsibilities.

---

# 🧠 Mental Model

### 📲 Normal Push Notification

**Purpose:** Tell the user something happened.

Example:

> "Your rider has arrived."

---

### 🟢 Local ActivityKit Update

**Purpose:** Update a Live Activity when the application already knows the new state.

Example:

> Locally generated workout progress.

---

### ☁️ Live Activity Push Token

**Purpose:** Let the backend update an already-running Live Activity through APNs.

Example:

> Delivery status changing while the application isn't actively running.

---

### 🚀 Push-to-Start

**Purpose:** Allow the backend to remotely initiate a Live Activity.

Example:

> A tracked event begins based on a server-side trigger.

---

### ⏰ `stale-date`

**Purpose:** Define when the displayed information should be considered outdated.

---

### 🏁 `event: end`

**Purpose:** Tell ActivityKit that the Live Activity has completed.

---

### 🗑️ `dismissal-date`

**Purpose:** Influence when the ended Live Activity disappears from the Lock Screen.

---

### 🔑 Token Update Streams

**Purpose:** Keep backend targeting synchronized when ActivityKit tokens change.

---

# 🔥 Senior-Level Interview Takeaway

If an interviewer asks:

> **"How would you implement Live Activities?"**

Don't immediately start listing APIs.

Start asking questions.

### 1. Who starts the Live Activity?

App?

Or backend?

### 2. Who owns the source of truth?

Device?

Or server?

### 3. Where do updates originate?

Locally?

Or remotely?

### 4. Does it need to update when the app isn't actively running?

This completely changes the architecture.

### 5. How fresh does the information need to be?

Think about:

```text
stale-date
```

### 6. What happens when updates arrive late or out of order?

Think about:

```text
timestamp
```

and your backend state model.

### 7. Who ends the Live Activity?

Application?

Backend?

Both depending on the flow?

### 8. How long should the final state remain visible?

Think about:

```text
dismissal-date
```

### 9. What happens when tokens change?

Your backend synchronization strategy matters.

---

# ❤️ Final Takeaway

The biggest lesson from yesterday's poll isn't:

> **"Which ActivityKit API should I use?"**

The better questions are:

> **Who owns the state?**

> **Who starts the experience?**

> **Who updates it?**

> **How does it behave when my app isn't running?**

> **When does the information stop being trustworthy?**

> **How does the experience end?**

That's the difference between:

**knowing an API**

and

**designing a production feature.** 🚀

---

## 🍎 iOS Labs

### Think lifecycle. Think ownership. Think architecture.

**Don't memorize APIs. Understand why they exist. ❤️**