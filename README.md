# 🌐 API Cheat Sheet for Frontend Developers (Fetch + Axios)

> A complete, beginner-to-intermediate-level guide to working with APIs using Fetch and Axios, including file uploads, interceptors, custom hooks, and reusable request logic.

---

## 🔹 1. What is an API?
- API (Application Programming Interface) allows communication between frontend (client) and backend (server).
- Used to perform operations like GET, POST, PUT, DELETE via HTTP.

---

## 📦 Fetch API

### ✅ Basic Fetch GET Request
```js
fetch("https://api.example.com/data")
  .then(res => res.json())
  .then(data => console.log(data))
  .catch(error => console.error("Error:", error));
```

### ✅ With async/await
```js
try {
  const res = await fetch("https://api.example.com/data");
  if (!res.ok) throw new Error("Request failed");
  const data = await res.json();
} catch (error) {
  console.error("Error:", error);
}
```

### ✅ POST Request (JSON)
```js
fetch("https://api.example.com/post", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    Authorization: `Bearer ${token}`
  },
  body: JSON.stringify({ name: "John" })
});
```

### ✅ Upload File with FormData
```js
const formData = new FormData();
formData.append("file", file);
formData.append("name", "John");

fetch("https://api.example.com/upload", {
  method: "POST",
  body: formData // No need to set Content-Type
});
```

---

## ⚙️ Axios

### ✅ Installation
```bash
npm install axios
```

### ✅ Basic GET Request
```js
import axios from "axios";
const res = await axios.get("https://api.example.com/data");
console.log(res.data);
```

### ✅ POST Request with Headers
```js
const res = await axios.post("https://api.example.com/post", {
  name: "John"
}, {
  headers: {
    Authorization: `Bearer ${token}`
  }
});
```

### ✅ Query Params
```js
axios.get("https://api.example.com/users", {
  params: {
    search: "john",
    limit: 10
  }
});
```

### ✅ Axios Instance
```js
const api = axios.create({
  baseURL: "https://api.example.com",
  headers: {
    "Content-Type": "application/json"
  }
});

api.get("/products");
```

---

## 🔁 Interceptors

### ✅ Request Interceptor for Token
```js
api.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem("token");
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => Promise.reject(error)
);
```

### ✅ Response Interceptor for Error Handling
```js
api.interceptors.response.use(
  (res) => res,
  (error) => {
    if (error.response?.status === 401) {
      console.error("Unauthorized - Token expired");
    }
    return Promise.reject(error);
  }
);
```

---

## 🪝 useCallback with API

```js
const fetchData = useCallback(async () => {
  try {
    const res = await api.get("/data");
    setData(res.data);
  } catch (e) {
    console.error(e);
  }
}, []);

useEffect(() => {
  fetchData();
}, [fetchData]);
```

---

## 🔁 Reusable Fetch Function
```js
async function apiFetch(url, method = "GET", body = null, token = null) {
  const config = {
    method,
    headers: {
      "Content-Type": "application/json",
      ...(token && { Authorization: `Bearer ${token}` })
    },
    ...(body && { body: JSON.stringify(body) })
  };
  const res = await fetch(url, config);
  if (!res.ok) throw new Error("Request failed");
  return await res.json();
}
```

---

## 🔁 Reusable Axios Function
```js
const api = axios.create({ baseURL: "https://api.example.com" });

async function axiosRequest(method, url, data = null, token = null) {
  try {
    const config = {
      method,
      url,
      headers: {
        "Content-Type": "application/json",
        ...(token && { Authorization: `Bearer ${token}` })
      },
      ...(data && { data })
    };
    const res = await api(config);
    return res.data;
  } catch (err) {
    console.error("Axios error:", err);
    throw err;
  }
}
```

---

## ✅ Best Practices
- Always handle errors with `try/catch`
- Never set Content-Type for `FormData` manually
- Use interceptors for tokens and error logic
- Abstract repetitive logic with reusable functions
- Use `useCallback` to prevent unwanted re-renders

---

> 📌 **Tip**: Revisit this daily and try real projects or mock APIs to strengthen understanding.

---

## ❓ API Interview Q&A (Top 20 Questions with Answers)

### 1. What is the difference between Fetch and Axios?
**Answer:**  
Fetch is a native browser API for making HTTP requests. Axios is a promise-based library that simplifies requests and provides additional features like automatic JSON parsing, request/response interceptors, and cancellation support.

---

### 2. What does `fetch()` return?
**Answer:**  
A Promise that resolves to a `Response` object. You need to manually parse the data using `.json()`, `.text()`, etc.

---

### 3. How do you send a POST request using Fetch?
**Answer:**  
Use `fetch(url, { method: "POST", headers: {...}, body: JSON.stringify(data) })`.

---

### 4. What is FormData and when do you use it?
**Answer:**  
`FormData` is used to construct a set of key/value pairs to send files and data. It's useful for multipart form uploads.

---

### 5. Why should you avoid manually setting `Content-Type` for FormData?
**Answer:**  
The browser sets the proper `Content-Type` and `boundary` automatically. Setting it manually can break the request.

---

### 6. How does Axios simplify API requests?
**Answer:**  
Axios automatically parses JSON responses, handles errors better, supports interceptors, and allows creating pre-configured instances.

---

### 7. What are Axios interceptors?
**Answer:**  
Functions that run before a request is sent or after a response is received. They're used for logging, token injection, global error handling.

---

### 8. What is the purpose of `axios.create()`?
**Answer:**  
It allows you to create an Axios instance with a base URL, headers, and configuration to avoid repeating setup code.

---

### 9. How do you send query parameters in Axios?
**Answer:**  
By using the `params` option: `axios.get(url, { params: { key: value } })`.

---

### 10. What is `useCallback` used for in API functions?
**Answer:**  
`useCallback` memoizes the function to prevent it from being recreated on every render, especially when passed to child components or used in `useEffect`.

---

### 11. How do you handle API errors properly?
**Answer:**  
Always use `try/catch` blocks in async functions or `.catch()` for Promises.

---

### 12. How do you send an authenticated request?
**Answer:**  
Include the token in the `Authorization` header: `"Authorization": "Bearer token"`.

---

### 13. What is a reusable API function?
**Answer:**  
A centralized function that accepts URL, method, body, and token, making API calls consistent and easier to maintain.

---

### 14. Can Axios send files?
**Answer:**  
Yes, use `FormData` and pass it as the `data` in `axios.post()`. Axios handles the headers automatically.

---

### 15. What happens if you manually set `Content-Type` for multipart data?
**Answer:**  
You must define the correct boundary. Failing to do so will result in a broken request or server rejection.

---

### 16. How can you retry a failed request?
**Answer:**  
Use a `refetch` function inside a custom hook or a retry mechanism like Axios interceptors or manual retry with setTimeout.

---

### 17. What’s the benefit of storing Axios instance in a separate file?
**Answer:**  
It improves code reusability, readability, and maintainability. All requests share a consistent base configuration.

---

### 18. What is the `.ok` property in Fetch?
**Answer:**  
It indicates if the response status code is between 200–299. Use it to check success before calling `.json()`.

---

### 19. How do you send data in Axios POST?
**Answer:**  
Pass the data as the second argument: `axios.post(url, data, config)`.

---

### 20. Can Axios handle HTML response?
**Answer:**  
Yes, Axios can parse and return any type of response — you can access it via `response.data`.

---
