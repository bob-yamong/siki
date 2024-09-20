# SIKI(Systemcall-Wiki)

Linux Kernel Hooks & System Calls for BPF Programming

Welcome to **SIKI**, a comprehensive collection of Linux Kernel **LSM Hooks**, **Kprobes**, **Tracepoints**, and **System Calls** specifically focused on aiding **BPF (Berkeley Packet Filter) programming**. This repository aims to serve as a reference and learning resource for developers working with eBPF in kernel-level programming.

## Overview

This repository documents various kernel hooks and system calls, providing insights into how they interact with BPF programs. Whether you're building security policies, performance analysis tools, or network monitoring systems, this collection offers guidance on leveraging these kernel hooks effectively.

### Included Sections:

- **LSM (Linux Security Module) Hooks**: Security-related hooks for monitoring system behavior and enforcing policies.
- **Kprobes**: Dynamic probes that can be inserted into kernel code to collect debugging and monitoring information.
- **Tracepoints**: Static points within the kernel for efficient event tracing and logging.
- **System Calls**: Documentation on critical system calls frequently used in BPF-related applications.

> [!WARNING]  
> **This documentation is still incomplete. Contributions are highly encouraged.**

## Contributions

Contributions are welcome! If you have suggestions, improvements, or additional documentation to add, feel free to submit a pull request.

## References

- [Tetragon: Tracing Policy Hooks](https://tetragon.io/docs/concepts/tracing-policy/hooks/)
- [Linasm: Linux Network System Calls](https://linasm.sourceforge.net/docs/syscalls/network.php#socket)
- [Kernel BPF Documentation](https://docs.kernel.org/bpf/libbpf/program_types.html)
- [bootlin: Elixir Cross Referencer](https://elixir.bootlin.com/linux/v6.8/source/include/linux/lsm_hook_defs.h)
