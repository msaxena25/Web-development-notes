#  `useEffect` is said to handle “outside React things”

### 🌐 What counts as “outside” React?
React’s main job is to render UI based on state and props. But real-world apps need to do more than just render:
- Fetch data from APIs
- Set up timers or intervals
- Listen to browser events (like scroll or resize)
- Interact with local storage or cookies
- Subscribe to external services (like WebSockets)

These are **imperative tasks** that don’t belong in React’s declarative rendering flow. That’s where `useEffect` steps in.

---