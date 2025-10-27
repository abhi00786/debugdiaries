# 🧰 Debug Diary Entry — Node.js To-Do App (Connection Refused on EC2)

**Date:** October 27, 2025  
**Issue:** `ERR_CONNECTION_REFUSED` when accessing `http://3.108.5.154:8000/todo`

---

## 🧩 Problem Summary
When trying to access the Node.js To-Do application running inside a Docker container on an AWS EC2 instance, the browser displayed:

```
This site can’t be reached
3.108.5.154 refused to connect.
ERR_CONNECTION_REFUSED
```

Even though the EC2 security group allowed inbound traffic on ports **22**, **80**, and **8000**, the connection kept getting refused.

---

## 🔍 Investigation

### 1️⃣ Checked container ports
```bash
docker ps
```
Output:
```
1d0213c25bb4   node-app    "node app.js"    Up About a minute   0.0.0.0:800->8080/tcp, [::]:8000->8080/tcp
```
> Container was mapping host port `8000` to **container port 8080**, but the application might not be listening on 8080.

---

### 2️⃣ Checked container logs
```bash
docker logs node-app
```
Output:
```
Todolist running on http://0.0.0.0:8000
```
> ✅ The Node.js app was actually running on **port 8000 inside the container**, not 8080.

---

## 🧠 Root Cause
Port mismatch between:
- **Docker run port mapping (8080)**  
- **Application’s actual listening port (8000)**  

Hence, requests to `3.108.5.154:8000` were never reaching the app.

---

## 🧰 Solution

### 1️⃣ Stop and remove existing container
```bash
docker stop node-app
docker rm node-app
```

### 2️⃣ Re-run with correct port mapping
```bash
docker run -d -p 8000:8000 --name node-app node-app
```

### 3️⃣ Verify running container
```bash
docker ps
```
Expected:
```
0.0.0.0:8000->8000/tcp
```

### 4️⃣ Test in browser
Open:
```
http://3.108.5.154:8000/todo
```

✅ **App loaded successfully!**

---

## 💡 Optional Improvement
Update the `Dockerfile` for clarity:
```dockerfile
EXPOSE 8000
```
Then rebuild the image:
```bash
docker build -t node-app .
```

---

## 🏁 Key Learnings
- Always verify which port your app listens on inside the container.  
- Use `docker logs` to confirm runtime behavior.  
- Keep EC2 Security Groups, container ports, and app ports consistent.  
