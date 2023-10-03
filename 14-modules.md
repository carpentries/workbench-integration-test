---
title: "Accessing software via Modules"
teaching: 30
exercises: 15
---



::: questions
 - "How do we load and unload software packages?"
:::

::: objectives
 - "Understand how to load and use a software package."
:::

On a high-performance computing system, it is seldom the case that the software
we want to use is available when we log in. It is installed, but we will need
to "load" it before it can run.

Before we start using individual software packages, however, we should
understand the reasoning behind this approach. The three biggest factors are:

 - software incompatibilities
 - versioning
 - dependencies

Software incompatibility is a major headache for programmers. Sometimes the
presence (or absence) of a software package will break others that depend on
it. Two of the most famous examples are Python 2 and 3 and C compiler versions.
Python 3 famously provides a `python` command that conflicts with that provided
by Python 2. Software compiled against a newer version of the C libraries and
then used when they are not present will result in a nasty `'GLIBCXX_3.4.20' not found` error, for instance.

Software versioning is another common issue. A team might depend on a certain
package version for their research project - if the software version was to
change (for instance, if a package was updated), it might affect their results.
Having access to multiple software versions allow a set of researchers to
prevent software versioning issues from affecting their results.

Dependencies are where a particular software package (or even a particular
version) depends on having access to another software package (or even a
particular version of another software package). For example, the VASP
materials science software may depend on having a particular version of the
FFTW (Fastest Fourier Transform in the West) software library available for it
to work.

## Environment Modules

Environment modules are the solution to these problems. A *module* is a
self-contained description of a software package --- it contains the
settings required to run a software package and, usually, encodes required
dependencies on other software packages.

There are a number of different environment module implementations commonly
used on HPC systems: the two most common are *TCL modules* and *Lmod*. Both of
these use similar syntax and the concepts are the same so learning to use one
will allow you to use whichever is installed on the system you are using. In
both implementations the `module` command is used to interact with environment
modules. An additional subcommand is usually added to the command to specify
what you want to do. For a list of subcommands you can use `module -h` or
`module help`. As for all commands, you can access the full help on the *man*
pages with `man module`.

On login you may start out with a default set of modules loaded or you may
start out with an empty environment; this depends on the setup of the system
you are using.

### Listing Available Modules

To see available software modules, use `module avail`:

```bash
userid@uan01:/work/ta114/ta114/userid> module avail
```

```output
----------- /work/y07/shared/archer2-modules/modulefiles-cse-pyvenvs -----------
tensorflow/2.3.1-py38  torch/1.6.0-py38  

----------- /work/y07/shared/archer2-modules/modulefiles-cse-pymods ------------
python-netCDF4/1.5.5.1  

------------ /work/y07/shared/archer2-modules/modulefiles-cse-utils ------------
bolt/0.7                  ncview/ncview-2.1.7-gcc-10.1.0  vmd/1.9.3-mpi-gcc10  
cmake/3.18.4              reframe/3.2                     xios/2.5-gcc10       
ed/1.16-gcc10             tcl/8.4.20-gcc10                xthi/1.0             
epcc-job-env              tcl/8.5.0-gcc10                 xthi/1.0-gcc10       
epcc-reframe/0.1          tcl/8.6.0-gcc10                 
genmaskcpu/1.0            tcl/8.6.10-gcc10(default)       
gnuplot/5.4.1-gcc-10.1.0  tk/8.5.6-gcc10                  
lzip/1.20-gcc10           tk/8.6.10-gcc10(default)        
nco/4.9.6                 visidata/2.1                    
nco/4.9.6-gcc-10.1.0      vmd/1.9.3-gcc10(default)        

------------ /work/y07/shared/archer2-modules/modulefiles-cse-libs -------------
adios/1.13.1     hypre/2.18.0             mumps/5.2.1     superlu-dist/6.1.1  
boost/1.72.0     libxml2/2.9.7-gcc-9.3.0  parmetis/4.0.3  superlu/5.2.1       
glm/0.9.9.6      matio/1.5.18             petsc/3.13.3    trilinos/12.18.1    
gmp/6.1.2-gcc10  metis/5.1.0              scotch/6.0.10   
...
```

### Listing Currently Loaded Modules

You can use the `module list` command to see which modules you currently have
loaded in your environment. If you have no modules loaded, you will see a
message telling you so

```bash
userid@uan01:/work/ta114/ta114/userid> module list
```
```output
Currently Loaded Modulefiles:
 1) cpe-cray                          8) perftools-base/20.10.0(default)                                  
 2) cce/10.0.4(default)               9) xpmem/2.2.35-7.0.1.0_1.9__gd50fabf.shasta(default)               
 3) craype/2.7.2(default)            10) cray-mpich/8.0.16(default)                                       
 4) craype-x86-rome                  11) cray-libsci/20.10.1.2(default)                                   
 5) libfabric/1.11.0.0.233(default)  12) bolt/0.7                                                         
 6) craype-network-ofi               13) /work/y07/shared/archer2-modules/modulefiles-cse/epcc-setup-env  
 7) cray-dsmml/0.1.2(default)        14) /usr/local/share/epcc-module/epcc-module-loader  
```

## Loading and Unloading Software

To load a software module, use `module load`. Let's say we would like
to use the NetCDF utility `ncdump`. 

On login, `ncdump` is not available. We can test this by using the `which`
command. `which` looks for programs the same way that Bash does,
so we can use it to tell us where a particular piece of software is stored.

