# Dynamic Immediate Representation 
A virtual assembly language that will be used to make a VM for our CS class and is being used for pre-LLVM optimization in the Tontu compiler

# What it is
This language is represented as an array of "instruction" data structures that have this definition:
```c
typedef struct instruction_struct {
  int id;
  int args_len;
  variable **args;
} instruction;
```
In case you're wondering, this is what the "variable" data structure definition looks like:
```c
typedef struct variable_struct {
  char *name;
  char *type;
  int memtype; // stack=0, heap=1, SSA=2
  int scope;
  int *scope_to_destroy; // pointer to the scope value unless
                         // it's heap-allocated with an extended lifetime 
} variable;
```

# How it looks
This is some code in the Nisse language:
```rust
fn test_f(a=7, b=9){
  hello = (6 + 7) * 3 + 6
  hello *= 7 + 8
  hello += 9
  ret hello
}

fn main(argc, argv){
  test_f(4, 1)
  ret 0
}
```
This is the DynIR that the Tontu compiler produces:
```asm
ARG DEF: 
INIT: a = 7
INIT: b = 9
a
b
DEFINE: test_f
NEW SCOPE
NEW SSA: 6 + 7
SSA: SSA * 3
SSA: SSA + 6
INIT: hello = SSA
NEW SSA: 7 + 8
NEW SSA: hello * SSA
ASSIGN: hello = SSA
NEW SSA: hello + 9
ASSIGN: hello = SSA
RETURN: hello
END SCOPE
ARG DEF: 
argc
argv
DEFINE: main
NEW SCOPE
ARG DEF
4
1
FCALL: test_f
RETURN: 0
END SCOPE
```
# Quick note
This project uses C-style C++, so other than the LLVM library usage, all the logic should also be valid in C.
# Credits
Created by s1santhosh, alange12, xmm16, and namithchelikani
