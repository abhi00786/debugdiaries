# 🐛 Issue: MySQL Workbench Couldn’t Connect to AWS RDS MySQL Instance

## ❓ Problem Statement
While attempting to connect **MySQL Workbench** to an **AWS RDS MySQL instance**, the connection failed with the error:

> “Cannot connect to database server”

Initial PowerShell test (`Test-NetConnection database-1.c5ysmgoiy895.ap-south-1.rds.amazonaws.com -Port 3306`) also failed with:
> “Name resolution failed”

---

## 🧪 Troubleshooting Journey
1. **Checked RDS Status** — Instance was *Available* with MySQL Community engine running.  
2. **Verified Connectivity** — Endpoint and port were correct (`3306`).  
3. **Tested Public Access** — RDS instance was initially *not publicly accessible*, later modified to `Publicly accessible: Yes`.  
4. **Investigated Security Groups** — Found inbound rule allowing access only from another security group (`sg-041da642606c813ab`), meaning local IP traffic was blocked.  
5. **Added Custom Inbound Rule** — Created a rule to allow MySQL/Aurora traffic from the current public IP (`x.x.x.x/32`) on port `3306`.  
6. **Retested Connection** — After the rule change, `Test-NetConnection` succeeded and MySQL Workbench connected successfully.

---

## 💡 Learnings
- **DNS resolution failure** in PowerShell often means the RDS endpoint isn’t publicly reachable (private subnet or SG restriction).  
- **Public accessibility** must be enabled for connections from outside AWS.  
- **Security group rules** should allow TCP port `3306` from the client’s IP address.  
- Use `Test-NetConnection` (Windows) or `telnet`/`nc` (Linux) to isolate **network vs credential** issues.

---

## ✅ Final Outcome
After enabling public access and updating the security group, the RDS MySQL instance became reachable from MySQL Workbench. The issue was confirmed to be **network configuration**, not credentials or database errors.
