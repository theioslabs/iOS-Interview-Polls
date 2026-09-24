# 🍎 iOS Labs — Interview Polls & Deep Dives

> **Don't memorize answers. Understand the engineering behind them.**

Welcome to **iOS Labs Interview Polls** — a community-driven collection of real-world iOS engineering scenarios, interview questions, discussions, and detailed technical breakdowns.

This repository grew out of the daily **iOS Interview Polls** shared within the **iOS Labs community**.

What usually starts as one innocent poll... 😄

```text
Here's a scenario 👀
        ↓
What would you choose?
        ↓
Community votes
        ↓
"Wait... but what about this edge case?" 😂
        ↓
Discussion
        ↓
Deep Dive
```

...often turns into a much deeper engineering conversation.

So instead of letting those discussions disappear into WhatsApp history, we're documenting them here. ❤️

---

## 🎯 Why This Repository?

There are already thousands of resources containing questions like:

> "What is an Actor?"

> "What is @State?"

> "What is dependency injection?"

Those are useful.

But real interviews — especially at **Senior iOS Engineer** level — often go beyond definitions.

You may instead hear:

> **"Here's a production scenario. How would you design it?"**

And suddenly there isn't one magical API or one-line answer.

You need to think about:

- 🧠 Architecture
- ⚖️ Trade-offs
- 🚀 Performance
- 🔄 State & lifecycle
- 🧵 Concurrency
- 📱 Platform behavior
- 🛡️ Reliability
- 🧩 Edge cases
- 🏗️ Maintainability
- 🌍 Real production constraints

That's what this repository focuses on.

---

# 🧠 Our Approach

Each topic starts with a **scenario-based poll**.

Instead of asking:

```text
What is ActivityKit?
```

we might ask:

```text
You're building a food-delivery app.

The user locks the phone.
The application may no longer be actively running.
The backend continues receiving order updates.

How would you keep the Live Activity updated?
```

Now the discussion becomes much more interesting. 😈

We explore:

```text
Scenario
   ↓
Possible Approaches
   ↓
Community Poll
   ↓
Why Each Option Could Work
   ↓
Where Each Option Breaks Down
   ↓
Trade-offs
   ↓
Production Architecture
   ↓
Edge Cases
   ↓
Interview Follow-ups
   ↓
Key Takeaways
```

The goal isn't simply to find:

> ✅ **The correct option**

The goal is to understand:

> 🧠 **Why would we choose it?**

and equally importantly:

> 🤔 **When would another option actually make more sense?**

---

# 📚 Topics We'll Explore

The repository will gradually cover areas across the iOS ecosystem.

### 🐦 Swift

- Value vs reference semantics
- Protocols & generics
- Property wrappers
- Memory management
- ARC
- Error handling
- Codable
- Existentials
- Macros
- Modern Swift features

### 🎨 SwiftUI

- State management
- Identity
- View lifecycle
- Observation
- Rendering
- Layout
- Navigation
- Animations
- Custom Shapes
- Performance
- SwiftUI ↔ UIKit interoperability

### 🧵 Swift Concurrency

- async/await
- Actors
- Sendable
- Structured concurrency
- Task groups
- Cancellation
- Actor isolation
- Reentrancy
- Race conditions
- MainActor

### 🏗️ Architecture

- MVVM
- Clean Architecture
- Repository pattern
- Dependency injection
- State ownership
- Offline-first architecture
- Caching
- Synchronization
- Modularization
- Scalability

### ⚡ Performance

- SwiftUI invalidation
- Rendering
- Memory
- Instruments
- Scrolling performance
- Networking
- Caching
- Image loading
- App launch performance

### 📱 iOS Platform & System APIs

- Push Notifications
- ActivityKit
- Live Activities
- Background execution
- Deep Links
- Universal Links
- Widgets
- App lifecycle
- Keychain
- Core Data / SwiftData
- AVFoundation
- WebKit
- Core Animation

### 🧪 Production Engineering

Because sometimes the most interesting interview question is simply:

> **"What happens when this fails in production?"** 😄

We'll discuss:

- Failure handling
- Network reliability
- Offline states
- Race conditions
- API contracts
- Backward compatibility
- Observability
- Debugging
- Testing
- Real-world edge cases

---

# 📖 Deep Dives

Every poll can eventually become a detailed Markdown breakdown.

For example:

### 📲 Push Notifications × Live Activities

Topics explored:

- Regular APNs notifications
- ActivityKit
- Local Live Activity updates
- Live Activity push tokens
- Push-to-Start
- Backend-driven updates
- Token lifecycle
- Timestamps
- Stale states
- Ending Live Activities
- Dismissal behavior
- Production architecture

