# Minotaur: A SIMD-Oriented Synthesizing Superoptimizer

The Alive2 requires a LLVM compiled with RTTI and exceptions enabled, use the following command to fetch and build LLVM. The project requires `cmake`, `ninja-build`, `gcc-10`, `g++-10`, `redis`, `redis-server`, `libhiredis-dev`, `libbsd-resource-perl`, `libredis-perl`, `libgtest-dev`, and `re2c` as dependencies. Manually install the latest stable `z3`.

    sudo apt-get install cmake ninja-build gcc-10 g++-10 redis redis-server libhiredis-dev libbsd-resource-perl libredis-perl re2c libgtest-dev
    git clone git@github.com:zhengyang92/llvm $HOME/llvm
    mkdir $HOME/llvm/build && cd $HOME/llvm/build
    cmake -GNinja -DLLVM_ENABLE_RTTI=ON -DLLVM_ENABLE_EH=ON -DBUILD_SHARED_LIBS=ON -DCMAKE_BUILD_TYPE=RelWithDebInfo -DLLVM_TARGETS_TO_BUILD=X86 -DLLVM_ENABLE_ASSERTIONS=ON -DLLVM_ENABLE_PROJECTS="llvm;clang" ../llvm
    ninja

To fetch and build the Alive2 with X86 intrinsics, use the following command.

    git clone git@github.com:zhengyang92/alive2-x86 $HOME/alive2-x86
    mkdir $HOME/alive2-x86/build && cd $HOME/alive2-x86/build
    cmake -GNinja -DLLVM_DIR=$HOME/llvm/build/lib/cmake/llvm -DBUILD_TV=1 -DCMAKE_EXPORT_COMPILE_COMMANDS=1 -DCMAKE_BUILD_TYPE=RelWithDebInfo ..
    ninja

To build Minotaur, use the following command.

    git clone git@github.com:zhengyang92/minotaur $HOME/minotaur
    mkdir $HOME/minotaur/build && cd $HOME/minotaur/build
    cmake .. -DALIVE2_SOURCE_DIR=$HOME/alive2-x86 -DALIVE2_BUILD_DIR=$HOME/alive2-x86/build -DCMAKE_PREFIX_PATH=$HOME/llvm/build -DCMAKE_EXPORT_COMPILE_COMMANDS=1 -DCMAKE_BUILD_TYPE=RelWithDebInfo -G Ninja
    ninja

To run the test suite, use

    ninja check

To run the program synthesizer on llvm IR files, use the following command

    $HOME/llvm/build/bin/opt  -enable-new-pm=0 -load $HOME/minotaur/build/minotaur.so -so -S <LLVM bitcode>

For C/C++ programs, we have a drop-in replacement of C/C++ compiler. Users can call `minotaur-cc` or `minotaur-cxx` to compile files. The minotaur optimizer will automatically triggered as a compiler pass.

    $HOME/minotaur/build/minotaur-cc <c source> [clang options]
