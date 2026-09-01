# Linux Troubleshooting Runbook — Day 05

## Target Service

**Service:** SSH (`ssh.service`)
**Purpose:** Remote access to the EC2 Ubuntu server
**Default Port:** TCP 22

---

## 1. Environment Basics

### Command

```bash
uname -a
```

### Observation

Ubuntu AWS kernel `7.0.0-1006-aws` running on `x86_64` architecture.

### Command

```bash
cat /etc/os-release
```

### Observation

System is running **Ubuntu 26.04 LTS (Resolute Raccoon)**.

---

## 2. Filesystem Sanity

### Command

```bash
mkdir /tmp/runbook-demo
cp /etc/hosts /tmp/runbook-demo/hosts-copy && ls -l /tmp/runbook-demo
```

### Output

```text
-rw-r--r-- 1 ubuntu ubuntu 221 Aug 31 04:01 hosts-copy
```

### Observation

Successfully created a temporary directory and copied `/etc/hosts`. File read/write operations are working normally.

Cleanup:

```bash
rm -rf /tmp/runbook-demo
```

---

## 3. CPU & Memory

### Command

```bash
ps -o pid,pcpu,pmem,comm -p 984
```

### Observation

Checked the SSH process (`sshd`) for CPU and memory usage. SSH was using very little system resources.

### Command

```bash
free -h
```

### Observation

System memory was available and there was no indication of memory pressure.

---

## 4. Disk & I/O

### Command

```bash
df -h
```

### Observation

Root filesystem:

```text
Size: 14G
Used: 2.1G
Available: 12G
Usage: 16%
```

Disk space is healthy with plenty of free capacity.

### Command

```bash
sudo du -sh /var/log
```

### Output

```text
17M /var/log
```

### Observation

System logs are using only approximately 17 MB. No excessive log growth observed.

### Command

```bash
vmstat 1 3
```

### Observation

CPU was approximately 98–100% idle, swap usage was 0, and I/O wait was 0%. No obvious CPU, memory, or I/O pressure observed.

---

## 5. Network

### Command

```bash
ss -tulpn
```

### Observation

SSH is listening on:

```text
0.0.0.0:22
[::]:22
```

This confirms SSH is listening on TCP port 22 for IPv4 and IPv6.

### Command

```bash
curl -I http://localhost:22
```

### Output

```text
curl: (1) Received HTTP/0.9 when not allowed
```

### Observation

The test reached port 22, but `curl` was speaking HTTP while port 22 expects SSH. Therefore, this protocol error does not indicate an SSH failure.

---

## 6. Logs Reviewed

### Command

```bash
sudo journalctl -u ssh -n 50
```

### Important observations

SSH successfully started and began listening on port 22.

```text
Server listening on 0.0.0.0 port 22.
Server listening on :: port 22.
```

A public-key authentication succeeded:

```text
Accepted publickey for ubuntu
```

There were also connection closures, but no clear SSH service failure.

The `invalid format` message was consistent with the earlier `curl` test against the SSH port.

---

## 7. Quick Findings

* SSH service is running normally.
* SSH is listening on TCP port 22.
* CPU usage is low and the system is mostly idle.
* Memory is not under pressure.
* Root filesystem is only 16% used.
* `/var/log` is approximately 17 MB.
* No swap or significant I/O pressure observed.
* Recent SSH logs show successful authentication.
* No obvious SSH service failure was found.

**Overall status: HEALTHY**

---

## 8. If This Worsens

### 1. Investigate logs

Check more SSH logs for authentication failures or service errors:

```bash
sudo journalctl -u ssh --since "30 minutes ago"
```

### 2. Check system resources

Look for a process consuming excessive CPU or memory:

```bash
top
free -h
df -h
```

### 3. Restart and verify SSH if necessary

If SSH is confirmed to be malfunctioning:

```bash
sudo systemctl restart ssh
sudo systemctl status ssh
ss -tulpn | grep :22
```

After restarting, verify that SSH is listening on port 22 and that a new SSH connection can be established.

---

## Troubleshooting Flow

```text
Problem reported
      ↓
Check service status
      ↓
Check CPU & Memory
      ↓
Check Disk & I/O
      ↓
Check Network / Ports
      ↓
Check Service Logs
      ↓
Identify likely cause
      ↓
Take corrective action
      ↓
Verify service health
```

**Key principle:** Always collect evidence before restarting or changing a service.
