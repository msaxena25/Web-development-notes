Congratulations on your promotion opportunity to **Technical Lead / Team Lead** — that's a big step in your career!

Now that you’ve already submitted your project assignment, the **panel discussion/interview** will likely assess not just your technical skills, but also your **leadership readiness**, **people management skills**, and **strategic thinking**. Here's what to expect:

---

### 🔹 1. **Project Assignment Review**

* **Expect questions like:**

  * What was the goal of your assignment?
  * What trade-offs did you make and why?
  * What were the most complex technical decisions?
  * How did you ensure quality and timely delivery?
  * Any improvements you'd make if you had more time?

---

### 🔹 2. **Technical Depth & Architectural Thinking**

* You're expected to **go beyond coding** and show broader system understanding.
* **They might ask:**

  * How would you design a scalable system?
  * What are common bottlenecks in a distributed system and how do you resolve them?
  * How do you choose between different tech stacks?
  * How do you evaluate performance and optimize?

---

### 🔹 3. **Leadership & Team Management**

* How you guide, mentor, and grow a team is crucial.
* **Questions could include:**

  * How do you handle conflicts within a team?
  * How do you manage underperforming team members?
  * How do you mentor junior developers?
  * How would you delegate vs. do it yourself?

---

### 🔹 4. **Project & Sprint Management**

* They may test your grasp of Agile, Scrum, and delivery timelines.
* **You may face:**

  * How do you estimate tasks and manage backlogs?
  * How do you handle mid-sprint scope changes?
  * How do you ensure continuous delivery without compromising quality?

---

### 🔹 5. **Communication & Stakeholder Management**

* Technical leads often bridge between devs and non-devs.
* **Expect:**

  * How do you explain technical concepts to non-technical stakeholders?
  * How do you manage stakeholder expectations when deadlines slip?

---

### 🔹 6. **Behavioral / Situational Questions (STAR Method)**

* Example:

  * “Tell me about a time you failed as a developer or lead — how did you handle it?”
  * “Give an example where you resolved a team conflict.”
  * “Describe a situation where your team was stuck — what did you do?”

---

### 🔹 7. **Vision & Thought Leadership**

* They want to see if you think strategically.
* **Sample questions:**

  * How would you define success as a tech lead?
  * What process improvements would you suggest in your current team?
  * How do you stay up-to-date with tech trends?

---

### ✅ Pro Tips to Prepare:

* **Review your assignment thoroughly** — panel will likely focus on it.
* Prepare examples from **real-world work** for common leadership challenges.
* Be clear on your **philosophy of leadership**.
* Show a **balance of tech & people skills**.
* Use the **STAR format** (Situation, Task, Action, Result) for behavioral answers.

---

Perfect — the additional details make your implementation even more solid. Now, here's a refined and complete set of answers for the **Project Assignment Review** section, tailored specifically to your implementation choices:

---

### 🔹 **Q1. What was the goal of your assignment?**

**Answer:**

> The goal was to design a responsive e-commerce web application using Angular with static JSON data, showcasing my ability to structure a scalable, modular front-end application. I aimed to demonstrate key architectural principles, state management, reusability through shared components, and data persistence using IndexedDB — all without relying on a live backend or third-party services. The focus was also on clean code practices, component reuse, and preparing the foundation for future integration of real-time APIs and external services.

---

### 🔹 **Q2. What trade-offs did you make and why?**

**Answer:**

> Due to time constraints, I chose to manage application state using Angular’s built-in services with **RxJS Observables**, instead of implementing a more complex solution like **NgRx**. This kept the app lightweight and still reactive. I also didn’t integrate a real **payment gateway** or live API, focusing instead on static JSON and IndexedDB for cart persistence, which limited dynamic interactions but allowed me to build a functional and offline-capable app. These trade-offs helped me deliver a complete, well-structured application on time.

---

### 🔹 **Q3. What were the most complex technical decisions?**

**Answer:**

> One of the more complex decisions was architecting the app using a **feature-module structure** while ensuring component reusability and separation of concerns. I built shared and core modules, along with reusable components and services, to ensure scalability. Another challenge was persisting cart data locally using **IndexedDB** — ensuring the right abstraction, managing asynchronous reads/writes, and syncing UI state with stored data. Also, deciding how much state to elevate into services versus keeping local was a key architectural decision.

