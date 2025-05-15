# 🚧 This page is still very much under construction 🚧
This is the (informal) specification for the programming language TLM2 (or TLMlang 2)

## Contents
* [General](#general)
* [Instructions](#instructions)
* [Control flow](#control-flow)
* [The stack](#the-stack)
* [Functions](#functions)
  * [Function modifiers](#function-modifiers)
    * [Persistent functions](#persistent-functions)
    * [Clean functions](#clean-functions)
* [Registers](#registers)
* [Meta programming](#meta-programming)
* [Ideas](#ideas)

## General
TLM2 is based on TLMlang (Trans lives matter language) from May'24.
The '24 TLMlang is a stack-based, two-dimensional programming language focused on meta programming. Inspirations include Brainfuck, Befunge and cellular automata.

TLM2 is a complete rework of the original language, which includes some of the old features (but not all) and many new. The source code for the simplest TLM2 program (which could be saved in a `.tlm` file to be interpreted) looks like this:
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
1SL
}
```
After pushing 1 to the stack, the `S` instruction pops the topmost value off the stack and writes it in its stead into the source code. The body of the main function now looks like this `11L` and after getting redirected by the `L` (left) instruction, *two* 1s get pushed to the stack and the program exits the main function on the lefthand side.

## Instructions
Every instruction in TLM2 is a single character. Excluding function modifiers, there are 63 valid instructions. Namely lowercase letters (`a`..`z`), uppercase letters (`A`..`Z`) and digits (`0`..`9`) and the no-op dot (`.`). If any other character is in the body of a function, the source code no longer describes a valid TLM2 program.

## Control flow
Control flow always starts in the top left corner of the `main` function, moving to the right. If control flow makes the instruction pointer leave the bounds of a function, this function is exited. If the `main` function is exited, the program halts.

Control flow can be changed directly via the `U`, `D`, `L`, `R` (up, down, left, right) instructions and via the `O` (conditional redirect) instruction. The conditional redirect rotates the instruction-flow vector depending on the value on top of the stack. A value greater than 0 rotates the flow clockwise, less than 0 rotates counter-clockwise and a value of 0 leaves flow unaffected.

The following program goes in a loop forever
```
{main
R..D
....
U..L
}
```
The following program uses a counter to go in a loop 10 times, using the value on top of the stack as a counter. `1NA` pushes 1 to the stack, negates it, then adds the top two values on the stack - effectively a decrement operation. This will happen as long as the `O` instruction keeps redirecting control-flow downwards (and thereby back into the loop "body"). As soon as the value on top of the stack hits 0, control-flow at the `O` instruction will be unaffected and leave the main function to the right.
```
{main
55A.....R....1NA..O...
......................
........U.........L...
}
```

more things here???

## The stack
TLM2 is a stack based language and as such there are several operations that modify the stack in one way or another. The stack can be accessed from any function at any time. The stack contains integers only, however these integers might be interpreted as symbols by writing them to the output or by putting them into source-code via the meta-programming related instructions.

## Functions
Functions are blocks of code enclosed within curly braces. The first line of a function contains its identifier and signature, the last line is the closing curly brace. Between the first and last line lies the body of the function. Every line of the body of a function must have the same number of characters.

Control flow can "leave" the main function by going to a different function. Function names are single lowercase letters (`a` through `z`). The instruction to enter a function is its name. When entering a function, the instruction pointer starts (as it does with the `main` function) at the top left corner and moves to the right. Leaving a function causes the instruction pointer to return to the place where it entered it. Leaving `main` halts the program.

The following program enters the function `f` twice - each time pushing the number 1 to the stack.
```
{main
ff
}
{f
1
}
```
Control flow is not preserved when exiting a function, so even if the body of the function of `f` was `1D`, the program would functionally be identical.

## Function modifiers
Function modifiers directly follow the identifier of the function and are listed in square brackets `[...]` seperated by semi-colons `;`. If a modifier takes arguments, they are listed within parenthesis `()` seperated by commas `,`.
```
{f[!;%;$(a,b,c)]
.
}
```

## Persistent functions
Normally, when exiting a function by going out of bounds, the function body is reset to its original state and all modifications done with to meta-programming are reverted. Persistent functions do not do this. A function can be marked as persistent using the `!` function modifier.

Take the following program as an example
```
{main
ff
}
{f[!]
1S
}
```
When the program enters the function `f` for the first time, 1 is pushed to the stack and put into the place of the `S`. The function is left with an empty stack. The next time the program enters `f`, *two* 1s are pushed to the stack before the function is exited again.

## Clean functions
Functions that don't contain any meta-programming instructions are "clean". Clean functions are persistent. Functions can optionally be marked as clean with the `%` function modifier as a hint for yourself and the interpreter or compiler (as if there will ever be a compiler, lol). Functions that do contain meta-programming instructions are "dirty".

## Registers
TLM2, as opposed to TLM, has registers to write to and read from; the X and Y registers can be written to via the `X` and `Y` instructions and can be read from via the `U` and `V` instructions respectively. Registers contain, as the stack does, integers.

## Meta programming
There are several ways to do meta-programming in TLM2. The most straightforward is the `S` instruction. As example, take the following program
```
{main
1111SSSS
}
```
Four 1s will be pushed to the stack and then written to source code. Right before exiting `main` with an empty stack, the source code will look like this
```
{main
11111111
}
```


## Ideas
* discard can be implemented with a simple function
```
{d
S
}
```
* overwrite old code somehow, because using `S` can only write in its stead, not overwrite other things (positional write? write block?)
* strings/lambdas on the stack
* pointers
* self-modification: bind symbols to output of a tlm2 program?
* output is 2d as well
* instead of registers, have "bags", retrieving an item from a bag returns a random item
* each point in TLM2 tracks how it was changed over time-allowing to time travel back and forth
* first function is main

If the output of a TLM2 program is a 2D area of characters, then the output can be another valid TLM2 program







