# job-scheduler-multi-gpu

# Tiny Job Scheduler for Multi-GPU Jobs

A minimal, systems-focused job scheduler for running and managing **multi-GPU jobs** on a single node or small cluster. This project is designed to teach the core ideas behind schedulers like Slurm, Kubernetes GPU operators, and internal AI platform schedulers—without the heavy machinery.

This is an educational infra project focused on:

* Resource accounting (GPUs, CPU, memory)
* Job queues and priorities
* Placement and scheduling logic
* Process lifecycle management

---

## Why This Project

Modern AI workloads depend on schedulers to decide:

* Which job runs where
* How many GPUs it gets
* When it should be preempted or queued

By building a tiny scheduler, you will understand:

* Scheduling algorithms
* GPU resource isolation
* Process orchestration
* Infra tradeoffs behind real systems

This is ideal for engineers targeting **AI Infrastructure / ML Platforms / Systems** roles.

---

## Features (Planned)

* GPU discovery via `nvidia-smi` or NVML
* Job queue with priorities
* Multi-GPU job placement
* FIFO and priority scheduling
* Job states: queued, running, finished, failed
* CLI to submit, list, cancel jobs
* Simple logging and metrics

---

## Architecture Overview

### High-Level Flow

```
User CLI  -->  Scheduler  -->  Worker/Runner  -->  GPU Processes
```

### Core Components

1. **CLI**

   * Submit jobs
   * Query status
   * Cancel jobs

2. **Scheduler Core**

   * Maintains job queue
   * Tracks free/used GPUs
   * Decides placement

3. **Resource Manager**

   * Discovers GPUs
   * Tracks allocation

4. **Job Runner**

   * Spawns processes
   * Sets CUDA_VISIBLE_DEVICES
   * Tracks exit status

5. **State Store**

   * In-memory (Phase 1)
   * File/SQLite later

---

## Repository Structure

```
tiny-gpu-scheduler/
│
├── scheduler/
│   ├── core.py           # Scheduling logic
│   ├── queue.py          # Job queue
│   ├── resources.py      # GPU discovery & tracking
│   ├── runner.py         # Launch & monitor jobs
│   └── state.py          # Job state management
│
├── cli/
│   └── tgpu.py           # Command line interface
│
├── examples/
│   ├── sleep_job.sh
│   ├── multi_gpu_job.sh
│   └── train_demo.py
│
├── logs/
│
├── requirements.txt
└── README.md
```

---

## Scheduling Model

Each job defines:

* Command to run
* GPUs required
* Priority

Example job spec:

```
command: python train.py
num_gpus: 2
priority: 5
```

Scheduler loop:

1. Check free GPUs
2. Pick highest-priority runnable job
3. Allocate GPUs
4. Launch job with CUDA_VISIBLE_DEVICES
5. Monitor until exit
6. Free resources

---

## Build Roadmap

### Phase 1 — Local Scheduler

* Detect GPUs
* FIFO queue
* Run single job at a time

### Phase 2 — Multi-GPU Placement

* Allow jobs to request N GPUs
* Allocate multiple devices

### Phase 3 — Priority Scheduling

* Priority queue
* Preemption (optional)

### Phase 4 — Persistence

* Store job state in file/SQLite

### Phase 5 — Metrics

* Basic stats: wait time, utilization

---

## First Milestone

**Goal:**

* Submit job requesting 2 GPUs
* Scheduler assigns GPUs correctly
* Job runs with correct CUDA_VISIBLE_DEVICES

---

## Requirements

* Linux
* NVIDIA GPUs
* Python 3.9+
* `nvidia-smi` or NVML

---

## Example CLI

Submit job:

```
tgpu submit --gpus 2 --priority 5 -- cmd="python train.py"
```

List jobs:

```
tgpu list
```

Cancel job:

```
tgpu cancel <job_id>
```
---

## License

MIT
