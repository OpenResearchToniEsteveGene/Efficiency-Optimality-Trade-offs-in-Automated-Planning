# Ubuntu Setup Guide for Running the Planners

This README explains how to install **Ubuntu** and prepare an environment to run the planners used in the study **“Efficiency–Optimality Trade-offs in Automated Planning for Logistics Problems”**. It is written for students and researchers who want a simple, reproducible setup for running **Fast Downward** on Logistics-domain PDDL files.

The workflow below is based on the current official Ubuntu installation guide and the current Fast Downward documentation. Ubuntu’s official desktop installation tutorial currently targets **Ubuntu Desktop 24.04 LTS**, recommends at least **25 GB of storage**, and suggests using a **USB flash drive of 8 GB or more**. Fast Downward’s own documentation recommends **Ubuntu 24.04** for development and lists `cmake`, `g++`, `make`, and `python3` as the mandatory dependencies on Debian/Ubuntu systems. :contentReference[oaicite:0]{index=0}

## 1. Recommended target setup

To match the paper as closely as possible, you can use a machine with:

- Ubuntu Desktop 24.04 LTS
- 8 GB RAM
- Intel i3-class CPU
- Local execution from the terminal

This is not a strict requirement, but it reproduces the spirit of the experimental environment described in the article.

## 2. Choose how you want to install Ubuntu

You have three common options:

- **Full installation on a dedicated machine**
- **Dual boot with Windows**
- **Virtual machine installation**

Ubuntu officially supports standard desktop installation and also notes that you can install Ubuntu alongside another operating system, or run it in a virtualized setup instead of replacing your current system. :contentReference[oaicite:1]{index=1}

## 3. Install Ubuntu Desktop 24.04 LTS

### Step 1 — Download the ISO image

Download the current Ubuntu Desktop image from the official Ubuntu download page. The official Ubuntu Desktop documentation currently uses the **Ubuntu 24.04 LTS** installation flow. :contentReference[oaicite:2]{index=2}

### Step 2 — Create a bootable USB

Write the downloaded ISO to a USB flash drive (8 GB or larger is recommended by Ubuntu). Any standard USB imaging tool is fine.

### Step 3 — Boot from the USB

Restart your computer, open the boot menu or BIOS/UEFI menu, and select the USB drive.

### Step 4 — Start the installer

Choose **Install Ubuntu**.

If you are unsure which installer options to choose, the Ubuntu documentation explicitly says the default guided installer choices are safe for a normal desktop setup. :contentReference[oaicite:3]{index=3}

### Step 5 — Select disk layout

Choose one of these:

- **Erase disk and install Ubuntu** if the machine is dedicated to Ubuntu
- **Install Ubuntu alongside another operating system** if you want dual boot

Ubuntu’s installer supports both modes. :contentReference[oaicite:4]{index=4}

### Step 6 — Finish installation

Create your username, password, timezone, and start the installation.

### Step 7 — First update after boot

After Ubuntu starts for the first time, open a terminal and run:

```bash
sudo apt update
sudo apt upgrade -y
````

## 4. Important installation note for some Windows laptops

Ubuntu’s official installation guide warns that some PCs use **Intel RST (Rapid Storage Technology)**, which is not supported during installation unless it is disabled and the storage mode is changed appropriately. If the Ubuntu installer complains about RST, resolve that first before continuing. ([documentation.ubuntu.com][1])

## 5. Install the dependencies for Fast Downward

Fast Downward’s build instructions state that on Debian/Ubuntu, the mandatory dependencies are:

* `cmake`
* `g++`
* `make`
* `python3`

Install them with:

```bash
sudo apt install cmake g++ make python3 git -y
```

These are the official mandatory dependencies listed by Fast Downward for Linux/macOS, and Ubuntu 24.04 is the version recommended by the Fast Downward team for development. ([GitHub][2])

## 6. Download and build Fast Downward

Clone the repository and build it:

```bash
git clone https://github.com/aibasel/downward.git
cd downward
./build.py
```

According to the official build instructions, `./build.py` creates the default **release** build inside the `builds` directory. ([GitHub][2])

## 7. Test that the planner works

Fast Downward’s build guide includes a simple validation command using one of its benchmark files:

```bash
./fast-downward.py misc/tests/benchmarks/miconic/s1-0.pddl --search "astar(lmcut())"
```

This is the official sanity-check example from the build instructions. ([GitHub][2])

## 8. Run the planners used in the article

Fast Downward’s official usage documentation shows the following command for **A* with LM-cut**:

```bash
./fast-downward.py domain.pddl problem.pddl --search "astar(lmcut())"
```

For an **FF-based greedy best-first configuration**, the current usage documentation provides the following efficient FF setup:

```bash
./fast-downward.py domain.pddl problem.pddl \
  --search "let(hff, ff(), lazy_greedy([hff], preferred=[hff]))"
```

Fast Downward also warns that the seemingly simpler form below is inefficient because it computes the FF heuristic twice per state:

```bash
./fast-downward.py domain.pddl problem.pddl --search "lazy_greedy([ff()], preferred=[ff()])"
```

These command forms come directly from the official Fast Downward usage guide. ([fast-downward.org][3])

## 9. Example project layout

A simple folder structure is enough:

```text
planning-project/
├── logistics-domain.pddl
├── prob01.pddl
├── prob03.pddl
├── prob05.pddl
└── downward/
```

Then run commands such as:

```bash
cd downward

./fast-downward.py ../planning-project/logistics-domain.pddl ../planning-project/prob01.pddl \
  --search "astar(lmcut())"

./fast-downward.py ../planning-project/logistics-domain.pddl ../planning-project/prob01.pddl \
  --search "let(hff, ff(), lazy_greedy([hff], preferred=[hff]))"
```

## 10. Reading the output

Fast Downward typically reports:

* plan length
* plan cost
* expanded states
* evaluated states
* generated states
* total planner time
* peak memory

These are exactly the types of metrics used in the article to compare efficiency and optimality across the Logistics instances.

## 11. Troubleshooting

### Build fails after changing dependencies

Fast Downward’s build instructions recommend deleting the `builds` directory and rebuilding if the build environment changes. ([GitHub][2])

```bash
rm -rf builds
./build.py
```

### `fast-downward.py` is not executable

Run:

```bash
chmod +x fast-downward.py
chmod +x build.py
```

### Ubuntu installer does not detect the disk correctly

Check whether **Intel RST** is enabled in BIOS/UEFI and switch to the mode required by Ubuntu’s installer. ([documentation.ubuntu.com][1])

## 12. Minimal reproducible workflow

After Ubuntu is installed, the shortest end-to-end setup is:

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install cmake g++ make python3 git -y

git clone https://github.com/aibasel/downward.git
cd downward
./build.py

./fast-downward.py ../domain.pddl ../prob01.pddl --search "astar(lmcut())"
./fast-downward.py ../domain.pddl ../prob01.pddl --search "let(hff, ff(), lazy_greedy([hff], preferred=[hff]))"
```

## 13. Final note

If your goal is to reproduce the paper faithfully, **Ubuntu 24.04 LTS + Fast Downward release build** is the cleanest choice. That combination is aligned with the current official Ubuntu installation path and with the Fast Downward team’s own recommendation for development on Ubuntu. ([documentation.ubuntu.com][1])

[1]: https://documentation.ubuntu.com/desktop/en/latest/tutorial/install-ubuntu-desktop/ "Install Ubuntu Desktop - Ubuntu Desktop documentation"
[2]: https://github.com/aibasel/downward/blob/main/BUILD.md "downward/BUILD.md at main · aibasel/downward · GitHub"
[3]: https://www.fast-downward.org/latest/documentation/planner-usage/ "Planner usage - Fast Downward"