And most importantly:

> **Who owns the source of truth?**

Because architecture usually matters more than memorizing the API call.

---

# 🗂️ Repository Structure

As the repository grows, topics will be organized by domain.

```text
iOS-Interview-Polls/
│
├── README.md
│
├── Swift/
│
├── SwiftUI/
│
├── Concurrency/
│
├── Architecture/
│
├── Performance/
│
├── System-APIs/
│   └── Push-Notifications-Live-Activities.md
│
└── Production-Engineering/
```

This structure will evolve as the collection grows.

---

# 🎤 How To Use This For Interview Preparation

Don't just read the answer.

Try this instead.

### 1️⃣ Read only the scenario

Stop before reading the breakdown.

### 2️⃣ Pick your approach

Ask yourself:

> **What would I do if this were my production application?**

### 3️⃣ Explain WHY

Imagine the interviewer asks:

> "Why?"

Then:

> "What are the trade-offs?"

Then:

> "What happens if the app is terminated?"

Then:

> "What happens at scale?"

😈

### 4️⃣ Read the breakdown

Compare your reasoning with the different approaches.

### 5️⃣ Challenge the solution

Ask:

> "Under what circumstances would another option be better?"

That's where the real learning starts.

---

# 💡 There May Not Always Be One Correct Answer

This is important.

Software engineering is full of:

```text
"It depends."
```

😂

But **"it depends" is not the final answer.**

A strong engineer should be able to explain:

```text
It depends on WHAT?
```

For example:

- Product requirements
- Data ownership
- Deployment target
- Performance constraints
- Existing architecture
- Team expertise
- Scale
- Reliability requirements
- Maintenance cost

Our breakdowns focus heavily on those trade-offs.

---

# 🤝 Community Driven

These scenarios originate from discussions within the **iOS Labs community**.

That means the learning doesn't come from one person.

It comes from:

❤️ Community votes  
💬 Discussions  
🤔 Different opinions  
🏗️ Production experiences  
🐛 Bugs we've encountered  
💡 Alternative approaches  
🔥 Interview experiences

Sometimes someone choosing a different poll option creates the **best discussion of the day**.

So disagree.

Challenge assumptions.

Ask questions.

Bring your production experience.

That's exactly what we want.

---

# 🙌 Contributions Are Welcome

Have an interesting iOS interview scenario?

Found an edge case we missed?

Disagree with one of the approaches?

Have a better production solution?

**Open an Issue or submit a Pull Request.**

A good contribution might include:

- A new scenario
- Another possible solution
- Production experience
- Edge cases
- Corrections
- Code examples
- Performance observations
- Interview follow-up questions

Just one request:

### Explain the **WHY**, not only the **WHAT**. ❤️

---

# 🌐 What's Next?

GitHub is only the beginning.

We're gradually building these discussions into a larger **iOS Labs learning library**.

Today:

```text
WhatsApp Poll
      ↓
Community Discussion
      ↓
GitHub Deep Dive
```

Soon:

```text
Poll
  ↓
Discussion
  ↓
GitHub
  ↓
iOS Labs Website 🌐
  ↓
Permanent Learning Resource ❤️
```

The goal is simple:

### Turn everyday community discussions into reusable knowledge for iOS developers everywhere.

---

# 🍎 About iOS Labs

**iOS Labs** is a global community of iOS developers focused on learning, knowledge sharing, interview preparation, open source, mentorship, and real-world engineering discussions.

We're interested in more than:

```text
"How do I write this syntax?"
```

We want to understand:

```text
Why does it work?

When should we use it?

When should we NOT use it?

What are the trade-offs?

What happens in production?
```

Because that's where engineering gets interesting. ❤️

---

# ⭐ Support The Initiative

If you find these deep dives useful:

⭐ **Star the repository**

👀 **Watch for new discussions**

🍴 **Fork and experiment**

🤝 **Contribute**

📤 **Share it with another iOS developer**

And most importantly...

### Participate in the discussions.

Don't be a silent `UIView`. 😂

Become an:

```swift
@Observable
final class IOSDeveloper {
    var isLearning = true
    var isSharing = true
    var isGrowing = true
}
```

😂🍎❤️

---

# ❤️ One Last Thing

You don't become a better engineer by memorizing 500 interview answers.

You become better by learning how to:

**Think.**

**Question.**

**Compare.**

**Debug.**

**Make trade-offs.**

**Explain your decisions.**

And keep learning from other engineers.

---

## 🍎 iOS Labs

### Learn • Discuss • Build • Share • Grow 🚀

> **Don't memorize answers. Understand the engineering behind them.**