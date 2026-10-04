# CC Experiment 01 — Hypervisor Performance Comparison

## Performance Evaluation of Type-1 and Type-2 Hypervisors

---

## 1. Objectives

1. To study the working of Type-1 and Type-2 hypervisors.
2. To configure and run a virtual machine using a Type-1 hypervisor.
3. To configure and run a virtual machine using a Type-2 hypervisor.
4. To execute the same CPU benchmark on both virtual machines.
5. To compare the performance of both virtualization environments based on execution time, CPU throughput, and latency.

---

# 2. Type-1 Hypervisor — Proxmox VE

## 2.1 Configuration

The Type-1 virtualization environment was created using **Proxmox VE**.

| Parameter | Configuration |
|---|---|
| Hypervisor | Proxmox VE |
| Hypervisor Type | Type-1 |
| Virtualization | KVM |
| VM Name | CC-Exp1-Type1 |
| Guest OS | Ubuntu 22.04.5 LTS |
| CPU | 2 vCPU |
| CPU Type | x86-64-v2-AES |
| Memory | 2048 MiB |
| Storage | 20 GB |
| Network | VirtIO / vmbr0 |
| Benchmark | Sysbench CPU 1.0.20 |
| Prime Number Limit | 20000 |
| Number of Threads | 1 |

---

## 2.2 Architecture

Proxmox VE is a **Type-1 (bare-metal) hypervisor**. It runs directly on the physical hardware and uses KVM for virtualization.

```text
+--------------------------------------+
|       Ubuntu 22.04.5 LTS VM          |
|                                      |
|       2 vCPU | 2 GB RAM | 20 GB     |
+--------------------------------------+
|              KVM                     |
|       Virtualization Layer           |
+--------------------------------------+
|          Proxmox VE                  |
|          Type-1 Hypervisor           |
+--------------------------------------+
|       Physical Hardware              |
|        CPU | RAM | Storage           |
+--------------------------------------+
```

---

## 2.3 Execution

The following commands were executed inside the **Ubuntu VM**:

```bash
hostnamectl - Shows the system/VM name and OS details
lscpu -Shows CPU information such as cores, threads, architecture, and speed.
free -h-Shows RAM/memory usage in human-readable format.
df -h-Shows disk/storage space used and available.
top-Displays real-time CPU, memory, and process usage.
```

### Installing Sysbench

```bash
sudo apt update-Updates the list of available software packages.
sudo apt install sysbench -y -Installs the Sysbench performance testing tool.
```

### Checking Sysbench Version

```bash
sysbench --version -Displays the installed Sysbench version.
```

### Running CPU Benchmark

```bash
sysbench cpu --cpu-max-prime=20000 run-Performs a CPU performance test by calculating prime numbers up to 20,000.
```

The benchmark was executed for approximately **10 seconds using 1 thread**.

---

## 2.4 Result

| Metric | Proxmox VE |
|---|---:|
| Number of Threads | 1 |
| Prime Limit | 20000 |
| Execution Time | 10.0006 s |
| Total Events | **16,903** |
| Events/sec | **1689.43** |
| Minimum Latency | 0.57 ms |
| Average Latency | **0.59 ms** |
| Maximum Latency | 1.09 ms |
| 95th Percentile | 0.68 ms |

### Observation

The Proxmox VE VM completed **16,903 events** during the benchmark. The measured CPU throughput was **1689.43 events/sec**, with an average latency of **0.59 ms**.


---

# 3. Type-2 Hypervisor — VMware Workstation

## 3.1 Configuration

The Type-2 virtualization environment was created using **VMware Workstation**.

| Parameter | Configuration |
|---|---|
| **Hypervisor** | VMware Workstation |
| **Hypervisor Type** | Type-2 |
| **Virtualization** | Hosted Virtualization |
| **Guest OS** | Ubuntu 64-bit |
| **CPU** | 2 vCPU |
| **Processor Configuration** | 1 Processor × 2 Cores |
| **Memory** | 2048 MB |
| **Storage** | 20 GB |
| **Network** | NAT |
| **Host CPU** | 12th Gen Intel Core i5-12450H |
| **Benchmark** | Sysbench CPU 1.0.20 |
| **Prime Number Limit** | 20000 |
| **Number of Threads** | 1 |

---

## 3.2 Architecture

VMware Workstation is a **Type-2 (hosted) hypervisor**. It runs as an application above the host operating system and provides virtualization to the guest operating system.

```text
+--------------------------------------+
|          Ubuntu 64-bit VM            |
|                                      |
|       2 vCPU | 2 GB RAM | 20 GB     |
+--------------------------------------+
|        VMware Workstation            |
|          Type-2 Hypervisor           |
+--------------------------------------+
|       Host Operating System          |
|             Windows                  |
+--------------------------------------+
|          Physical Hardware           |
|           CPU | RAM | Storage        |
+--------------------------------------+
```