---

### 🔹 **Q4. How did you ensure quality and timely delivery?**

**Answer:**

> I started with a **task estimation sheet**, breaking down the project into feature-based units and allocating time for each. I adopted a mobile-first, responsive design approach and modular coding practices to ensure the codebase remains maintainable. I created **shared test utility files** to reduce redundancy in unit tests and focused on reusability to speed up development. I followed naming conventions, linting, and code consistency checks throughout the process, and prioritized delivering a working, polished MVP within the given timeframe.

---

### 🔹 **Q5. Any improvements you'd make if you had more time?**

**Answer:**

> Given more time, I would:
>
> * Integrate a **mock backend** using Angular’s in-memory web API or JSON Server to simulate real API interactions.
> * Add **unit and integration tests** for all components, especially business logic around cart and filters.
> * Introduce **route guards**, **lazy loading**, and **offline caching strategies**.
> * Explore **NgRx** or **Akita** for more scalable state management.
> * Add features like **payment gateway integration**, **product recommendations**, and **order history tracking**.
> * Improve accessibility and add **keyboard navigation**, **ARIA roles**, and **semantic HTML**.

---

These refined answers demonstrate **technical depth, leadership in structuring code**, **project planning**, and **awareness of trade-offs and future improvements** — exactly what a panel looks for in a **Tech Lead or Team Lead** candidate.

----

Excellent — let’s now tackle the **Technical Depth & Architectural Thinking** section, tailored to your Angular-based e-commerce project and your readiness for a **Technical Lead / Team Lead** role.

---

### 🔹 **Q1. How would you design a scalable system?**

**Answer:**

> A scalable Angular app must be structured around **modular architecture**, lazy loading, and separation of concerns. In my project, I used:
>
> * **Feature modules** for isolated functionalities like Products, Cart, and Shared UI.
> * **Core module** for single-instance services and interceptors.
> * **Shared module** for reusable components, directives, and pipes.
> * **Lazy loading** to load modules only when needed, which improves performance.
> * I also used **IndexedDB** to persist cart state locally, ensuring scalability even offline.
>   Going forward, I’d integrate REST APIs with caching, paginate large datasets, and apply SSR (Angular Universal) for performance.

---

### 🔹 **Q2. What are common bottlenecks in a distributed system and how do you resolve them?**

**Answer (high-level for panel context):**

> While my current app is client-side only, I understand distributed systems face issues like **network latency, inconsistent data, service failures, and scaling bottlenecks**. In a real-world setup, to resolve this:
>
> * I’d use **caching layers** (Redis, CDN), **load balancers**, and **circuit breakers**.
> * Use **async queues** for non-blocking operations.
> * Apply **retry logic** and **graceful fallbacks** in API calls.
> * For Angular specifically, I’d use **RxJS with retryWhen**, and **interceptors** for centralized error handling and token refresh.

---

### 🔹 **Q3. How do you choose between different tech stacks?**

**Answer:**

> I evaluate tech stacks based on:
>
> * **Project requirements** (e.g., real-time needs → WebSocket or SignalR),
> * **Team expertise** and maintainability,
> * **Ecosystem maturity** and community support,
> * **Performance benchmarks**.
>   For this app, Angular was ideal due to its enterprise-grade features, TypeScript safety, CLI tooling, and modularity. If I needed a fast MVP with SSR, I might have picked Next.js. For mobile-first projects, React Native or Flutter would be more suitable.

---

### 🔹 **Q4. How do you evaluate performance and optimize?**

**Answer:**

> I start by profiling with **Chrome DevTools** and Angular’s built-in **performance tools**. In my app, I:
>
> * Used **trackBy** with `*ngFor` to reduce DOM updates,
> * Avoided unnecessary change detection using **OnPush strategy**,
> * Lazy loaded routes,
> * Compressed image assets,
> * Minified bundle size using **Angular CLI build optimizations**.
>   For larger apps, I’d also apply **preloading strategies**, **code splitting**, and **tree shaking** to improve initial load times.

---

Great! Now let’s move on to the **Leadership & Team Management** section — this is where the panel will assess your **people management, mentorship, conflict resolution**, and ability to guide a team through both delivery and growth.

