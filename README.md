Fixes to SVF for `epoll` and `IntToPtrInst` handling. 

## Epoll

The functions `epoll_ctl` and `epoll_wait` potentially pass `struct` objects to the kernel. The baseline SVF does not track these objects, leading to unsoundness. In `epoll.patch` we fix this by adding the `CopyEdges` between the objects passed as arguments to these functions.

## IntToPtrInst

The `IntToPtrInst` LLVM instruction allows the casting of arbitrary integers to pointers. In the current version of SVF, this is not handled correctly. We fix this by performing a backward slice of the `inttoptrinst` to first find the source pointer that was cast to the `int`, if possible. If we can find this source pointer, then we add a `CopyEdge` between the source pointer and the `IntToPtrInst`. If not, we add a `BlackHoleEdge` that causes the analysis to treat that pointer to point to ALL objects in the application.
