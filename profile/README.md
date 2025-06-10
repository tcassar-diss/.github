# My Dissertation

All the code and artifacts for my Final Year Project at the [University of Manchester](https://www.manchester.ac.uk/).

## Fine-Grained System Call Filtering in Linux

### `seccomp` and it's issues

- System call (syscall) filters are used to secure applications from RCEs,
  supply chain attacks, and for sandboxing.
- `seccomp` exists, and is [widely used](https://en.wikipedia.org/wiki/Seccomp)
  to filter syscalls.
- The issue with `seccomp` is that **applications are large**. As applications
  grow in size, the set of syscalls they make also tends to grow.
- This means that a single syscall filter for the whole application **is
  necessarily permissive**.
- This **reduces the effectiveness of syscall filtering** to mitigate attacks.

### Per-`vma` filtering

- **Solution**: Use multiple syscall filters for a single process
- `addrfilter` assigns a filter to each file-backed memory region in the
  process's address space.
- This means that **each shared library has its own filter**

### Effective?

- Yes. `addrfilter` sees a mean privilege reduction of **52.6%** compared to seccomp across our suite of benchmarks
- See report [§5](https://www.github.com/tcassar-diss/report) for details (PDF available in _releases_)

### Slow?
- Depends. High rates of syscalls => more slowdown
- Nginx showed 25.1% reduction in throughput, 36.1% in mean latency (using `wrk`)
- `fio` and OpenMP-based NPB benchmarks showed no slowdown in any metric

So, slowdown is **highly workload dependant**

## Try it for yourself

Go to [github.com/tcassar-diss/addrfilter](https://www.github.com/tcassar-diss/addrfilter) to try it (requires linux, x86).

### Other Repos

- [syso](https://www.github.com/tcassar-diss/syso): evaluation tool to generate
  privilege reduction numbers. Can also dynamically generate `addrfilter`
  whitelists.
- [report](https://www.github.com/tcassar-diss/report): a formal write up of the
  project - the dissertation deliverable.
