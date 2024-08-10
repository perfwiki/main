## Introduction

This page provides various documentation for perf tool.
It's under construction forever.

### Code trees
If not state otherwise all features code described in here is based on:

```
git://git.kernel.org/pub/scm/linux/kernel/git/jolsa/perf.git

perf/core branch
```

which is regularly based/synced on Arnaldo's tree:

```
git://git.kernel.org/pub/scm/linux/kernel/git/acme/linux (fetch)

perf/core branch
```

## Docs

- [events](./jolsa_docs_event.md)
- [counting explained](./jolsa_docs_counting.md)
- [sampling explained](./jolsa_docs_sampling.md)
- [diff explained](./jolsa_docs_sampling.md)
- [annotations explained](./jolsa_docs_annotations.md)
- [jolsa_Docs_FileFormat](./file_format_description.md)
- [join perf developement](./jolsa_docs_developing.md)

### Commands
- [perf stat](./jolsa_docs_perf_stat.md)
- [perf record](./jolsa_docs_perf_stat.md)
- [perf report](./jolsa_docs_perf_stat.md)
- [perf diff](./jolsa_docs_perf_stat.md)

## Tests

- [tests explained](./jolsa_tests_explained.md) - how to run/create tests
- [auto test framework](./jolsa_tests_auto.md) - automatic compile/run test

## Howtos

- [install perf package](./jolsa_howto_install.md)
- [install perf from sources](./jolsa_howto_install_sources.md)
- [measure child functions cycles](./jolsa_howto_measure_child_functions_cycles.md) - measure child functions cycles by parent

## Features

### Merged
- [post DWARF unwind](./jolsa_features_post_dwarf_unwind.md) - callchains DWARF unwind
- [group sampling](jolsa_features_groupsampling.md) - group leader sampling
- [multi diff](./jolsa_features_multidiff.md) - diff multiple files

### To be done
- [multiple file storage](./jolsa_features_multiple_file_storage.md) - allow to store record data into multiple files
- [cpu hotplug](./jolsa_features_cpu_hotplug.md) - follow cpu hotplug in perf record
- [stat formulas](./jolsa_features_stat_formulas.md) - formulas support over perf stat
- [precise](./jolsa_features_precise.md) - precise level detection
- [kbuild](./jolsa_features_kbuild.md) - kernel kbuild support
- [mmap details](./jolsa_features_mmap.md) - report mmap details
- [top data file](./jolsa_features_record_top_data_file.md) - store top data into data file
- [nonarchitectural event](./jolsa_features_nonarchitectural_events.md) - nonarchitectural event specification
- [data file merge](./jolsa_features_data_file_merge.md) - merge data files by time
- [toggle event](./jolsa_features_togle_event.md) - run event on another event sample

## Contact
- `Jiri Olsa <jolsa at redhat dot com>`