Here are polished responses based on your context as a software developer moving into a **Tech Lead / Team Lead** role:

---

### 🔹 **Q1. How do you handle conflicts within a team?**

**Answer:**

> I believe in addressing conflicts early through open, neutral communication. My approach is to:
>
> * First, **understand both perspectives** without judgment,
> * Focus on **common goals** — like delivery timelines, code quality, or user needs,
> * Encourage a solution-oriented mindset where both parties can propose options,
> * If needed, escalate transparently to higher stakeholders.
>   I try to **avoid blame culture**, and instead promote **retrospectives** to reflect on root causes constructively.

---

### 🔹 **Q2. How do you manage underperforming team members?**

**Answer:**

> First, I try to identify the **root cause** — whether it's lack of clarity, skill gaps, motivation, or personal issues. I then:
>
> * Set **clear, achievable goals** and expectations,
> * Provide **mentoring or pairing opportunities**,
> * Give **regular feedback**, not just during review cycles,
> * Track progress and recognize even small improvements.
>   If performance doesn’t improve, I collaborate with HR or senior leadership for structured improvement plans. My intent is always to **coach first, not punish**.

---

### 🔹 **Q3. How do you mentor junior developers?**

**Answer:**

> I focus on **teaching by doing** — through code reviews, pair programming, and small design discussions. I:
>
> * Encourage juniors to **ask questions freely** and contribute early,
> * Assign **incrementally challenging tasks** to build confidence,
> * Give feedback that’s **specific, positive, and constructive**,
> * Help them understand the **“why” behind design choices**.
>   I also recommend **learning resources** and encourage them to demo what they’ve learned — promoting both learning and ownership.

---

### 🔹 **Q4. How would you delegate vs. do it yourself?**

**Answer:**

> As a lead, I believe delegation is key to building trust and scalability. I:
>
> * Delegate tasks based on **team members' strengths and growth areas**,
> * Ensure the task is **well-scoped and understood**,
> * Stay available for guidance but don’t micromanage.
>   I step in directly only when:
> * A critical blocker is holding the team back,
> * A key deadline is at risk,
> * Or if the task requires deep expertise I uniquely hold.
>   My goal is to enable the team, not become a bottleneck.

---

Excellent — let’s now cover **Section 4: Project & Sprint Management**.

As a future **Tech Lead / Team Lead**, the panel will want to see how well you understand **Agile processes**, how you break down and manage work, handle blockers, and lead your team toward timely delivery.

Here are strong, relevant answers for your context:

---

### 🔹 **Q1. How do you estimate tasks and manage backlogs?**

**Answer:**

> I start by breaking down features into **small, clearly defined tasks**, usually in collaboration with team members. For estimation, I use **story points or hour-based estimation**, depending on the team maturity. I factor in **complexity, dependencies, and testing needs**.
> For the e-commerce app, I created a detailed **estimation sheet** for each task, which helped me stay on track.
> I also maintain and groom the **backlog regularly**, ensure stories have proper acceptance criteria, and that priorities align with stakeholder expectations.

---

### 🔹 **Q2. How do you handle mid-sprint scope changes?**

**Answer:**

> Scope changes are common, but managing them without derailing the sprint is key. I first assess:
>
> * **Urgency** and **impact** of the new change,
> * Whether the team can **absorb it without compromising sprint goals**.
>   If not, I negotiate with the product owner or stakeholders to:
> * **Defer the change** to the next sprint, or
> * **Swap lower-priority tasks** from the current sprint.
>   Communication and transparency with both devs and stakeholders is essential to avoid burnout or delivery risks.

---

### 🔹 **Q3. How do you ensure continuous delivery without compromising quality?**

**Answer:**

> I believe in automating what can be automated and enforcing strong engineering practices. My approach includes:
>
> * Writing **modular, testable code** and encouraging **peer reviews**,
> * Creating **shared test utility files** to reduce duplication and encourage test coverage,
> * Using **CI pipelines** (or pushing for one, if not in place) to validate builds and run tests on every PR,
> * Breaking work into **deployable, incremental units**, which supports frequent, low-risk releases.
>   Also, practicing **feature toggling** allows incomplete features to be hidden while keeping the main branch clean and deployable.

