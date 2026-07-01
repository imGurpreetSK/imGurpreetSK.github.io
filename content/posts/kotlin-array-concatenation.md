+++
date = '2026-07-01T00:32:19-07:00'
draft = false
title = 'Why the stdlib probably already does the best thing - an array concatenation driven learning'
author = "Gurpreet"
tags = ["neetcode", "TIL"]
+++

Here's what I was solving: [Concatenation of Arrays](https://neetcode.io/problems/concatenation-of-array/question?list=neetcode250)

Three solutions:
    1. One was returning `input` + `input` array.
    2. The second was to allocate an intermediate array of size 2n and iterate through the input by index, putting each element at the correct indices.
    3. Finally, using `copyInto` to make system call for moving memory blocks.

All of these use O(n) space for holding the result, O(n) for the iteration (eventually under the hood as well, I thought). I did not prefer the first approach because it felt like cheating and I also did not know if it's performant enough - I was rather sure that it wouldn't be.

Submitted each. Got surprised!

The simpler solution was far more performant than my loop, and marginally better than using the system call - it had a runtime of 82 milliseconds and was faster than 91% of all submissions versus 93 milliseconds and beat 52% for loop. Here's why:

With iteration approach, the system moves each element into the new array one at a time. This is highly inefficient as it wastes CPU resources in trips; it might get worse with an increase in input size due to cache misses and round-trips to system memory.

The approach with `copyInto` is much more interesting. It internally creates a new array of required size calls into `system.arraycopy` native function (a JVM intrinsic). It moves the required memory chunk directly at the correct place instead of iterating over individual items. Till now, it is quite evident on why it is more performant. However, why is the first approach the simplest and marginally more performant than this? The answer lies in the number of instructions that need to be run by the system - looking at the bytecode, the stack is populated twice with the input array and the system copy is executed in first approach. Compare this to using `copyInto`, where an array is created then loaded into memory and then the input array is pushed into the correct places.

Looking at the bytecode explains the difference in executable code and explains the (marginal) gains.

1. `input` + `input`:
    ```bytecode
    public final getConcatenation([I)[I
    L0
        ALOAD 1
        LDC "nums"
        INVOKESTATIC kotlin/jvm/internal/Intrinsics.checkNotNullParameter (Ljava/lang/Object;Ljava/lang/String;)V
    L1
        LINENUMBER 17 L1
        ALOAD 1
        ALOAD 1
        INVOKESTATIC kotlin/collections/ArraysKt.plus ([I[I)[I
        ARETURN
    L2
        LOCALVARIABLE this Larrays/ConcatenationOfArray; L0 L2 0
        LOCALVARIABLE nums [I L0 L2 1
        MAXSTACK = 2
        MAXLOCALS = 2
    }
    ```

2. `copyInto`:
    ```bytecode
    public final getConcatenation([I)[I
    L0
        ALOAD 1
        LDC "nums"
        INVOKESTATIC kotlin/jvm/internal/Intrinsics.checkNotNullParameter (Ljava/lang/Object;Ljava/lang/String;)V
    L1
        LINENUMBER 19 L1
        ALOAD 1
        ARRAYLENGTH
        ICONST_2
        IMUL
        NEWARRAY T_INT
        ASTORE 2
    L2
        LINENUMBER 26 L2
        ALOAD 1
        ALOAD 2
        ICONST_0
        ICONST_0
        ICONST_0
        BIPUSH 14
        ACONST_NULL
        INVOKESTATIC kotlin/collections/ArraysKt.copyInto$default ([I[IIIIILjava/lang/Object;)[I
        POP
    L3
        LINENUMBER 27 L3
        ALOAD 1
        ALOAD 2
        ALOAD 1
        ARRAYLENGTH
        ICONST_0
        ICONST_0
        BIPUSH 12
        ACONST_NULL
        INVOKESTATIC kotlin/collections/ArraysKt.copyInto$default ([I[IIIIILjava/lang/Object;)[I
        POP
    L4
        LINENUMBER 29 L4
        ALOAD 2
        ARETURN
    L5
        LOCALVARIABLE result [I L2 L5 2
        LOCALVARIABLE this Larrays/ConcatenationOfArray; L0 L5 0
        LOCALVARIABLE nums [I L0 L5 1
        MAXSTACK = 7
        MAXLOCALS = 3
    }
    ```
