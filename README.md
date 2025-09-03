# Philosophers - Dining Philosophers Problem

A multi-threaded simulation of the classic Dining Philosophers problem, demonstrating concurrent programming concepts, synchronization, and deadlock prevention.

## Table of Contents

- [About](#about)
- [The Problem](#the-problem)
- [Features](#features)
- [Installation](#installation)
- [Usage](#usage)
- [Algorithm](#algorithm)
- [Examples](#examples)
- [Performance Analysis](#performance-analysis)
- [Common Issues](#common-issues)
- [Learning Objectives](#learning-objectives)
- [Resources](#resources)

## About

The Philosophers project is an implementation of Edsger Dijkstra's famous Dining Philosophers problem. This project teaches fundamental concepts of concurrent programming, including thread creation, mutex usage, deadlock prevention, and resource sharing in a multi-threaded environment.

## The Problem

### Original Problem Statement

Five philosophers sit at a round table with a bowl of spaghetti in the center. Between each pair of adjacent philosophers is a single fork. Each philosopher alternates between thinking and eating. To eat, a philosopher must pick up both forks adjacent to them (left and right). After eating, they put down both forks and return to thinking.

### The Challenge

The challenge is to design a solution that:
- Prevents deadlock (all philosophers waiting indefinitely)
- Prevents starvation (any philosopher waiting indefinitely)
- Maximizes concurrency (philosophers can eat simultaneously if resources allow)
- Maintains data consistency and thread safety

### Real-World Applications

This problem models many real-world scenarios:
- **Database transactions**: Multiple processes accessing shared resources
- **Operating systems**: Process scheduling and resource allocation
- **Network protocols**: Distributed systems coordination
- **Parallel computing**: Load balancing and synchronization

## Features

- **Configurable parameters**: Number of philosophers, timing, and death conditions
- **Thread-safe implementation**: Proper mutex usage and synchronization
- **Deadlock prevention**: Smart fork acquisition strategy
- **Real-time monitoring**: Live status updates and logging
- **Death detection**: Automatic simulation termination on philosopher death
- **Performance metrics**: Timing analysis and statistics
- **Memory leak prevention**: Proper cleanup and resource management

## Installation

### Prerequisites

- **GCC compiler** (or any C99-compatible compiler)
- **POSIX threads library** (pthread)
- **Make** utility

### Compilation

```bash
# Clone the repository
git clone https://github.com/rustc0/DiningPhilos.git
cd DiningPhilos

# Compile the project
make

# Clean object files
make clean

# Full cleanup
make fclean

# Recompile everything
make re
```

## Usage

```bash
./philo number_of_philosophers time_to_die time_to_eat time_to_sleep [number_of_times_each_philosopher_must_eat]
```

### Parameters

| Parameter | Description | Unit | Required |
|-----------|-------------|------|----------|
| `number_of_philosophers` | Number of philosophers (and forks) | count | Yes |
| `time_to_die` | Time before a philosopher dies without eating | milliseconds | Yes |
| `time_to_eat` | Time it takes for a philosopher to eat | milliseconds | Yes |
| `time_to_sleep` | Time a philosopher spends sleeping | milliseconds | Yes |
| `number_of_times_each_philosopher_must_eat` | Simulation stops when all have eaten this many times | count | No |

### Example Commands

```bash
# Basic simulation with 5 philosophers
./philo 5 800 200 200

# Simulation that ends after each philosopher eats 3 times
./philo 4 410 200 200 3

# Quick death scenario (testing edge cases)
./philo 3 300 100 100

# Large scale simulation
./philo 100 800 200 200
```

## Algorithm

### Core Algorithm Steps

1. **Initialization Phase**
   ```
   Create N philosophers as threads
   Create N mutexes (one for each fork)
   Initialize timing and state variables
   ```

2. **Philosopher Lifecycle**
   ```
   WHILE (not dead AND not finished eating):
       THINK
       TRY TO ACQUIRE FORKS
       EAT (if both forks acquired)
       RELEASE FORKS
       SLEEP
   ```

3. **Fork Acquisition Strategy**
   ```
   To prevent deadlock:
   - Even numbered philosophers pick up left fork first
   - Odd numbered philosophers pick up right fork first
   ```

### Deadlock Prevention

The solution implements several deadlock prevention techniques:

1. **Ordered Resource Allocation**: Philosophers acquire forks in a specific order
2. **Timeout Mechanisms**: Prevent indefinite waiting
3. **Resource Hierarchy**: Assign ordering to resources (forks)

### State Machine

```
    [THINKING] 
        ↓
    [HUNGRY] ← trying to acquire forks
        ↓
    [EATING] ← has both forks
        ↓
    [SLEEPING]
        ↓
    [THINKING]
```

## Examples

### Basic Simulation Output

```bash
$ ./philo 5 800 200 200
0 1 has taken a fork
0 1 has taken a fork
0 1 is eating
0 3 has taken a fork
0 3 has taken a fork
0 3 is eating
200 1 is sleeping
200 3 is sleeping
200 2 has taken a fork
200 2 has taken a fork
200 2 is eating
200 4 has taken a fork
200 4 has taken a fork
200 4 is eating
400 1 is thinking
400 3 is thinking
400 2 is sleeping
400 4 is sleeping
...
```

### Death Scenario

```bash
$ ./philo 4 310 200 100
0 1 has taken a fork
0 1 has taken a fork
0 1 is eating
0 3 has taken a fork
200 1 is sleeping
200 2 has taken a fork
200 2 has taken a fork
200 2 is eating
300 1 is thinking
310 4 died
```

### Successful Completion

```bash
$ ./philo 3 800 200 200 2
...
1200 All philosophers have eaten at least 2 times
Simulation completed successfully
```

## Performance Analysis

### Key Metrics

1. **Throughput**: Meals eaten per second
2. **Fairness**: Standard deviation of meals per philosopher
3. **Resource Utilization**: Fork usage percentage
4. **Latency**: Time from hungry to eating

### Optimization Techniques

1. **Minimize Critical Sections**: Reduce mutex lock duration
2. **Efficient Timing**: Use high-resolution timers
3. **Smart Scheduling**: Balance thinking and eating times
4. **Memory Locality**: Optimize data structure layout

## Common Issues

### Deadlock Scenarios

```bash
# This configuration is prone to deadlock
./philo 5 800 200 200

# Symptoms:
- All philosophers acquire one fork
- None can acquire second fork
- Simulation freezes
```

### Race Conditions

```bash
# Insufficient synchronization leads to:
- Garbled output messages
- Incorrect meal counting
- Premature death detection
```

### Memory Issues

```bash
# Common problems:
- Mutex not properly destroyed
- Thread resources not cleaned up
- Memory leaks on error conditions
```

### Common Debugging Tools

1. **Valgrind** (Memory leaks)
   ```bash
   valgrind --tool=helgrind ./philo 5 800 200 200
   ```

2. **Thread Sanitizer** (Race conditions)
   ```bash
   gcc -fsanitize=thread -g philo.c -o philo
   ```

3. **GDB** (Step-through debugging)
   ```bash
   gdb ./philo
   (gdb) set args 5 800 200 200
   (gdb) run
   ```

## Learning Objectives

After completing this project, you should understand:

### Threading Concepts
- **Thread creation** using `pthread_create()`
- **Thread synchronization** with mutexes
- **Thread lifecycle** management
- **Concurrent execution** patterns

### Synchronization Primitives
- **Mutex locks** for mutual exclusion
- **Critical sections** identification
- **Deadlock prevention** strategies
- **Race condition** elimination

### System Programming
- **Time management** and precision timing
- **Resource management** and cleanup
- **Error handling** in concurrent environments
- **Performance optimization** techniques

### Problem-Solving Skills
- **Algorithm design** for concurrent systems
- **State machine** implementation
- **Resource allocation** strategies
- **System monitoring** and logging

## Resources

### Essential Reading

1. **"The Little Book of Semaphores"** by Allen B. Downey
2. **"Operating System Concepts"** by Silberschatz, Galvin, Gagne
3. **"Concurrent Programming in C"** by Steve Summit

---

**"In concurrent programming, the devil is in the details, and those details are called race conditions."** - Anonymous
