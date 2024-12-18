# Troubleshooting Neo4j Desktop Ports

To ensure no other Neo4j databases are running simultaneously on your Mac, you can follow these steps:


## **1. Check for Running Neo4j Instances**

Use the terminal to look for running Neo4j processes:

```bash
ps aux | grep -i neo4j
```

- This command will list all processes that include the word "neo4j."
- If you see a Neo4j process running, note its **Process ID (PID)**.

---

## **2. Stop Any Running Neo4j Instances**

If Neo4j processes are running and shouldn't be, you can stop them:
- Use the `kill` command to stop the specific process:

```bash
kill -9 PID
```

Replace `PID` with the actual process ID from the `ps aux` command.

---

## **3. Check Active Ports**

Neo4j services use specific ports (e.g., 7687 for Bolt, 7474 for HTTP). Check if these ports are in use:

```bash
lsof -i :7687
lsof -i :7474
```

- If another process is using these ports, identify it from the output.

Stop any processes using these ports (if they’re related to another Neo4j instance):

```bash
kill -9 PID
```

---

## **4. Manage Neo4j Services on macOS**

If Neo4j was installed as a service (via Homebrew or similar), you can stop it using:

```bash
brew services stop neo4j
```

This ensures Neo4j is not running as a background service.

---

## **5. Use Neo4j Desktop**

Neo4j Desktop manages databases independently. To ensure only one database is running:

1. Open **Neo4j Desktop**.
2. In the project interface, check the status of your databases.
3. Stop all databases except the one you intend to start:
   - Click the **Stop** button next to each running database.

---

## **6. Optional: Restart Your Mac**

If you're uncertain about processes or port usage, restarting your Mac will terminate all running processes, ensuring no Neo4j instances are active when you start your database.

---

## **7. Start the Desired Database**

Once you’ve ensured no other Neo4j processes are running, you can start the desired database:

- Open **Neo4j Desktop**.
- Select the database you want to start and click **Start**.