---

### 🔹 **Q4. How do you track progress and manage delays?**

**Answer:**

> I track progress through **daily standups**, task boards (like Jira), and by reviewing sprint burndown.
> If delays occur, I:
>
> * Reassess and **re-prioritize tasks**,
> * Identify blockers — whether they are technical or resource-related,
> * Work with the team to **reallocate work** or escalate dependencies.
>   I keep stakeholders informed early, so expectations are managed and the team doesn’t feel last-minute pressure.

---

Great — let’s now cover **Section 6: Behavioral / Situational Questions**.

This part is key to proving that you not only *can* lead, but that you've already **acted like a leader** in real-life scenarios. Panelists often look for the **STAR method** (Situation, Task, Action, Result) in your responses.

Here are tailored answers based on your role and context:

---

### 🔹 **Q1. Tell me about a time you failed as a developer or lead — how did you handle it?**

**Answer (STAR Format):**

> **Situation:** In a past project, I underestimated the effort needed to implement a dynamic filtering system, thinking it would be simple.
> **Task:** I had committed to delivering it within a day without deeply analyzing the edge cases.
> **Action:** When integration issues and performance problems started surfacing, I immediately acknowledged the issue to my team, documented the gaps, and broke the feature into smaller tasks.
> **Result:** I completed it over the next two days with better performance and test coverage. The key takeaway was to **avoid overcommitting without proper analysis**, especially when leading a feature.

---

### 🔹 **Q2. Give an example where you resolved a team conflict.**

**Answer (STAR Format):**

> **Situation:** Two developers on my team disagreed over how to structure a shared service — one preferred a monolithic approach, the other modular.
> **Task:** My role was to mediate and ensure delivery didn’t stall.
> **Action:** I arranged a 15-minute neutral design review where both shared their ideas. We discussed trade-offs, performance, and scalability.
> **Result:** We agreed on a modular design, and the other developer contributed to making it more reusable. This created mutual respect and a stronger shared ownership of the architecture.

---

### 🔹 **Q3. Describe a situation where your team was stuck — what did you do?**

**Answer (STAR Format):**

> **Situation:** During a sprint, the team was blocked on integrating a third-party library that had poor documentation.
> **Task:** We needed to use it for product display and lazy loading, and no one had prior experience.
> **Action:** I took ownership, experimented with it after hours, and created a simple demo for the team. I also proposed an alternative approach in case integration failed.
> **Result:** The original library worked after some adjustments, and the team was able to proceed. My proactiveness helped **unblock the sprint early** and increased confidence in my leadership.

---

### 🔹 **Q4. Tell me about a time you had to give difficult feedback.**

**Answer (STAR Format):**

> **Situation:** One of my teammates was repeatedly merging untested code into the main branch.
> **Task:** I had to address this without demoralizing him.
> **Action:** I scheduled a one-on-one, highlighted the issue using a recent example, and explained the impact it had on others. Then I offered to help him write better tests and even pair-program on one.
> **Result:** He appreciated the feedback, started writing unit tests regularly, and soon became an advocate for test quality in the team.

---

Thanks! Based on your role in the **insurance squad** and your responsibilities, here's a well-structured, panel-ready answer for the common question:

---
Perfect! With those additional responsibilities, here's a **refined and complete version** of your answer that reflects your **Tech Lead–level ownership** very clearly:

---

### 🔹 **Q: What is your role in the current project?**

**Answer:**

> I’m currently working in the **insurance squad**, which has three parallel teams — iOS, Android, and OB (our web/Angular platform). I’m responsible for leading the **OB team**, which includes **three developers including myself**.
>
> My key responsibilities include:
>
> * **Gathering and discussing requirements** with clients and product owners.
> * Reviewing **technical documents, designs**, and engaging in clarification discussions with stakeholders.
> * **Estimating tasks**, breaking them down, and distributing them across the team.
> * Actively contributing to **code development**, performing **code reviews**, and managing **pull requests**.
> * Conducting **peer testing**, managing **builds**, and coordinating with the testing team for smooth QA.
> * Supporting QA by discussing any **testing challenges** they face during execution.
> * Providing **KT (knowledge transfer)** to new joiners, explaining the overall **process, codebase, and daily tasks**.
> * Presenting **daily consolidated progress updates** to clients in daily sync-up meetings.
> * Facilitating **breakout sessions** to resolve technical queries raised by team members.
> * Syncing our progress with **cross-platform teams** (iOS/Android) for alignment.
> * Demonstrating key features or components as **foundation demos** to stakeholders and client teams.

