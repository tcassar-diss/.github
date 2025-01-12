# tcassar-diss

All the code and artifacts for my Final Year Project at the [University of Manchester](https://www.manchester.ac.uk/).

## Fine Grained System Call Filtering

- Applying `seccomp` filters to applications is an established practice [](https://www.kernel.org/doc/html/v5.1/userspace-api/seccomp_filter.html).
- `seccomp` filtering reduces the number of system calls available to an application. This in turn limits what an application can do, and can therefore improve application security.

## Motivation

### Software Supply-Chain Attacks
- Supply chain attacks are becoming increasingly common e.g ([libxz](https://en.wikipedia.org/wiki/XZ_Utils_backdoor) backdoor in February 2024.
- This, along with the principle of least privilege, would suggest that it is good security practice to only grant a shared library access to the syscalls that it needs.

### `syso` - analysis tooling
- To see if the ability to filter system calls for each shared library makes sense, I implemented `syso`.
- `strace` like tooling which is able to map which shared library made a syscall using BPF (strace cannot trace system call back to shared library)
- See [github.com/tcassar-diss/syso](https://www.github.com/tcassar-diss/syso) for more information.

### Some test analyses
- Ran `syso` on redis and nginx (while benchmarks were being run) to get an idea of which system calls were made by which library.
- Computed some numbers: looked at privilege reduction if each of the shared libraries shown by `pmap` were compromised. 
- In the **worst case**, redis saw a 37.0% privilge reduction and nginx 23.7%.
- See raw results + in depth calculations at [github.com/tcassar-diss/syso-analyses](https://www.github.com/tcassar-diss/syso-analyses)


## `addrfilter`
- `addrfilter` does the address filtering.

### Roadmap
- [x] Apply filter only to relevant PIDs
- [x] Orchestrate killing a PID from BPF*
- [x] Walk stack back past `libc` in-kernel
- [ ] Map non-libc syscall site to relevant syscall whitelist
- [ ] Kill PID only if syscall number not in whitelist
- [ ] Assign whitelists to address space in userspace

- [ ] Demo with example applications

- [ ] Move PID kill functionality from userspace -> kernel module
- [ ] Move address space parsing from userspace -> kernel module

