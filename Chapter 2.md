# Chapter 2

### help

```bash
(lldb) help # 명령어들 나열
(lldb) help breakpoint # <command>에 대한 help
(lldb) help breakpoint name # <subcommand>에 대한 help
```

### apropos

명령어가 기억 안 날 때 연관검색어로 검색하기

```bash
(lldb) apropos swift
The following commands may relate to 'swift':
  swift    -- A set of commands for operating on the Swift Language Runtime.
  demangle -- Demangle a Swift mangled name
  refcount -- Inspect the reference count data for a Swift object
  dclass   -- Dumps info about objc/swift classes

The following settings variables may relate to 'swift': 


  target.swift-extra-clang-flags -- Additional -Xcc flags to be passed to the Swift ClangImporter.
  target.swift-framework-search-paths -- List of directories to be searched when locating
                                         frameworks for Swift.
  target.swift-module-search-paths -- List of directories to be searched when locating modules for
                                      Swift.
  target.use-all-compiler-flags -- Try to use compiler flags for all modules when setting up the
                                   Swift expression parser, not just the main executable.
  target.experimental.swift-create-module-contexts-in-parallel -- Create the per-module Swift AST
                                                                  contexts in parallel.
  symbols.swift-module-loading-mode -- The module loading mode to use when loading modules for
                                       Swift.
  symbols.use-swift-clangimporter -- Reconstruct Clang module dependencies from headers when
                                     debugging Swift code
  symbols.use-swift-dwarfimporter -- Reconstruct Clang module dependencies from DWARF when
                                     debugging Swift code
```

하지만 아래와 같은 검색어는 결과가 안 나옴. 왜냐하면 apropos는 argument만 검색해주기 때문

```bash
(lldb) apropos "reference count"
The following commands may relate to 'reference count':
  refcount -- Inspect the reference count data for a Swift object
```



