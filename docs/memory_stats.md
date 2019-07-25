free

total = used + free + buffers + cache

cat /proc/meminfo

Active = Active(anon) + Active(file)
Inactive = Inactive(anon) + Inactive(file)
Slab = SReclaimable + Sunreclaim

free and /proc/meminfo
free::Mem_total  == meminfo::MemTotal
free::Mem_free == meminfo::MemFree
free::Mem_buff == meminfo::Buffers
free::Mem_cache == meminfo::Cached + meminfo::Slab
free::Mem_available == meminfo::MemAvailable
free::Swap_total == meminfo::SwapTotal
free::Swap_free == meminfo::SwapFree
free::shared == meminfo::Shmem


https://access.redhat.com/solutions/406773

https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=34e431b0ae398fc54ea69ff85ec700722c9da773
