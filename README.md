HPC Performance Anomaly Suite (HPAS)
====================================

This repository holds the anomaly suite presented in the paper "[HPAS: An HPC Performance Anomaly Suite for Reproducing Performance Variations](https://github.com/peaclab/HPAS/blob/master/docs/ates_icpp19.pdf)"[1]. HPAS, consists of a set of synthetic anomalies that reproduce common root causes of performance variations in supercomputers:

* CPU contention
* Cache evictions
* Memory bandwidth interference
* Memory intensive processes
* Memory leaks
* Network contention
* I/O contention 

These anomalies use processes that run in user space; thus, it does not require any hardware or kernel modification. For installation details please see below. 


Installation
------------
A brief intro is here, but check INSTALL for more details.
1. Install C/C++ compilers, GNU autotools
    1. Install shmem if you wish to use netoccupy anomaly. OpenMPI typically
       includes a shmem compiler.
2. If checking out from git, generate the configure script by running the
   command `./autogen.sh`
3. `./configure`
    1. Configure has some options that can be explored, such as prefix
       directory, see `./configure --help`
    2. The cache anomalies measure the cache size during this step, so it's
       important to run configure on the node that the anomaly is going to
       execute on.
    3. `LD_LIBRARY_PATH` and `CFLAGS` should indicate the location of `shmem.h` and
       relevant libraries. If using OpenMPI, use `oshcc` to compile (add
       `CC=oshcc` to `configure` arguments) and oshrun to run.
4. `make`
5. `make install`

More Details on Anomalies
-------------------------

The time units are specified in double precision floats, in seconds. Up to
microsecond granularity can be used. Each anomaly has a `--help` option, which
prints detailed information about command line arguments.

For detailed evaluation of anomalies, refer to our paper at ICPP'19, a copy is
included in `docs/ates_icpp19.pdf`.

#### Note for `netoccupy`
The `netoccupy` anomaly assumes that each node has a hostname of the form e.g.,
`nid00020`, such that each node name is `nid` followed by a unique integer. The
relevant line within `src/netoccupy.c` should be changed to support different
node naming conventions.


Usage of Anomalies
------------------

For SLURM systems, the anomalies can be injected into the application with the
following example line (with necessary modifications) in the job submission script:

```
srun --nodelist=$ANOMALOUS_NODE -N 1 -n 1 $PREFIX/bin/hpas cpuoccupy [--options] &
anomaly_pid=$!
```

For anomalies that stress a shared resource between nodes (I/O and network),
`$ANOMALOUS_NODE` should be different from application nodes, and it should be
one of the application nodes if the anomaly is a CPU, cache or memory anomaly.
The I/O anomalies can be executed with higher `-N` and `-n` values for more
interference, and the network anomaly has to be executed with `-N 2`.


Contributing
------------

This anomaly suite is not an exhaustive list of anomaly generators for HPC
systems. Therefore, we are open to additions of new anomalies. If you have an
anomaly generator you would like to contribute, please open a pull request/issue
at GitHub, and we can work together to include the anomalies.

For any bugs/problems, please open a pull request if you have a fix, issue
otherwise.

Using HPAS
-----

If you use this anomaly suite for a publication, please cite the following
paper: Ates et al., "HPAS: An HPC Performance Anomaly Suite for Reproducing
Performance Variations". To appear in 48th International Conference on Parallel
Processing (ICPP 2019).

Bibtex entry:
```
@inproceedings{ates:2019,
    author = {Emre Ates and Yijia Zhang and Burak Aksar and Jim Brandt and
              Vitus J. Leung and Manuel Egele and Ayse K. Coskun},
    title = {{HPAS}: An {HPC} Performance Anomaly Suite for Reproducing
             Performance Variations},
    booktitle = {48th International Conference on Parallel Processing
                 (ICPP 2019)},
    year = {2019},
}
```

License
-----

HPAS is licensed under the [BSD 3-Clause license](https://github.com/peaclab/HPAS/blob/master/LICENSE).

References
-----

[1] Emre Ates, Yijia Zhang, Burak Aksar, Jim Brandt, Vitus J. Leung, Manuel Egele, and Ayse K. Coskun. HPAS: An HPC Performance Anomaly Suite for Reproducing Performance Variations. In International Conference on Parallel Processing (ICPP), Aug. 2019
