# optical-transport-bdp-benchmark
Empirical benchmark &amp; simulation testbed for evaluating QUIC, TCP BBR, and CUBIC over degraded high-BDP intercontinental optical links (10,000 km, 100 ms RTT).

# Performance Evaluation of QUIC, TCP BBR, and CUBIC over Degraded High-BDP Optical Links

This repository contains the reproduction artifact, simulation testbed, and automated benchmarking suite for evaluating transport layer protocols (TCP CUBIC, TCP BBR, and QUIC) over emulated long-haul, high-BDP optical transmission spans.

## 📌 Research Overview
- **Emulated Link**: 10,000 km intercontinental SMF-28 fiber link
- **Bandwidth**: 100 Mbps
- **Baseline RTT**: 100 ms (One-way delay: 50 ms)
- **Bandwidth-Delay Product (BDP)**: 1.25 MB (~856 in-flight packets)
- **Loss Regimes**: 0.00%, 0.01%, 0.10%, 0.50%, 1.00%, 3.00%, 5.00%, and 10.00%
- **Evaluated Protocols**: TCP CUBIC (in-kernel), TCP BBR (in-kernel), QUIC (`aioquic`)

## 📂 Repository Structure