> Overall, I act as both a **technical owner and coordinator**, bridging communication between developers, QA, and clients while ensuring delivery quality and team productivity.

---
That’s already a strong list of initiatives showing **technical leadership, innovation, quality focus, and cross-team contribution** — all very important for a Tech Lead/Team Lead role.

Here’s a **polished and structured answer** to the question:

---

### 🔹 **Q: What are some additional initiatives you’ve taken beyond your regular work?**

**Answer:**

> Alongside my day-to-day responsibilities, I’ve consistently taken initiatives that contribute to team growth, innovation, and technical excellence:
>
> * **Innovation & Ideation:**
>
>   * Participated in the **Squad Sparkathon**, where I proposed an idea called **“Quick Edit”** — aimed at enhancing content update workflows. It was selected in the **top 2 ideas** and is now under **client evaluation**.
>   * Successfully **presented the idea to the client**, receiving highly positive feedback and appreciation.
>   * Participated in other innovation programs like **Hackathons**, contributing to experimental feature development and collaboration across squads.

> * **Technical Improvements:**
>
>   * Led the **migration of our Angular application from v15 to v18**, ensuring minimal impact on feature delivery.
>   * **Implemented a stricter PR merging policy** within the OB team — merges now only happen after complete **code reviews**, **build validations**, and **test phase completion**, improving overall code stability.

> * **Team & Process Contribution:**
>
>   * Supported **new team member onboarding** with structured KTs and documentation.
>   * Contributed to **internal best practices** documentation and **code quality guidelines**.
>   * Helped streamline the **peer testing checklist** to reduce QA feedback cycles.
> * **Cross-Team Collaboration:**
>
>   * Coordinated with iOS and Android teams to **sync platform behavior**, ensuring consistent implementation across all three platforms.
>   * Took initiative to lead or support **foundation demos** and **feature walkthroughs** for other squads.

---

## **"Tell me about yourself"**

---

✅ **Professional Background:**

* I have **12 years of experience** as a **software developer**.
* Completed **5 years at Nagarro**, working across **three major accounts** – currently with **Nedbank**, and previously with **CSX**.
* Worked in multiple **domains** including:

  * **Banking**
  * **E-commerce web applications**
  * **Healthcare**
  * **Resource management**

✅ **Technical Skills:**

* Strong expertise in **frontend development** using:

  * **Angular** (core framework I’ve worked with)
  * **React**
* Recently started working with **React Native** for mobile app development.
* Currently involved in **GitHub Copilot training** to enhance productivity and support AI-assisted development.

✅ **Team & Leadership Experience:**

* Leading the **OB (Angular) web team** in the insurance squad.
* Handling:

  * **Requirement gathering**
  * **Task estimation and distribution**
  * **Code development, PR/code reviews**
  * **Peer testing and build coordination**
  * **Client communication** and daily sync-ups
  * **KT sessions** and team onboarding

✅ **Initiatives & Achievements:**

* Presented **“Quick Edit”** innovation in **Squad Sparkathon** – selected in **Top 2 ideas**, presented to client, and awarded.
* Participated in various **hackathons** and innovation programs.
* Led **Angular migration from v15 to v18**.
* Implemented **stricter PR process** to improve code quality and build/test compliance.

✅ **Personal Interests:**

* Hobbies include **swimming**, **singing**, **traveling**, and watching **motivational videos and stories**.

---


## Nagarro CARING - meaning

- Client-centric
- Agile
- Responsible
- Intelligent
- Non-hierarchical
- Global

---

## 💬 **Q: Are you replaceable with AI? If not, why?**

This is a **very likely and smart interview question**, especially for a **tech lead** role in today’s AI-driven world.

Here's how you can **confidently answer**:

### ✅ Suggested Answer:

> *"AI is a powerful assistant, but not a replacement — especially for leadership roles like mine. As a tech lead, I don’t just write code. I drive architecture decisions, mentor team members, handle ambiguity in requirements, coordinate with multiple stakeholders, and ensure delivery aligns with business goals. These tasks require context, judgment, empathy, and strategic thinking — things AI still can’t replicate."*

---

## 🔍 Expand with These Key Points:

### 🔹 1. **Human Judgment & Decision Making**

* AI lacks **intuition** and real-world **contextual understanding**.
* Example: Choosing between tech trade-offs (like performance vs scalability) requires business sense.

---

### 🔹 2. **People & Team Management**

* Leading a team involves:

  * Emotional intelligence
  * Mentorship
  * Conflict resolution
  * Motivation
* AI can't replace **human connection**, coaching, and leadership.

---

### 🔹 3. **Client Communication & Collaboration**

* Understanding vague or evolving requirements from clients.
* Negotiating timelines, prioritizing tasks — needs human interaction skills.
* AI may assist, but cannot fully understand **tone, emotion, or political nuance** in stakeholder conversations.

---

### 🔹 4. **System Design & Ownership**

* Architecture decisions involve long-term vision, scalability, and integrations.
* AI can suggest patterns, but **vision, accountability, and ownership** must come from humans.

---

### 🔹 5. **Innovation & Business Alignment**

* Many of my contributions are not just technical (e.g., Sparkathon ideas, Angular upgrade planning, process improvements).
* AI can assist in execution, but innovation and initiative come from people.

---

## 🎯 Final Impact Statement:

> *"I believe AI can boost my productivity — make me 2x or 3x faster — but not replace the core of what I do. In fact, as a tech lead, I see my role evolving: leveraging AI more effectively to lead better, deliver faster, and create more impact."*

---

# **must-have qualities** for a **Senior Team Lead**

Here are the **must-have qualities** for a **Senior Team Lead**, especially in a software engineering environment like Nagarro or any tech-driven company:

---

## 🧠 **1. Technical Expertise & Vision**

* Deep understanding of architecture, design patterns, scalability, and performance.
* Ability to **make high-level technical decisions** (e.g., tech stack choice, modularization, micro frontends, state management).
* Stays up to date with **emerging technologies** (like AI integration, microservices, cloud-native development).

---

## 🤝 **2. People Leadership**

* Mentor and grow developers at different experience levels.
* Handle **conflicts, feedback**, and team morale with empathy.
* Lead **by influence**, not just authority — earn trust.

---

## 📊 **3. Project & Delivery Ownership**

* Full responsibility for **end-to-end delivery** — from requirement gathering to post-release support.
* Create and track project **estimations, timelines**, and **resource planning**.
* Proactively handle **risks**, scope changes, and cross-team dependencies.

---

## 🗣️ **4. Communication & Stakeholder Management**

* Present clearly to clients, management, and non-tech stakeholders.
* Drive **requirement discussions, demos**, and align team goals with **business needs**.
* Build strong **collaboration bridges** across squads (e.g., iOS, Android, OB teams).

---

## ⚙️ **5. Process & Quality Ownership**

* Establish and enforce processes like:

  * PR reviews
  * CI/CD pipelines
  * Unit testing and shared test utilities
* Promote engineering best practices and **quality-first culture**.

---

## 💡 **6. Innovation & Initiative**

* Encourage innovation (e.g., Sparkathon, POCs).
* Suggest process improvements, tech upgrades (like Angular v15 → v18 migration).
* Help the team and product **stay ahead** of the curve.

---

## 🧭 **7. Strategic Thinking**

* Think beyond sprint or feature — see the **product roadmap**, scalability needs, long-term goals.
* Balance **technical debt vs delivery pressure** wisely.

---

## 🔄 **8. Adaptability & Learning Mindset**

* Open to change, feedback, and **continuous improvement**.
* Adapts to new tools (e.g., GitHub Copilot, AI tools) and promotes them across the team.

---

### 🎯 Final Thought:

A **Senior Team Lead** acts as a **bridge between engineering execution and strategic business value** — someone who delivers, leads, mentors, and innovates while owning delivery from all angles.

-------

# ✅ **“Explain your current project”**

---

