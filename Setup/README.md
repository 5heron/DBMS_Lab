
---

# Oracle XE in Docker

This guide shows how to set up Oracle XE in Docker, run PL/SQL scripts, and interact with the container.

### Step 1: Install Docker
Follow the [Docker installation guide](https://docs.docker.com/get-docker/) for your OS.

### Step 2: Pull Oracle XE Image
Run the following command:
```bash
docker pull gvenzl/oracle-xe
```
(For more details, visit the [gvenzl/oracle-xe Docker Hub page](https://hub.docker.com/r/gvenzl/oracle-xe).)

### Step 3: Run the Container
Run Oracle XE in a container with this command:
```bash
docker run -d \
  -p 1521:1521 -p 8080:8080 \
  -e ORACLE_PASSWORD=your_password \
  -v /path/to/sqlscripts:/mnt/sqlscripts \
  --name oracle-xe \
  gvenzl/oracle-xe
```

### Step 4: Access Bash in the Container
Enter the container’s bash:
```bash
docker exec -it oracle-xe bash
```

### Step 5: Copy Files to the Container
Copy PL/SQL scripts into the container:
```bash
docker cp /path/to/script.sql oracle-xe:/mnt/script.sql
```

### Step 6: Run PL/SQL Scripts
Log into Oracle and run the script:
```bash
sqlplus sys/your_password@localhost:1521/xe as sysdba
@/mnt/script.sql
```

### Step 7: Manage the Container
- **Stop the container**:  
  ```bash
  docker stop oracle-xe
  ```
- **Start the container**:  
  ```bash
  docker start oracle-xe
  ```

---

This setup will run Oracle XE in Docker, allow you to copy PL/SQL scripts, and execute them.
