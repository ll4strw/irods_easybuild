**irods_easybuild** provides instructions and a collection of EasyBuild
config files to build [iRODS](https://irods.org) using 
[EasyBuild](https://easybuild.io/).

EasyBuild is a software build and installation framework that allows you to manage (scientific) software on High Performance Computing (HPC) systems in an efficient way.

The Integrated Rule-Oriented Data System (iRODS) is open source data management software used by research, commercial, and governmental organizations worldwide.

### Advantages

Automated and **reproducible** way to install iRODS (or any of its components) and its external dependencies on any GNU/Linux system.
For instance, think of the deployment of `iRODS-icommands` on any GNU/Linux distribution.

If you are truly interested in _bit-to-bit_ reproducibility, please take a look at the [GNU Guix](https://guix.gnu.org/) project and install `iRODS` via

```
guix package -i  irods-client-icommands
```
At the time of writing, only `iRODS v4.2.8` is available.

### Installation

- Install and setup EasyBuild as described [here](https://docs.easybuild.io/installation/)
- Clone this repository

```
git clone git@github.com:ll4strw/irods_easybuild.git
```
- Install iRODS or only iRODS client libraries

```
ml load EasyBuild
cd irods_easybuild
eb --robot-paths=":./easyconfigs" ./easyconfigs/i/irods/irods-4.3.0-GCC-11.2.0.eb [--dry-run] [--try-amend=build_cmd='make irods_client '] -r
```

- Install iRODS-icommands

```
eb --robot-paths=":./easyconfigs" ./easyconfigs/i/irods-icommands/irods-icommands-4.3.0-GCC-11.2.0.eb  [--dry-run] -r
```

#### Clang, GCC, libstdc++ and libc++

The **current** setup builds iRODS successfully using `Clang` and `libstdc++`

```
# cat irods-4.3.0-GCC-11.2.0.eb
configopts = " -DIRODS_BUILD_WITH_CLANG=ON "
configopts += " -DIRODS_BUILD_AGAINST_LIBCXX=FALSE "
configopts += " -DCMAKE_SHARED_LINKER_FLAGS='-fuse-ld=lld' "
configopts += " -DCMAKE_EXE_LINKER_FLAGS='-fuse-ld=lld' "
configopts += " -DCMAKE_MODULE_LINKER_FLAGS='-fuse-ld=lld' "

```

Is it nonetheless possible to build iRODS and icommands using `GCC` instead of `Clang` by using

```
# cat irods-4.3.0-GCC-11.2.0.eb
configopts = " -DIRODS_BUILD_WITH_CLANG=OFF "
configopts += " -DIRODS_BUILD_AGAINST_LIBCXX=FALSE "
#configopts += " -DCMAKE_SHARED_LINKER_FLAGS='-fuse-ld=lld' "
#configopts += " -DCMAKE_EXE_LINKER_FLAGS='-fuse-ld=lld' "
#configopts += " -DCMAKE_MODULE_LINKER_FLAGS='-fuse-ld=lld' "
```

However this approach leads to the following error when invoking the icommands

```
ils 
JSON error occurred while authenticating user [****] [[json.exception.type_error.305] cannot use operator[] with a string argument with array] failed with error -167000 SYS_LIBRARY_ERROR
```


Similarly, you can also try building using `libc++` instead of `libstdc++`

```
# cat irods-4.3.0-GCC-11.2.0.eb
configopts += " -DIRODS_BUILD_AGAINST_LIBCXX=TRUE "
```

but this approach could lead to similar linking error

```
ld.lld: error: undefined symbol: fmt::v8::vformat(fmt::v8::basic_string_view<char>, fmt::v8::basic_format_args<fmt::v8::basic_format_context<fmt::v8::appender, char> >)
```

unless you compile all dependencies with `libc++`.

### Test

```
ml purge
ml load irods-icommands/4.3.0-GCC-11.2.0
iinit
```

