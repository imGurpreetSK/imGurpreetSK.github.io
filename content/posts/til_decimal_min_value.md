+++
date = '2026-09-29T14:06:40-07:00'
draft = false
title = 'TIL: Float.MIN_VALUE and Double.MIN_VALUE are not the most negative values'
author = "Gurpreet"
tags = ["kotlin"]
+++

Float.MIN_VALUE and Double.MIN_VALUE are the smallest positive values, not the most negative. The most negative value is -Double.MAX_VALUE.

The name MIN_VALUE means different things for whole numbers and decimals:

- Whole numbers: Int.MIN_VALUE is the most negative value, -2,147,483,648.
- Decimals: Double.MIN_VALUE is the tiny positive number closest to zero, 4.9E-324 (0.000…0049 with 323 zeros after the decimal point). It is **not** negative.

So for Double, the "min" is about how close to zero it can get, not how low it can go. This matters when you search for a maximum:

```kotlin
var best = Double.MIN_VALUE        // Bug: this is 4.9E-324, a positive number
for (x in doubleArrayOf(-5.0, -2.0)) {
    best = maxOf(best, x)
}
println(best)                      // 4.9E-324, not -2.0
```

Every negative input is smaller than this starting value, so it never gets replaced. The fix is to start from -Double.MAX_VALUE (the most negative finite Double) or Double.NEGATIVE_INFINITY. Int.MIN_VALUE doesn't have this problem, because for Int it really is the lowest value.
