# 🚧🚧🚧 This page is still very much under construction 🚧🚧🚧


## Specification
This is the specification for the programming language TLM2 (or TLMlang 2)
### General
TLM2 is based on TLMlang (Trans lives matter language) from May'24.
The '24 TLMlang is a stack-based, two-dimensional programming language focused on meta programming. Inspirations include Brainfuck, Befunge and cellular automata.

TLM2 is a complete rework of the original language, which include some of the old features (but not all) and many new. The source code for the simplest TLM2 program (which could be saved in a `.tlm` file to be interpreted) looks like this:
```
{main
.
}
```
Every TLM2 program has to contain a `main` function, where the instruction pointer starts at the top-left corner (the entry point), with the instruction-flow set to `(1, 0)`. In the above program the instruction pointer starts at the `.` (no-op) instruction, executes it, moves the instruction pointer one space to the right, which exits the function. The program then terminates. A little more advaced program could look like this
```
{main
11AB
}
```
As before, the instruction pointer starts on the leftmost instruction and moves to the right. The instructions executed are (in order)
* `1` - Push the number 1 to the stack
* `1` - Push the number 1 to the stack
* `A` - Pop the topmost two values (a and b) from the stack and push a+b (in this case 1+1=2) to the stack
* `B` - Pop the topmost value from the stack and write it to the output.

The program then, again, exits the main function on the right and the program halts.

A (functionally) identical program to the one above, which demonstrates the two-dimensional nature of the language is
```
{main
11D.
..A.
..B.
}
```
The only new instruction is `D` (down), which changes the instruction-flow from `(1, 0)` to `(0, 1)`. The instruction pointer starts moving downwards after executing it, executes the add and write instructions and then exits the main function on the bottom.

TLM2 is also able to modify its own source code, demonstrated with the following program
```
{main
1WL
}
```
After pushing 1 to the stack, the `W` instruction pops the topmost value off the stack and writes it in its stead into the source code. The body of the main function now looks like this `11L` and after getting redirected by the `L` (left) instruction, *two* 1s get pushed to the stack and the program exits the main function on the lefthand side.

### Control flow
Control flow can be changed direction via the `U`, `D`, `L`, `R` (up, down, left, right) instructions and via the `O` (conditional redirect) instruction. The conditional redirect rotates the instruction-flow vector depending on the value on top of the stack. A value greater than `0` rotates the flow clockwise, less than `0` rotates counter-clockwise and a value of `0` leaves flow unaffected.

More things here??

### The stack
yay

### Functions
yay

### Registers
yay

### Nice to have
* mark function as "clean" (does not modify its own source code in any way), could be very efficient to save its state (as it's just the instruction pointer position and flow) when going into other functions or doing recursion
* mark function as "persistant" (exiting it does not reset any source-code modification) (what about recursion?)
* ?
