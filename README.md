# Pantheon Congestion Control Experiments

## 📌 Overview
This project evaluates and compares the performance of various congestion control algorithms using the Pantheon framework developed by Stanford. The experiments focus on measuring throughput, latency, and loss under different network conditions.

## ✅ Successfully Tested Schemes
- **Sprout** (Worked perfectly)
- **Vivace** (Worked after minor display configuration)
- **Fillp** (Worked with IP fix)

## ❌ Schemes That Failed (and why)
| Scheme   | Status   | Error Description                                                                 |
|----------|----------|----------------------------------------------------------------------------------|
| Cubic    | Failed   | Missing binaries (`cubic_sender`, `cubic_receiver`); Makefile and protobuf issues |
| Vegas    | Failed   | Same as Cubic; incomplete source, missing builds                                  |
| Verus    | Failed   | Boost error: `double` passed to `milliseconds()`                                 |
| WebRTC   | Failed   | Requires `Xvfb`; JS-based, unstable in CLI environments                         |
| PCC      | Failed   | Missing compiled binaries; `sender`/`receiver` not built                         |

## 💽 System Requirements
- Ubuntu 20.04 (recommended)
- Python 2.7
- Git, build tools

## 🛠️ Installation & Setup
```bash
# OS Update
sudo apt update && sudo apt upgrade -y

# Install dependencies
sudo apt install -y python2.7 python2.7-dev python2-minimal \
  git build-essential cmake libtool autoconf pkg-config \
  iproute2 iputils-ping net-tools iptables curl

# Clone Pantheon
git clone https://github.com/StanfordSNR/pantheon.git
cd pantheon

# Submodule initialization
git submodule update --init --recursive

# Mahimahi install (fallback)
sudo apt install -y mahimahi

# Python 2 pip setup
curl https://bootstrap.pypa.io/pip/2.7/get-pip.py -o get-pip.py
sudo python2 get-pip.py
sudo python2 -m pip install matplotlib numpy pandas tabulate pyyaml

Dependency Setup
# Scheme setup
python2 src/experiments/setup.py --setup --schemes "sprout vivace fillp"


Create Mahimahi Traces
echo -e "1\n1\n1\n1" > 50mbps.trace   # Low latency
echo 12 > 1mbps.trace                  # High latency


Running Experiments
# Low latency test
mm-delay 5 mm-link 50mbps.trace 50mbps.trace
python2 src/experiments/test.py local --schemes "sprout vivace fillp" --data-dir results_lowlatency
exit

# High latency test
mm-delay 100 mm-link 1mbps.trace 1mbps.trace
python2 src/experiments/test.py local --schemes "sprout vivace fillp" --data-dir results_highlatency
exit

Analyze Results
# Built-in analysis
python2 src/analysis/analyze.py --data-dir results_lowlatency
python2 src/analysis/analyze.py --data-dir results_highlatency

Troubleshooting Notes
Xvfb for Vivace:
sudo apt install xvfb
export DISPLAY=:99 && Xvfb :99 &

Protobuf for Verus:
sudo apt install protobuf-compiler
protoc -I=./protobufs-default --cpp_out=./protobufs-default ./protobufs-default/dna.proto

Boost error in Verus:
// Fix: Change to integer milliseconds
boost::posix_time::milliseconds(static_cast<int>(value))


Important Notes
Do not rerun git clone inside existing Pantheon folder.

Manual Makefiles were created for Cubic and Vegas but not used in final run.

Only Sprout, Vivace, and Fillp are used in final result graphs.

Output Artifacts
results_lowlatency/, results_highlatency/

PNGs: throughput_low.png, throughput_high.png, rtt_vs_throughput.png

PDF: pantheon_report.pdf (if generated)
