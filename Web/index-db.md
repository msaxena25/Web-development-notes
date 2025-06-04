# **📌 IndexedDB: Basics & Usage**  

IndexedDB is a **client-side NoSQL database** in the browser that allows storing large amounts of structured data. It works asynchronously and provides transactions for data consistency.  

---

## **🔹 Key Features of IndexedDB**
✅ **Key-value storage** – Data is stored in object stores like NoSQL.  
✅ **Asynchronous API** – Non-blocking operations.  
✅ **Indexes for fast lookups** – Similar to relational databases.  
✅ **Works offline** – Data persists in the browser.  
✅ **Supports transactions** – Ensures data integrity.  

---

## **📌 How to Use IndexedDB**

### **1️⃣ Open/Create a Database**
```javascript
let db;
const request = indexedDB.open("MyDatabase", 1);

request.onsuccess = function (event) {
  db = event.target.result;
  console.log("Database opened successfully", db);
};

request.onerror = function (event) {
  console.error("Database error:", event.target.error);
};

// Runs when database is created or version is updated
request.onupgradeneeded = function (event) {
  db = event.target.result;
  if (!db.objectStoreNames.contains("users")) {
    db.createObjectStore("users", { keyPath: "id", autoIncrement: true });
  }
};
```

---

### **2️⃣ Add Data to Object Store**
```javascript
function addUser(user) {
  const transaction = db.transaction(["users"], "readwrite");
  const store = transaction.objectStore("users");
  const request = store.add(user);

  request.onsuccess = () => console.log("User added:", request.result);
  request.onerror = () => console.error("Error adding user:", request.error);
}

addUser({ id: 1, name: "Alice", age: 25 });
```

---

### **3️⃣ Retrieve Data**
```javascript
function getUser(id) {
  const transaction = db.transaction(["users"], "readonly");
  const store = transaction.objectStore("users");
  const request = store.get(id);

  request.onsuccess = () => console.log("User found:", request.result);
  request.onerror = () => console.error("Error fetching user:", request.error);
}

getUser(1);
```

---

### **4️⃣ Update Data**
```javascript
function updateUser(id, updatedData) {
  const transaction = db.transaction(["users"], "readwrite");
  const store = transaction.objectStore("users");
  const request = store.get(id);

  request.onsuccess = function () {
    const data = request.result;
    Object.assign(data, updatedData);
    store.put(data);
  };
}

updateUser(1, { name: "Alice Smith" });
```

---

### **5️⃣ Delete Data**
```javascript
function deleteUser(id) {
  const transaction = db.transaction(["users"], "readwrite");
  const store = transaction.objectStore("users");
  const request = store.delete(id);

  request.onsuccess = () => console.log("User deleted");
}

deleteUser(1);
```

---

### **6️⃣ Get All Data**
```javascript
function getAllUsers() {
  const transaction = db.transaction(["users"], "readonly");
  const store = transaction.objectStore("users");
  const request = store.getAll();

  request.onsuccess = () => console.log("All Users:", request.result);
}

getAllUsers();
```

---

## **🔥 Best Practices**
✔ **Always check for IndexedDB support:**  
```javascript
if (!window.indexedDB) {
  console.log("Your browser doesn't support IndexedDB");
}
```
✔ **Use transactions properly** – Always handle errors.  
✔ **Index frequently queried fields** – Improves performance.  
✔ **Use Promises or Async/Await** – Wrap IndexedDB calls for cleaner code.  

---
