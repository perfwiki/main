- Enable callchains for guests (used by perf kvm). At least doing this for the guest kernel should be very possible.

- The feature tests should be performed only when a file that needs those tests, or at least only when some .c or .h file will be rebuilt
    - An initial step would be for 'make install-doc' not to run the feature tests, there it is not needed at all.

- Packages needed for the build should be checked before we start building object files, such as bison (bpetkov)

- Forward port the page fault tracepoints and use it in 'trace':
    - [32ba4abf60ae](http://git.kernel.org/?p=linux/kernel/git/acme/linux.git;a=commitdiff;h=d53b11976093b6d8afeb8181db53aaffc754920d;hp=32ba4abf60ae1b710d22a75725491815de649bc5)

- Use Kconfig to allow selecting features and build minimal version of perf, e.g. one with just 'record' for use in embedded platforms.
    - David Ahern prototyped this, dig those patches and update them.

- Cherry pick the plugin support in libtraceevent.

- Add build id support in PERF_RECORD_MMAP, so that we can support long running sessions where update of components may take place.

- Allow automatic downloading of DSOs with richer symtabs and DWARF info from debuginfo servers such as darkserver (https://fedoraproject.org/wiki/Darkserver).

- Limit the size of the build id cache (~/.debug), in a way similar to how ccache manages its cache.

- Adopt Vince Weaver's suite of tests in 'perf test'.
    - http://www.eece.maine.edu/~vweaver/projects/perf_events/validation/

- Add reference counters to the dso and thread structs, so that in tools like 'top' we can remove unused threads from the dead_threads list and also unload symbol tables not referenced by any maps.

- Accumulate callchain info in order to get cumulative period info like 'sysprof'.

- [`Add --sort addr`](https://bugzilla.kernel.org/show_bug.cgi?id=55561)

- Systemtap SDT suppport in 'perf probe'
    - http://sourceware.org/systemtap/wiki/UserSpaceProbeImplementation

- Move build-id trimming from perf-record to perf-archive:
    - Just write the build-id for all DSOs, without trying to process all samples at perf-record time to find out which DSOs had samples and thus should be included in the perf.data build-id header
    - At perf archive time, process all samples and trim the result, so that the tarball is smaller.
    - Perhaps even an heuristic to figure out if the savings would be worth the trouble of processing all samples, i.e. look at the build-id table and do the math to figure out the sum of file sizes, if it is below some threshold, don't process the samples, just pack those files straight away, doing the sample processing only if it is more than that threshold.

- Resolve samples in callchains to DSOs and stash its build ids in the perf.data file header (acme)

- Implement --initial-delay, already available in 'perf stat', on 'perf trace'.

- Fix 'perf top --stdio -g' to limit the number of lines displayed, as it is not considering the callchains, perhaps we need to wire this up with the logic for '--max-stack', that is already available for 'perf top'. The problem is that it scrolls the screen, we can't see the top entries.

- What I want is that if I am on bar*(), it annotates bar*(), no samples just the call site (obtained from the callchain) dissassembly. This is useful because in many cases there maybe multiple call sites within a function and there maybe inlines in between. Hard to track down if you cannot figure out the surrounding addresses of the call site. (Request made by Stephane Eranian)

- Check for control+C, Q, when processing events in 'perf report', so that we can exit the tool when processing big files (acme)

- Adopt the kernel ERR_PTR() macro to avoid passing pointer addresses to return pointers from functions, instead use return pointer or return ERR_PTR(ENOENT), for instance, see include/linux/err.h in the kernel sources. (acme)

- Adopt the Hints warnings provided by 'perf trace' for permission checks in the other tools (acme)

- 'perf top' should be, just like the other tools (trace, stat, record), able to start a workload to observe it. I.e. 'perf top -e probe_perf:map__get,probe_perf:map__put perf top' should work.

- When viewing multiple events (say, perf top -e cycles,instructions) in non-grouped mode (the default) list the events sorted by number of samples, nowadays I think it retains the evlist linked list order, i.e. how the user specified it in the command line. (From a conversation between acme and fbl)

- Don't stop the build tests when some devel package required for one of the tests is not present, just skip that test, warning the user about it. I.e. in 'make -C tools/perf build-test' (acme)

- Record which 'make build-test' tests fail the most, and then run it in order of previous failures. Recording it in each test machine helps running the tests that fails the most on specific build environment, i.e. some distro may fail more frequently some tests than others. Do this for 'perf test' as well. And make it a command line selectable behaviour, as sometimes we're not interested in those, just checking if something _else_ that usually passes, stopped passing.

- Check if the objdump call in annotate worked or not, providing a popup window telling it didn't work, to test, just uninstall binutils or otherwise remove objdump from the path (acme)

- Introduce perf_evlist__strerror_create_maps() to provide more informative error messages for when a thread specified via -p in top/record doesn't exist, etc. (acme)

- Validade the CPU before passing to the kernel, i.e. 'perf top -C 10000' returns -EINVAL at sys_perf_event_open for something we could have validated beforehand (acme)

- Improve error message for 'perf record --call-graph dwarf' on an old kernel, say 3.9 on fedora 17, it should use perf_missing_features to notice that the perf_event_attr members needed for DWARF callchains are not present instead of plain telling that an invalid option was used (acme)

- Use '-e msr/smi/' in 'perf trace' if available, will be nice in a 'strace' like tool to show that in addition to syscalls, by default. (acme)

- Improve git pre-commit hook script to check for OPTION() with -- in its name, to fix bugs like [ad16511b0e40](https://git.kernel.org/cgit/linux/kernel/git/acme/linux.git/commit/?id=ad16511b0e40) ("perf mem: Add -U/-K (--all-user/--all-kernel) options") (acme)

- Use __printf(a, b) to annotate applicable functions, see tools/include/compiler-gcc.h and source code in the kernel tree to see examples (acme).

- Accessing symtabs in containers from the host, watch [Brendan's talk (link to the moment he describes it)](http://www.youtube.com/watch?v=bK9A5ODIgac&t=23m46s) to see the description of the problem and how to fix it (acme)

- perf probe tab completion, considering -x for a userspace binary or without it for kernel symbols, to allow this to work: `$ perf probe -x ~/bin/perf -L trace__` (acme)

- Use perf probe -x ~/bin/perf -F trace__* to get the possible functions for the tab completion (acme)

### Old entries
- Make the instruction augmentation in the annotate browser platform specific.
    - Right now they are x86 specific but are in the common code. [DONE](https://git.kernel.org/cgit/linux/kernel/git/tip/tip.git/commit/?id=786c1b51844d858041166057c0c79e93c2015013)
- Factorize the multidimensional sorting between perf report and annotate (will be used by perf trace)
- Implement a perf cmp (profile comparison between two perf.data) (DONE, its called 'perf diff')
- Implement a perf view (GUI) (Partially done, see 'perf report --gtk')
- Make pressing 'V' multiple times to go on cycling thru various verbosity levels in 'perf top', so that info that is present in 'perf top -v' can be obtained without having to restart the tool (acme). ([DONE](https://git.kernel.org/cgit/linux/kernel/git/acme/linux.git/commit/?id=fbf626346cc21065ec22dcd64ff34513506f271a))
- Use the highest precision level available by default, e.g.: cycles:pp ([DONE](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=7f8d1ade1b19f684ed3a7c4fb1dc5d347127b438))
- Enhance perf trace:
    - Handle the cpu field
    - Handle the timestamp
    - Use the in-perf ip -> symbol resolving
    - Use the in-perf pid -> cmdline resolving
    - Implement multidimensional sorting by field name