> “I’m currently working on a banking-insurance domain project for **Nedbank**, one of South Africa’s largest and most trusted financial institutions. The project involves developing and maintaining the **Insurance module** within the bank’s digital platform.”

---

### 🔍 **Project Scope & Overview**

> "The insurance module allows customers to explore, purchase, and manage different insurance products such as:

* **Life Cover**
* **Personal Accident**
* **Funeral Policies**
* **Vehicle Insurance**
* **Household Insurance**
* **Legal Expense Cover**
* **Policy and Claim Management** features"

---

### 👥 **Team & Responsibilities**

> "The project is divided into three parallel teams – **iOS**, **Android**, and **OB (Omni-Channel Web)**. I lead the **OB team** consisting of 3 members, and we are responsible for building and maintaining the web interface using **Angular v18**, **HTML**, and **SCSS**, while the backend is powered by **Java-based services**."

---

### 💼 **Current Implementation Focus**

> "Currently, we are working on a key enhancement under the **Funeral Policy Management**—specifically a new **Policy Admin workflow** that allows customers to modify, cancel, or update existing funeral policy details, nominees, and beneficiaries."

---

## 🛠️ **Architecture & Tools of current project**

> “Our web application is designed with a scalable and maintainable architecture, using the following principles and tools:”

---

#### 🧱 **1. Application Architecture**

* **MVVM (Model-View-ViewModel)** architecture forms the core of the application structure.
* Follows a **modular and component-based approach**, where code is split into domain-specific modules, shared libraries, and feature modules.
* Utilizes **shared and reusable components**, pipes, and directives for UI consistency and maintainability.

---

#### 🔄 **2. State Management**

* Uses **ViewModels and Observables** to manage reactive UI states.
* **Service-based data flow** helps in maintaining single responsibility and separation of concerns.
* **IndexedDB and Service Workers** are used for caching and offline support where needed (e.g., for persisting cart data or form states).

---

#### 🧠 **3. Design Patterns & Best Practices**

* Applied **Angular design patterns** like Singleton Services, Smart-Dumb Component pattern, Resolver Guards, and Dependency Injection extensively.
* Lazy loading and route-level guards are implemented for performance and security.

---

#### 🧪 **4. Testing & Quality**

* Developed **unit test cases using Jasmine and Karma**.
* Created **shared test utility files** to eliminate redundancy and ensure better code coverage.
* Peer testing and PR-based review process ensure high-quality, well-reviewed code.

---

#### 🚀 **5. DevOps & Collaboration**

* Working in **Agile methodology** with 2-week sprints, daily stand-ups, grooming, and retrospectives.
* Regular client sync-ups and **breakaway sessions for technical clarifications and demonstrations**.
* **CI/CD pipeline** set up with **Azure DevOps**, ensuring automated builds and deployments.
* **Version control and branching strategies** are followed using **Git**.

---

# What are the current challenges in your project?

---

### 🔧 **1. Legacy Code and Tech Debt**

> "Some parts of our OB module still rely on older patterns or components that don’t align with the latest Angular best practices. Cleaning up and migrating such legacy code while maintaining delivery timelines is a challenge."

---

### ⏱️ **2. Time & Resource Constraints**

> "We are a small team handling the OB module with high expectations on delivery and quality. Managing task estimation, review cycles, and client commitments within tight deadlines requires effective prioritization."

---

### 🧪 **3. Testing Scope & QA Dependencies**

> "Sometimes, QA faces difficulty testing features due to lack of data or understanding of edge cases. I bridge that gap by doing peer testing and guiding the QA team — but we still need better mock data and test coverage."

---

### 🔄 **4. Cross-Team Dependencies**

> "The OB module is tightly coupled with iOS and Android workstreams. Any changes or delays from their side can impact our flow and coordination is key."

---

### 🌐 **5. Lack of Live APIs in Dev Phase**

> "In many cases, we develop using mock data due to unavailability of backend APIs during early sprints. That introduces rework when APIs become available later and slightly differ from the mocks."

### 🔁 6. **Frequent Changes in UI/UX Designs**

> "Designs get updated frequently, sometimes during or after development. This leads to rework, impacts timelines, and creates inconsistencies across platforms if not handled carefully."

---