---

## 3.3 Execution

The following commands were executed inside the **Ubuntu VM**:

```bash
hostnamectl
lscpu
free -h
df -h
top
```

### Installing Sysbench

```bash
sudo apt update
sudo apt install sysbench -y
```

### Checking Sysbench Version

```bash
sysbench --version
```

### Running CPU Benchmark

```bash
sysbench cpu --cpu-max-prime=20000 run
```

The benchmark was executed for approximately **10 seconds using 1 thread**.

---

## 3.4 Result

The important measurements obtained from the Sysbench CPU benchmark are shown below.

| Metric | VMware Workstation |
|---|---:|
| **Number of Threads** | 1 |
| **Prime Limit** | 20000 |
| **Execution Time** | 10.0002 s |
| **Total Events** | **10,589** |
| **Events/sec** | **1058.76** |
| **Minimum Latency** | 0.72 ms |
| **Average Latency** | **0.94 ms** |
| **Maximum Latency** | 5.32 ms |
| **95th Percentile** | 1.61 ms |

---

## Observation

The VMware Workstation VM completed **10,589 events** during the benchmark. The measured CPU throughput was **1058.76 events/sec**, with an average latency of **0.94 ms**.

The result indicates that the VMware Workstation VM achieved lower CPU throughput and higher latency compared with the Proxmox VE VM under the tested conditions.

---

# 4. Performance Comparison

The performance of the **Type-1 Proxmox VE** and **Type-2 VMware Workstation** hypervisors was compared using the same Sysbench CPU benchmark.

## 4.1 Performance Comparison Table

| Parameter | **Proxmox VE** | **VMware Workstation** |
|---|---:|---:|
| **Hypervisor Type** | Type-1 | Type-2 |
| **Execution Time** | 10.0006 s | 10.0002 s |
| **Total Events** | **16,903** | **10,589** |
| **CPU Throughput** | **1689.43 events/sec** | **1058.76 events/sec** |
| **Minimum Latency** | **0.57 ms** | 0.72 ms |
| **Average Latency** | **0.59 ms** | 0.94 ms |
| **95th Percentile** | **0.68 ms** | 1.61 ms |
| **Maximum Latency** | **1.09 ms** | 5.32 ms |

---

## 4.2 CPU Throughput Comparison

The CPU throughput obtained from both hypervisors is:

- **Proxmox VE:** 1689.43 events/sec
- **VMware Workstation:** 1058.76 events/sec

Proxmox VE achieved approximately **59.6% higher CPU throughput** than VMware Workstation in this experiment.

---

## 4.3 Latency Comparison

The average latency obtained was:

- **Proxmox VE:** 0.59 ms
- **VMware Workstation:** 0.94 ms

Thus, VMware Workstation showed higher average latency than Proxmox VE.

The maximum latency was:

- **Proxmox VE:** 1.09 ms
- **VMware Workstation:** 5.32 ms

This indicates that the VMware Workstation VM experienced larger latency spikes during the benchmark.

---

## 5. Performance Graph

The following graph compares the CPU throughput of the Type-1 and Type-2 hypervisors using the Sysbench CPU benchmark.

![Hypervisor Performance Comparison](screenshots/comparison/01-hypervisor-performance-comp<img width="2664" height="1768" alt="image" src="https://github.com/user-attachments/assets/1224b72a-0fc0-4424-b7b6-3a8dcf59bd2e" />
arison.png)

### Graph Observation

Proxmox VE achieved a CPU throughput of **1689.43 events/sec**, while VMware Workstation achieved **1058.76 events/sec**. Therefore, **Proxmox VE showed higher CPU throughput** than VMware Workstation under the tested conditions.

# 7. Conclusion

This experiment compared the CPU performance of a **Type-1 hypervisor (Proxmox VE)** and a **Type-2 hypervisor (VMware Workstation)** using the Sysbench CPU benchmark.

Both virtual machines were configured with comparable resources and tested using the same **prime number limit of 20000** and **1 benchmark thread**.

The results obtained were:

- **Proxmox VE:** 1689.43 events/sec
- **VMware Workstation:** 1058.76 events/sec

Proxmox VE achieved higher CPU throughput and lower average latency in the conducted experiment. Therefore, under the tested conditions, the **Type-1 Proxmox VE environment demonstrated better CPU performance than the Type-2 VMware Workstation environment**.

---

# 8. Author and USN

**Author:** Anushree Angadi

**USN:** 01FE24BCI050
