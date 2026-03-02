# 🎯 Mid-Level Interview Preparation Guide: High Probability Questions

This guide contains the most frequently asked **Mid-Level** interview questions that were _not_ covered in the first guide. These are concepts interviewers love to ask to see if you have real hands-on experience and understand _why_ things work, not just _how_ to write the code.

Everything is explained in simple English with real-world examples so you can articulate them perfectly.

---

## 🟢 PART 1: .NET Core & C# (Highly Asked)

### 1. What are the different Lifetimes of Dependency Injection (DI) in .NET Core? (Scoped vs Transient vs Singleton)

_This is arguably the most asked .NET question in interviews._

**Simple Answer & Real-World Example:**
When we register a service in `Program.cs` (or `Startup.cs`), we have to tell .NET how long that service should live.

- **Transient (`AddTransient`):** A new box of fries is created _every single time_ you ask for it.
  - **Tech Meaning:** A new instance of the service is created every time it is injected. Good for lightweight, stateless services.
- **Scoped (`AddScoped`):** One waiter is assigned to your table for your _entire dinner_.
  - **Tech Meaning:** A new instance is created _once per HTTP request_. All classes that ask for this service during the same web request get the _exact same object_. This is perfect for our `DbContext` (Database connection).
- **Singleton (`AddSingleton`):** The restaurant manager. There is only _one_ manager for everybody, all day long.
  - **Tech Meaning:** Only one instance is created when the app starts, and _every_ user shares that same instance until the server stops. Good for caching or configuration settings.

### 2. Difference between Middleware and Action Filters?

**Simple Answer:**

- **Middleware:** Runs for _every single HTTP request_ that hits your server, even if the URL doesn't exist (like a 404 image request). It sits at the outermost door of your app. (Example: Global Error Handling, logging all traffic).
- **Action Filters:** Only run when an actual _Controller Action_ is hit. They sit right outside the specific room you are trying to enter. (Example: Checking if a user has a specific role before letting them execute the `CreateCourse` method).

**When the interviewer asks "Which one should I use?":**
Say: "If I need to intercept _every_ request (like security headers or global exceptions), I use Middleware. If I only care about specific controller methods (like validating an input model), I use Action Filters."

### 3. What is the difference between Eager Loading and Lazy Loading in Entity Framework Core?

**Simple Answer & Real-World Example:**
Imagine you are at a library checking out a "Course" book. That book has a list of "Students" inside it.

- **Eager Loading (`.Include()`):** You tell the librarian, "Bring me the Course book, AND immediately bring me all the Student books associated with it."
  - **Tech Meaning:** EF Core writes one big SQL JOIN query and fetches everything at once. We use `.Include(c => c.Students)` in .NET. It's faster if you know you need the related data.
- **Lazy Loading:** You just borrow the Course book. Later, when you finally flip to the "Students" page, the book magically pauses, runs back to the library, and fetches the students.
  - **Tech Meaning:** EF Core waits until you actually access the `course.Students` property in your code, and _then_ it fires a new SQL query to the database automatically.
  - **Warning:** Mention the **N+1 Problem**. Say: "Lazy loading can be dangerous because if I loop through 100 courses and access their students, EF Core will fire 100 separate queries to the database, which kills performance. I prefer Eager Loading."

### 4. What are Record Types in C# (introduced in C# 9)?

**Simple Answer:**
Records are like Classes, but they are built specifically to handle data that _should not change_ (Immutable data).
**Real World Example:** A bank transaction receipt. Once a transaction receipt is created, you cannot change the amount or the date. It is locked. Records are perfect for Data Transfer Objects (DTOs) that we send to the frontend, because they are lightweight and their values are securely immutable.

---

## 🔵 PART 2: React & Frontend

### 1. How does the Virtual DOM work in React, and why is it fast?

**Simple Answer & Real-World Example:**

- **Real DOM:** Imagine building a huge Lego house (Real DOM). If you want to change one window, taking apart the real house is slow and heavy.
- **Virtual DOM:** React keeps a lightweight "blueprint" (Virtual DOM) of your Lego house in its memory.
- **How it works:** When data changes (state updates), React builds a _new_ lightweight blueprint. It compares the new blueprint with the old blueprint (this is called **Diffing**). It spots exactly what changed (e.g., "Ah, just the window changed!"). Then, it goes to the Real Lego House and _only_ swaps that one window. This way, it doesn't have to rebuild the entire web page.

### 2. Explain the `useEffect` Dependency Array and Cleanup Function.

