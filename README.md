[README.md](https://github.com/user-attachments/files/33176036/README.md)
# Performance Evaluation of QUIC, TCP BBR, and CUBIC over Degraded High-BDP Optical Links

[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![Platform](https://img.shields.io/badge/platform-Linux%20%2F%20WSL2-lightgrey.svg)](https://www.linux.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

This repository contains the complete empirical evaluation framework, automation scripts, raw benchmark logs, and visualization routines for evaluating transport-layer protocols (**TCP CUBIC**, **TCP BBR**, and **QUIC**) across degraded high Bandwidth-Delay Product (BDP) optical links.

---

## 📌 Research Context & Parameters

This benchmark emulates an intercontinental long-haul optical fiber transmission link spanning **10,000 km** based on standard Single-Mode Fiber (SMF-28) physical propagation characteristics.

| Parameter | Specification | Note |
| :--- | :--- | :--- |
| **Physical Distance** | 10,000 km | SMF-28 ($n \approx 1.4682$, velocity $\approx 204,000$ km/s) |
| **Bottleneck Bandwidth ($C$)** | 100 Mbps | Rate-limited via token bucket filter |
| **Baseline Round-Trip Time (RTT)** | 100 ms | One-way propagation delay: 50 ms |
| **Bandwidth-Delay Product (BDP)** | 1.25 MB | $100\text{ Mbps} \times 0.100\text{ s} \approx 856\text{ in-flight packets}$ |
| **Loss Regimes ($p$)** | 8 levels | 0.00%, 0.01%, 0.10%, 0.50%, 1.00%, 3.00%, 5.00%, 10.00% |
| **Kernel Tuning** | TSO/GSO disabled | Pure transport protocol dynamics captured |
| **Evaluated Protocols** | 3 protocols | In-kernel TCP CUBIC, in-kernel TCP BBR, user-space QUIC (`aioquic`) |
| **Repetitions** | $n = 10$ runs | Sustained 20s transmission per iteration (240 total executions) |

---

## 📂 Repository Structure

```text
├── src/
│   ├── run_simulation.py       # Main automated testbed runner (netns + netem + iperf3 + aioquic)
│   ├── quic_bench.py           # Client/server QUIC benchmark helper using aioquic
│   ├── analyze_results.py      # Statistical aggregation and summary generation
│   ├── generate_all_figures.py # Visualization scripts for publication-ready figures
│   └── verify_manuscript.py    # Cross-verification of manuscript data against raw outputs
├── data/                       # Benchmark logs (JSON) per protocol and loss condition
├── figures/                    # Generated high-resolution plots (PNG/PDF)
├── summary_table.csv           # Consolidated metric table (Goodput, RTT, Retransmissions)
├── requirements.txt            # Python runtime dependencies
└── README.md                   # Repository documentation
```

---

## 🚀 Setup & Reproduction Steps

### 1. Prerequisites
Ensure you are running on **Linux** (native Ubuntu/Debian or WSL2) with root permissions:
```bash
sudo apt-get update
sudo apt-get install -y iproute2 iperf3 python3 python3-venv python3-pip
```

### 2. Python Environment Setup
```bash
# Clone the repository
git clone https://github.com/username/optical-transport-bdp-benchmark.git
cd optical-transport-bdp-benchmark

# Create and activate virtual environment
python3 -m venv .venv
source .venv/bin/activate

# Install dependencies
pip install -r requirements.txt
```

*(If `requirements.txt` is not yet created, install `aioquic matplotlib pandas numpy`)*

### 3. Run the Benchmark Automation
Execute the automated testbed runner. Root privileges (`sudo`) are required to configure Linux network namespaces (`ip netns`) and traffic control queue disciplines (`tc netem`):
```bash
sudo .venv/bin/python src/run_simulation.py
```

The script will automatically:
1. Initialize isolated dual network namespaces (`ns_client` and `ns_server`) with virtual ethernet (`veth`).
2. Deactivate hardware offloads (`TSO`, `GSO`, `GRO`) on veth interfaces.
3. Configure link delay (50 ms each direction) and buffer limits.
4. Execute 240 randomized benchmark runs across 8 loss conditions.
5. Export raw JSON metrics into `data/` and summary data into `summary_table.csv`.

### 4. Analyze Results & Generate Figures
```bash
# Generate statistical analysis and table verification
python src/analyze_results.py

# Generate publication plots (Throughput collapse, RTT inflation, Retransmission ratio)
python src/generate_all_figures.py
```

---

## 📊 Summary of Empirical Results

| Packet Loss (%) | TCP CUBIC Goodput (Mbps) | TCP BBR Goodput (Mbps) | QUIC (aioquic) Goodput (Mbps) |
| :---: | :---: | :---: | :---: |
| **0.00%** | $92.50 \pm 0.08$ | $90.54 \pm 0.24$ | $71.38 \pm 19.17$ |
| **0.01%** | $91.84 \pm 0.55$ | $90.54 \pm 0.44$ | $36.00 \pm 11.53$ |
| **0.10%** | $20.56 \pm 10.15$ | $90.09 \pm 0.41$ | $7.61 \pm 2.11$ |
| **0.50%** | $6.20 \pm 2.37$ | $89.87 \pm 0.47$ | $2.31 \pm 0.44$ |
| **1.00%** | $3.12 \pm 1.16$ | $89.27 \pm 0.48$ | $1.76 \pm 0.35$ |
| **3.00%** | $1.16 \pm 0.23$ | $86.72 \pm 1.25$ | $1.52 \pm 0.22$ |
| **5.00%** | $0.70 \pm 0.11$ | $83.56 \pm 1.54$ | $1.50 \pm 0.20$ |
| **10.00%** | $0.37 \pm 0.06$ | $66.86 \pm 7.71$ | $1.54 \pm 0.24$ |

*TCP BBR demonstrates an astounding **181x throughput advantage** over TCP CUBIC under 10.00% packet loss.*

---

## 📄 Citation

If you use this benchmark testbed, automation code, or empirical datasets in your academic research, please cite our paper:

```bibtex
@article{hasan2026quic,
  title   = {Performance Evaluation of QUIC, TCP BBR, and CUBIC over Degraded High-BDP Optical Links},
  author  = {Hasan, Syahbudin Hamzah and Ichsan, Ichwan Nul and Suranegara, Galura Muhammad},
  journal = {Transmisi: Jurnal Ilmiah Teknik Elektro},
  year    = {2026}
}
```

---

## ⚖️ License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
