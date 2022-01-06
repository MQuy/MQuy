## Performance

There are several ways to improve performance

- Pipelining
- Branch prediction
- Loop unrolling
- Vectorization
- Cache

### Branch prediction

CPU does branch prediction to improve the flow in instruction pipeline. At the same time, branch misprediction is costy. When happening, all predicted computations are discarded, results of mispredicted computations have to be undone <- archieved via any errors are held until branch is evaluated and memory writes must be held.

```
$ pref stat ./your-program  // show percentage of predicted and mispredicted branches
```

### Cache

There are 3 common CPU caches: Data, Instruction and Translation lookaside buffer (TLB). Cache is classified by hierarchy: L1 (Data/Instruction cache per core), L2 (per core) and L3 (shared by all cores).
![cache hierarchy](https://i.imgur.com/aPT9JUc.png)

Factors affect cache:

- Cache associativity: Since cache is formed by cache line (block of memory for example 64 bytes). If we jump between memory addresses like 1, 65, 129, ... (usually looping) instead of 1, 2, 3 -> system has to bring that new cache line everytime (no locality).
- Cache coherency: a mechanism (hardware) that makes sure writing data is propagated to other copies (if needed) in cache lines.
- False sharing: if p is located in cache lines, thread 2 writes y -> system has to invalidate mark other cache line dirty and refetch it.
  ```c
  struct point {
    int x;
    int y;
  } p;
  int read_x() { 		// thread 1 (core 1) reads x
  	return p.x;
  }
  void write_y() {  // thread 2 (core 2) writes y
  	++p.y;
  }
  ```

### References

- [Scott Meyers: Cpu Caches and Why you care](https://www.youtube.com/watch?v=WDIkqP4JbkE)
- [Timur Doumler: Want fast C++? Know your hardware!](https://www.youtube.com/watch?v=BP6NxVxDQIs)
- [Fedor Pikus: Branchless programming in C++](https://www.youtube.com/watch?v=g-WPhYREFjk)