**Simple Answer:**
The `useEffect` hook lets us perform side effects (like calling APIs) when a component loads.

- **The Dependency Array (`[]`):** Tell React _when_ to run it.
  - No array: Runs on every single render (bad for performance).
  - Empty array `[]`: Runs exactly _once_ when the page first loads (like `componentDidMount`).
  - Array with a variable `[courseId]`: Runs only when the `courseId` value changes.
- **Cleanup Function:** Used to clean up memory.
  - **Real World Example:** If you subscribe to a live chat inside a chat room component, what happens when the user clicks 'Back' and leaves the room? You must unsubscribe, or you'll get memory leaks. The cleanup function inside `useEffect` (the `return () => {}` part) is where we put the code to disconnect the chat.

### 3. Redux vs Context API for State Management

**Simple Answer:**

- **Context API:** Built into React. Like putting a megaphone in the center of the building. Any room can hear it. Good for small, simple things that don't change often (like Dark Mode/Light Mode, or currently logged-in User details).
- **Redux:** A massive, strict warehouse system. Every single movement is logged, tracked, and follows strict rules (Actions and Reducers). Good for massive applications with highly complex data changing constantly.
- **Interview Tip:** "For our current application size, Context API was enough, but if it grew massively, we would adopt Redux Toolkit."

---

## 🟡 PART 3: SQL Database Fundamentals

### 1. What is the difference between a Clustered Index and a Non-Clustered Index? (Very Important)

**Simple Answer & Real-World Example:**
Think of a physical Telephone Book.

- **Clustered Index:** This is how the data is _actually physically stored_ on the hard drive. In a phone book, everybody is physically sorted by their Last Name (A to Z). Because you can only physically sort a book one way, **a table can only have ONE Clustered Index** (usually the Primary Key).
- **Non-Clustered Index:** This is the _Index section at the back of a regular textbook_. It has a sorted list of keywords, and tells you which page to flip to find the actual data. A database table can have multiple Non-Clustered Indexes (e.g., indexing email addresses to make login searches faster).

### 2. What are the ACID properties in SQL?

**Simple Answer:**
ACID guarantees that our database transactions are secure and reliable.
**Real World Example (Bank Transfer):** You are sending $100 to a friend.

- **A - Atomicity (All or Nothing):** The money leaves your account AND enters your friend's account successfully. If the system crashes right after the money leaves your account, the transaction completely rolls back. It never does half a job.
- **C - Consistency:** The database rules are never broken. If a balance cannot be negative, a transaction that makes a balance negative will be rejected.
- **I - Isolation:** If two people try to withdraw the last $10 from an ATM at the _exact same millisecond_, the database puts them in an invisible queue. The second person will get an 'Insufficient Funds' error.
- **D - Durability:** Once the transaction is "Committed," it is saved permanently to the hard drive. Even if the server loses power one second later, the data is safe.

---

## 🟣 PART 4: Architecture & SOLID Principles

### 1. What are the SOLID principles? (Explain at least S and O)

**Simple Answer (The most important two):**

- **S - Single Responsibility Principle:** A class should only have ONE job.
  - _Example:_ Our `CourseController` only handles HTTP traffic. It does NOT write SQL queries. The SQL queries are handled by `CourseService`. If we put everything in one file, it becomes "Spaghetti Code".
- **O - Open/Closed Principle:** Code should be open for extension, but closed for modification.
  - _Example:_ If we want to add "Student Discounts" to our billing system, we should not have to modify the old, perfectly working `Invoice` class. We should _create a new class_ that extends the existing functionality. This prevents breaking stuff that already works.

### 2. What is the Repository Pattern?

**Simple Answer:**
The Repository Pattern acts like a middleman between our Application Logic and the Database.
Instead of our services writing EF Core code directly (`_context.Courses.Add(..)`), we create an interface like `ICourseRepository`.
**Why do interviewers like it?**
"It makes the application highly testable. In unit tests, we don't want to hit a real SQL database. With a repository pattern, we can create a 'Fake Repository' that just holds data in memory for testing, without changing our core business logic."

---

### 💡 Final Interview Strategy: The "I Don't Know" Answer

If an interviewer asks you a question you do not know, **do not lie or guess.**
Say this exactly:
_"To be completely honest, I haven't had the chance to implement [technology/concept] directly in my projects yet. However, based on my understanding, it is used for [X]. In my current project, we handled that problem by doing [Y]. But I am a fast learner and I am actually very excited to learn it."_
