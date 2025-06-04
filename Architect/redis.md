# 🚀 What is Redis?

**Redis** (REmote DIctionary Server) is an **open-source**, in-memory **key-value store** known for being:

* Extremely **fast** (sub-millisecond latency)
* Used as a **database**, **cache**, and **message broker**
* Often used in **high-performance** applications

---

## 🎯 Why Use Redis? (Purpose)

| ✅ Use Case                          | 💡 Why It Helps                                                            |
| ----------------------------------- | -------------------------------------------------------------------------- |
| 🔥 **Caching**                      | Speeds up responses by storing frequently-used data in memory              |
| 📋 **Session Store**                | Stores user session data for quick retrieval in login systems              |
| 📮 **Message Queue**                | Used as a lightweight pub/sub or job queue system (e.g., background tasks) |
| 📌 **Rate Limiting**                | Track user request counts for APIs in real-time                            |
| 🔍 **Leaderboard / Realtime Stats** | Fast operations on counters, scores, and sets                              |

---

## 🧠 What Problems Does Redis Solve?

### 🟥 Without Redis:

* Every request hits your database, increasing **load**, **latency**, and **cost**.
* Slower user experience.
* Scalability is hard as traffic grows.

### 🟩 With Redis:

* Store results of common DB queries in memory — return in **<1 ms**.
* Reduce database calls by **80–90%** in many systems.
* Smooth **scaling** and improved **user experience**.

---

## 🧪 Example: Redis as Cache in Node.js

### 1. Install Redis and Redis client

```bash
npm install redis
```

Also make sure Redis is running locally:

```bash
brew install redis  # (Mac)
sudo apt install redis-server  # (Linux)
```

---

### 2. Node.js Example – Using Redis for Caching

```js
const redis = require("redis");
const express = require("express");
const axios = require("axios");

const client = redis.createClient(); // default: localhost:6379
client.connect(); // Required for Redis v4+

const app = express();
const PORT = 3000;

// Middleware to check cache
app.get("/data/:id", async (req, res) => {
  const { id } = req.params;

  // Check cache
  const cached = await client.get(id);
  if (cached) {
    return res.json({ source: "cache", data: JSON.parse(cached) });
  }

  // Simulate API call
  const data = await axios.get(`https://jsonplaceholder.typicode.com/posts/${id}`);
  await client.setEx(id, 60, JSON.stringify(data.data)); // cache for 60 seconds

  res.json({ source: "api", data: data.data });
});

app.listen(PORT, () => console.log(`Server running on http://localhost:${PORT}`));
```

🧠 This means:

* The first request will fetch from API and cache it in Redis.
* Next requests (within 60 seconds) return **immediately from Redis**.

---

## 🧰 Common Redis Commands

| Command                 | Purpose                    |
| ----------------------- | -------------------------- |
| `SET key val`           | Save a value               |
| `GET key`               | Get a value                |
| `DEL key`               | Delete a key               |
| `EXPIRE key s`          | Set TTL in seconds         |
| `INCR key`              | Increment a counter        |
| `LPUSH`/`BRPOP`         | Queue-based job processing |
| `PUBLISH` / `SUBSCRIBE` | Pub/Sub messaging          |

---

## 🔄 Redis vs Database vs Memory

| Feature     | Redis                    | Traditional DB    | In-app memory     |
| ----------- | ------------------------ | ----------------- | ----------------- |
| Speed       | ⚡ Ultra fast             | 🐢 Slower         | ⚡ Fast            |
| Persistence | Optional                 | ✅ Yes             | ❌ No              |
| Shareable   | ✅ Between apps           | ✅                 | ❌ Only inside app |
| Best for    | Cache, counters, pub/sub | Core data storage | Temporary data    |

---

## Complete **example of Pub/Sub using Redis and Node.js** on **Windows**:

---

## ✅ What You’ll Need

1. **Redis installed** on your Windows system
2. **Node.js** installed
3. A folder with two scripts:

   * `publisher.js` → sends messages
   * `subscriber.js` → listens to messages

---

## 📦 Step 1: Install Redis on Windows

Redis doesn’t officially support Windows anymore, but you can:

### 🟢 Easiest: Use **Memurai** (Redis-compatible for Windows):

* Download from: [https://www.memurai.com/get-memurai](https://www.memurai.com/get-memurai)
* Install and run — it behaves just like Redis.

OR

### 🐳 If you have **WSL or Docker**, you can install Redis there:

```bash
sudo apt install redis-server
redis-server
```

---

## 📦 Step 2: Initialize Node Project

```bash
mkdir redis-pubsub
cd redis-pubsub
npm init -y
npm install redis
```

---

## 🧪 Step 3: Create the Redis Pub/Sub Scripts

---

### 🔹 `publisher.js`

```js
const { createClient } = require('redis');

async function publish() {
  const publisher = createClient();

  await publisher.connect();

  const message = `New order placed at ${new Date().toLocaleTimeString()}`;
  await publisher.publish('notifications', message);

  console.log(`📤 Sent message: ${message}`);

  await publisher.disconnect();
}

publish();
```

### 🔹 `subscriber.js`

```js
const { createClient } = require('redis');

async function subscribe() {
  const subscriber = createClient();

  await subscriber.connect();

  await subscriber.subscribe('notifications', (message) => {
    console.log(`🔔 Received message: ${message}`);
  });

  console.log('📡 Listening on channel: notifications');
}

subscribe();
```

---

## 🚀 Step 4: Run the Example

1. Open **two terminals**.

2. In terminal 1:

```bash
node subscriber.js
```

3. In terminal 2:

```bash
node publisher.js
```

🟢 You'll see `subscriber.js` print the message sent by `publisher.js`.

---

## ✅ Use Cases of Redis Pub/Sub

* Real-time notifications (chat, alerts, emails)
* Inter-process communication (IPC)
* Microservice messaging (lightweight alternative to Kafka/RabbitMQ)
