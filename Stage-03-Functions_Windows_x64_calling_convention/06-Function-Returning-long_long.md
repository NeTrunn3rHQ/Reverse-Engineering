# Program 06 — Function Returning long long

## 1. Objective

Understand how 64-bit integer values are stored, manipulated, and returned through RAX in Windows x64.

## 2. Original C

```
#include <stdio.h>

long long calculate(void)
{
    long long a = 5000000000LL;
    long long b = 3000000000LL;

    return a + b;
}

int main(void)
{
    long long result = calculate();
    printf("Result: %lld\n", result);
    return 0;
}
```

## 3. Behavioral prediction

- **Expected output:** `Result: 8000000000`
    
- **Expected return value:** `calculate()` returns 8000000000; `main()` returns 0.
    
- **Important values:** `a = 5000000000`, `b = 3000000000`, `result = 8000000000`.
    
- **Expected function calls:** `main()` → `calculate()` → `printf()`.
    

## 4. Compilation

- **Compiler/version:** GCC 15.2.0 (MSYS2).
    
- **Architecture:** x86-64 Windows.
    
- **Build configuration:** `-O0 -g`.
    
- **Compilation command:**
    

```
gcc -O0 -g -o 06-fun_ret_long_long.exe 06-fun_ret_long_long.c
```

## 5. Assembly observations

|Instruction|Register or memory effect|Interpretation|
|---|---|---|
|`mov rax, 12A05F200h`|Loads 5000000000|Initializes first 64-bit value.|
|`mov [rbp-8], rax`|Stores RAX|Saves variable `a`.|
|`mov eax, 0B2D05E00h`|Loads 3000000000|Loads lower 32 bits of second value; upper bits are zeroed.|
|`mov [rbp-10h], rax`|Stores RAX|Saves variable `b` as 64-bit value.|
|`mov rdx, [rbp-8]`|Loads `a`|Retrieves first 64-bit value.|
|`mov rax, [rbp-10h]`|Loads `b`|Retrieves second 64-bit value.|
|`add rax, rdx`|Adds both values|Calculates 8000000000.|
|`ret`|Returns through RAX|Returns the 64-bit result.|
|`call calculate`|Calls function|Executes calculation.|
|`mov [rbp-8], rax`|Saves RAX|Stores returned result in `result`.|
|`mov rdx, rax`|Copies result|Prepares second argument for `printf()`.|

## 6. C reconstruction

```
#include <stdio.h>

long long calculate(void)
{
    long long a = 5000000000LL;
    long long b = 3000000000LL;

    return a + b;
}

int main(void)
{
    long long a = calculate();

    printf("result: %lld", a);

    return 0;
}
```

## 7. Verification

- **Does it compile?** Yes.
    
- **Does it behave like the original?** Yes.
    
- **Test case:** `calculate()` returns 8000000000.
    
- **Differences:** Only formatting differences in `printf()`.
    

## 8. Remember

- `RAX` is 64-bit, whereas `EAX` is its lower 32-bit portion.
    
- `mov eax, immediate` zeroes the upper 32 bits of RAX in x86-64.
    
- `long long` requires 64-bit storage, so the compiler uses 64-bit registers and memory operations.
    

## 9. Key RE lesson

In Windows x64, 64-bit integer return values are placed in RAX. Unlike 32-bit integer operations using EAX, 64-bit operations use RAX and other 64-bit registers. Recognizing register widths and memory operand sizes helps identify data types and reconstruct programs handling large integers.

## 10. Checkpoint

- I can identify 64-bit registers.
    
- I can explain the 64-bit addition.
    
- I can identify RAX as the return register.
    
- I can reconstruct the calculation.
    
- I can independently reconstruct a modified 64-bit function.