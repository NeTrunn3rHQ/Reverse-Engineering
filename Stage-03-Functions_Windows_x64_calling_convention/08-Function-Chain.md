# Program 08 — Function-Chain

## 1. Objective

Understand nested function calls and trace how arguments and return values flow through multiple functions.

## 2. Original C

```
int first(int x)
{
    return x + 10;
}

int second(int x)
{
    return first(x) * 2;
}

int third(int x)
{
    return second(x) + 5;
}

int main(void)
{
    int result = third(10);
    printf("Result: %d\n", result);
    return 0;
}
```

## 3. Behavioral prediction

- **Expected output:** `Result: 45`
    
- **Expected return value:** `main` returns `0`.
    
- **Important values:**
    
    - `third(10)` calls `second(10)`.
        
    - `second(10)` calls `first(10)`.
        
    - `first(10)` returns `20`.
        
    - `second(10)` returns `40`.
        
    - `third(10)` returns `45`.
        
- **Expected function-call order:** `main → third → second → first`, then returns in reverse order.
    

## 4. Compilation

- **Compiler/version:** GCC 15.2.0 (MSYS2 UCRT64)
    
- **Architecture:** x86-64 Windows
    
- **Build configuration:** `-O0 -g`
    
- **Compilation command:**
    
    ```
    gcc -O0 -g -o 08-fun_chain.exe 08-fun_chain.c
    ```
    

## 5. Assembly observations

|Instruction|Register or memory effect|My interpretation|
|---|---|---|
|`mov ecx, 0Ah`|ECX = 10|Argument passed to `third`|
|`call third`|Calls `third()`|Starts the function chain|
|`mov ecx, eax`|Copies argument into ECX|Passes the value to the next function|
|`call second`|Calls `second()`|Continues the chain|
|`call first`|Calls `first()`|Executes the first calculation|
|`add eax, 0Ah`|EAX = EAX + 10|`first()` adds 10|
|`add eax, eax`|EAX = EAX * 2|`second()` doubles the result|
|`add eax, 5`|EAX = EAX + 5|`third()` adds 5|
|`mov [rbp-4], eax`|Stores return value|Saves final result in `main`|
|`mov eax, 0`|EAX = 0|Return value of `main`|

**Important observations:**

- Each function passes its argument to the next function through `ECX`.
    
- Each function receives the previous function's return value through `EAX`.
    
- `call` transfers execution into another function.
    
- `ret` returns execution to the calling function.
    
- The function chain unwinds in reverse order: `first → second → third → main`.
    

## 6. C reconstruction

```
#include <stdio.h>

int first(int val2)
{
    int val3 = val2;

    return val3 + 10;
}

int second(int val1)
{
    int val2 = val1;

    return first(val2) * 2;
}

int third(int a)
{
    int val1 = a;

    return second(val1) + 5;
}

int main(void)
{
    int a = third(10);

    printf("Result: %d", a);

    return 0;
}
```

## 7. Verification

- **Does it compile?** Yes.
    
- **Does it behave like the original?** Yes.
    
- **Test cases:** `first(10) = 20`, `second(10) = 40`, `third(10) = 45`.
    
- **Differences discovered:** My reconstruction introduces temporary variables (`val1`, `val2`, `val3`), which are unnecessary but do not change the behavior. The output newline is also omitted.
    

## 8. Remember

- Temporary variables are valid in the reconstruction, but the compiler does not necessarily need separate variables for every intermediate value.
    

## 9. Key RE lesson

A function chain involves one function calling another and using its return value in further calculations. Track the argument in `ECX` and the return value in `EAX` to follow the data flow. Remember that execution returns in reverse order of the calls.

## 10. Checkpoint

- I can explain every relevant instruction.
    
- I can reconstruct the program without the source.
    
- I can explain why my reconstruction is equivalent.
    
- I can solve a slightly modified version independently.