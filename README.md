# 📦 TCP State Entropy Monitor (eBPF-based)

A lightweight eBPF-based tool for detecting slow resource-exhaustion DDoS attacks by analyzing TCP state transitions.  
It dynamically attaches to the kernel function `tcp_set_state` using BCC and collects state transition statistics.  
Using a windowed **Shannon entropy** calculation, it reflects the distribution uniformity of TCP states over time.  
This is particularly effective for identifying attacks such as **Slowloris**, where connections get stuck in specific TCP states.

---
## 🗂️ Project Structure

```bash
tcp_state_monitor/
├── tcp_states_entropy.py        # Main monitoring script (user space, BCC-based)
├── README.md                    # Project documentation
```
---
## 🔍 `tcp_states_entropy.py`

**Main user-space controller script for real-time TCP state transition monitoring and entropy analysis.**

- Attaches to the `tcp_set_state` kernel function via `kprobe` using BCC
- Tracks all 12 TCP states (e.g., `ESTABLISHED`, `FIN_WAIT1`, `CLOSE_WAIT`, etc.)
- Aggregates state counts over a configurable sliding window (default: 5 seconds)
- Computes **Shannon entropy** to assess distribution uniformity
- Optionally resets and verifies the state map each window

### ✅ Features

- 📊 Real-time monitoring of TCP state behavior
- 🧠 Entropy-based metric to capture abnormal state concentration
- 🔄 Sliding-window aggregation to eliminate historical noise
- 🧪 Automatic eBPF map reset and verification
- 📉 Effective for detecting slow-rate resource-based DDoS attacks

---
## 🚀 Usage

### 📦 Requirements

- Linux kernel ≥ 4.9 with eBPF + kprobe support
- Python ≥ 3.6
- [BCC (BPF Compiler Collection)](https://github.com/iovisor/bcc) installed with Python bindings

### ▶️ Run the Script

```bash
sudo python3 tcp_states_entropy.py
````

### 🛑 Stop the Script

Press `Ctrl+C` to gracefully terminate.

---

## 🧪 Sample Output

```
Monitoring TCP states (5s sliding window), press Ctrl+C to exit
============================================================
📊 Window #1 - 12:01:10
Duration: 5s | Total Transitions: 431 | Entropy: 2.7321
------------------------------------------------------------
Active TCP States:
  TCP_ESTABLISHED  :    102 ( 23.7%) █████████
  TCP_CLOSE_WAIT   :    205 ( 47.6%) █████████████████
  TCP_FIN_WAIT1    :    124 ( 28.8%) ██████████
============================================================
🔄 Map cleared at 12:01:10
Verifying map reset...
✅ Map reset verification complete
```
![屏幕截图 2025-07-07 222603](https://github.com/user-attachments/assets/596fa4cc-db97-4144-b6d8-d9b77423cf72)

---

## 📚 Terminology

* **Shannon Entropy**: A measure of uncertainty in a distribution. When entropy is low, it indicates abnormal concentration—often a sign of slow or blocked connections.
* **CLOSE\_WAIT anomaly**: Attacks like Slowloris often cause connections to remain stuck in the `CLOSE_WAIT` state, resulting in unbalanced distributions.

---

## 🔧 Future Enhancements

* Entropy trend detection via sliding average
* Integration with IP-level tracing and blacklisting
* CSV / JSON export for dashboards and visualization
* Prometheus / Grafana integration support

