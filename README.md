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
