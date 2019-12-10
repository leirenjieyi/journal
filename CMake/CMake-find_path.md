## find_path 查找目录

    find_path (<VAR> name1 [path1 path2 ...])

    find_path (
          <VAR>
          name | NAMES name1 [name2 ...]
          [HINTS path1 [path2 ... ENV var]]
          [PATHS path1 [path2 ... ENV var]]
          [PATH_SUFFIXES suffix1 [suffix2 ...]]
          [DOC "cache documentation string"]
          [NO_DEFAULT_PATH]
          [NO_CMAKE_ENVIRONMENT_PATH]
          [NO_CMAKE_PATH]
          [NO_SYSTEM_ENVIRONMENT_PATH]
          [NO_CMAKE_SYSTEM_PATH]
          [CMAKE_FIND_ROOT_PATH_BOTH |
           ONLY_CMAKE_FIND_ROOT_PATH |
           NO_CMAKE_FIND_ROOT_PATH]
         )


This command is used to find a directory containing the named file. A cache entry named by <VAR> is created to store the result of this command. If the file in a directory is found the result is stored in the variable and the search will not be repeated unless the variable is cleared. If nothing is found, the result will be <VAR>-NOTFOUND, and the search will be attempted again the next time find_path is invoked with the same variable.

此命令用于查找包含指定文件的目录。创建一个由<var>命名的缓存条目来存储此命令的结果。如果找到目录中的文件，则结果存储在变量中，并且返回，不再向下搜索。如果什么都没有找到，结果将是<var>-NotFound，下次使用相同的变量调用find_path时，将再次尝试搜索。

Options include:

NAMES

    Specify one or more possible names for the file in a directory.

    When using this to specify names with and without a version suffix, we recommend specifying the unversioned name first so that locally-built packages can be found before those provided by distributions.
HINTS, PATHS
    Specify directories to search in addition to the default locations. The ENV var sub-option reads paths from a system environment variable.
PATH_SUFFIXES
    Specify additional subdirectories to check below each directory location otherwise considered.
DOC
    Specify the documentation string for the <VAR> cache entry.

If NO_DEFAULT_PATH is specified, then no additional paths are added to the search. If NO_DEFAULT_PATH is not specified, the search process is as follows:

    Search paths specified in cmake-specific cache variables. These are intended to be used on the command line with a -DVAR=value. This can be skipped if NO_CMAKE_PATH is passed.
        <prefix>/include/<arch> if CMAKE_LIBRARY_ARCHITECTURE is set, and <prefix>/include for each <prefix> in CMAKE_PREFIX_PATH
        CMAKE_INCLUDE_PATH
        CMAKE_FRAMEWORK_PATH
    Search paths specified in cmake-specific environment variables. These are intended to be set in the user’s shell configuration. This can be skipped if NO_CMAKE_ENVIRONMENT_PATH is passed.
        <prefix>/include/<arch> if CMAKE_LIBRARY_ARCHITECTURE is set, and <prefix>/include for each <prefix> in CMAKE_PREFIX_PATH
        CMAKE_INCLUDE_PATH
        CMAKE_FRAMEWORK_PATH
    Search the paths specified by the HINTS option. These should be paths computed by system introspection, such as a hint provided by the location of another item already found. Hard-coded guesses should be specified with the PATHS option.
    Search the standard system environment variables. This can be skipped if NO_SYSTEM_ENVIRONMENT_PATH is an argument.
        Directories in INCLUDE, <prefix>/include/<arch> if CMAKE_LIBRARY_ARCHITECTURE is set, and <prefix>/include for each <prefix>/[s]bin in PATH, and <entry>/include for other entries in PATH, and the directories in PATH itself.
    Search cmake variables defined in the Platform files for the current system. This can be skipped if NO_CMAKE_SYSTEM_PATH is passed.
        <prefix>/include/<arch> if CMAKE_LIBRARY_ARCHITECTURE is set, and <prefix>/include for each <prefix> in CMAKE_SYSTEM_PREFIX_PATH
        CMAKE_SYSTEM_INCLUDE_PATH
        CMAKE_SYSTEM_FRAMEWORK_PATH
    Search the paths specified by the PATHS option or in the short-hand version of the command. These are typically hard-coded guesses.

On OS X the CMAKE_FIND_FRAMEWORK and CMAKE_FIND_APPBUNDLE variables determine the order of preference between Apple-style and unix-style package components.

The CMake variable CMAKE_FIND_ROOT_PATH specifies one or more directories to be prepended to all other search directories. This effectively “re-roots” the entire search under given locations. Paths which are descendants of the CMAKE_STAGING_PREFIX are excluded from this re-rooting, because that variable is always a path on the host system. By default the CMAKE_FIND_ROOT_PATH is empty.

The CMAKE_SYSROOT variable can also be used to specify exactly one directory to use as a prefix. Setting CMAKE_SYSROOT also has other effects. See the documentation for that variable for more.

These variables are especially useful when cross-compiling to point to the root directory of the target environment and CMake will search there too. By default at first the directories listed in CMAKE_FIND_ROOT_PATH are searched, then the CMAKE_SYSROOT directory is searched, and then the non-rooted directories will be searched. The default behavior can be adjusted by setting CMAKE_FIND_ROOT_PATH_MODE_INCLUDE. This behavior can be manually overridden on a per-call basis using options:

CMAKE_FIND_ROOT_PATH_BOTH
    Search in the order described above.
NO_CMAKE_FIND_ROOT_PATH
    Do not use the CMAKE_FIND_ROOT_PATH variable.
ONLY_CMAKE_FIND_ROOT_PATH
    Search only the re-rooted directories and directories below CMAKE_STAGING_PREFIX.

The default search order is designed to be most-specific to least-specific for common use cases. Projects may override the order by simply calling the command multiple times and using the NO_* options:

find_path (<VAR> NAMES name PATHS paths... NO_DEFAULT_PATH)
find_path (<VAR> NAMES name)

Once one of the calls succeeds the result variable will be set and stored in the cache so that no call will search again.

When searching for frameworks, if the file is specified as A/b.h, then the framework search will look for A.framework/Headers/b.h. If that is found the path will be set to the path to the framework. CMake will convert this to the correct -F option to include the file.