```bash
 which ncdump
```
```output
which: no ncdump in (/usr/local/maven/bin:/lus/cls01095/work/y07/shared/bolt/0.7/bin:/work/y07/shared/utils/bin:/opt/cray/pe/perftools/20.10.0/bin:/opt/cray/pe/papi/6.0.0.4/bin:/opt/cray/libfabric/1.11.0.0.233/bin:/opt/cray/pe/craype/2.7.2/bin:/opt/cray/pe/cce/10.0.4/cce-clang/x86_64/bin:/opt/cray/pe/cce/10.0.4/binutils/x86_64/x86_64-pc-linux-gnu/bin:/opt/cray/pe/cce/10.0.4/binutils/cross/x86_64-aarch64/aarch64-linux-gnu/../bin:/opt/cray/pe/cce/10.0.4/utils/x86_64/bin:/usr/local/Modules/bin:/usr/local/bin:/usr/bin:/bin:/opt/cray/pe/bin:/usr/lib/mit/bin)
```

We can find the `ncdump` command by using `module load`:

```bash
 module load cray-hdf5
 module load cray-netcdf
which ncdump
```
```output
/opt/cray/pe/netcdf/4.7.4.2/bin/ncdump
```

So, what just happened?

To understand the output, first we need to understand the nature of the
`$PATH` environment variable. `$PATH` is a special environment variable
that controls where a UNIX system looks for software. Specifically,
`$PATH` is a list of directories (separated by `:`) that the OS searches
through for a command before giving up and telling us it can't find it.
As with all environment variables we can print it out using `echo`.

```bash
 echo $PATH
```
```output
/opt/cray/pe/netcdf/4.7.4.2/bin:/opt/cray/pe/python/3.8.5.0/bin:/lus/cls01095/work/z19/z19/aturner/.local/bin:/lus/cls01095/work/y07/shared/bolt/0.7/bin:/work/y07/shared/utils/bin:/usr/local/maven/bin:/opt/cray/pe/perftools/20.10.0/bin:/opt/cray/pe/papi/6.0.0.4/bin:/opt/cray/libfabric/1.11.0.0.233/bin:/opt/cray/pe/craype/2.7.2/bin:/opt/cray/pe/cce/10.0.4/cce-clang/x86_64/bin:/opt/cray/pe/cce/10.0.4/binutils/x86_64/x86_64-pc-linux-gnu/bin:/opt/cray/pe/cce/10.0.4/binutils/cross/x86_64-aarch64/aarch64-linux-gnu/../bin:/opt/cray/pe/cce/10.0.4/utils/x86_64/bin:/usr/local/Modules/bin:/home/z19/z19/aturner/bin:/usr/local/bin:/usr/bin:/bin:/opt/cray/pe/bin:/usr/lib/mit/bin
```

You'll notice a similarity to the output of the `which` command. In this case,
there's only one difference: the different directory at the beginning. When we
ran the `module load` command, it added a directory to the beginning of our
`$PATH`. Let's examine what's there:

```bash
 ls /opt/cray/pe/netcdf/4.7.4.2/bin
```
```output
nc-config  nccopy  ncdump  ncgen  ncgen3  ncxx4-config  nf-config
```

In summary, `module load` will add software to your `$PATH`.
`module load` may also load additional modules with software dependencies.

To unload a module, use `module unload` with the relevant module name.

::: challenge
## Unload!
Confirm you can unload the `cray-netcdf` module and check what happens to the `PATH` environment variable.
:::

## Software versioning

So far, we've learned how to load and unload software packages. This is very useful. However, we
have not yet addressed the issue of software versioning. At some point or other, you will run into
issues where only one particular version of some software will be suitable. Perhaps a key bugfix
only happened in a certain version, or version X broke compatibility with a file format you use. In
either of these example cases, it helps to be very specific about what software is loaded.

Let's examine the output of `module avail` more closely.

```bash
 module avail cray-netcdf
```
```output
--------------------------- /opt/cray/pe/modulefiles ---------------------------
cray-netcdf-hdf5parallel/4.7.4.0           cray-netcdf/4.7.4.0           
cray-netcdf-hdf5parallel/4.7.4.2(default)  cray-netcdf/4.7.4.2(default)  
```

Note that we have two different versions of `cray-netcdf` (and also two
versions of something else `cray-netcdf-hdf5parallel` which match our
search).

::: challenge
## Using `module swap`
Load module `cray-netcdf` as before. Note that if we do not specifify
a particular version, we load a default version.
If we wish to change versions, we can use
`module swap <old-module> <new-module>`. Try this to obtain
`cray-netcdf/4.7.4.0`. Check what has happened to the location of
the `ncdump` utility.
:::

::: challenge
## Using Software Modules in Scripts
Create a job that is able to run `ncdump --version`. Running a job
is just like logging on to the system
(you should not assume a module loaded on the login node is loaded on a
compute node).

::: solution

```bash
userid@uan01:/work/ta114/ta114/userid> nano ncdump-module.sh
userid@uan01:/work/ta114/ta114/userid> cat ncdump-module.sh
```
```output
#!/bin/bash
#SBATCH --partition=standard
#SBATCH --qos=short
#SBATCH --reservation=shortqos
module load epcc-job-env
module load cray-netcdf
ncdump --version
```

```bash
userid@uan01:/work/ta114/ta114/userid>  python-module.sh
```
:::
:::

::: keypoints
 - "Load software with `module load softwareName`."
 - "Unload software with `module purge`"
 - "The module system handles software versioning and package conflicts for you
  automatically."
:::
