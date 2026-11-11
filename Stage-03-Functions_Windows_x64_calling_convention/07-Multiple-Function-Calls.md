# Program 07 — Multiple-Function-Calls

## 1. Objective

Understand how a program calls multiple functions, passes arguments, receives return values, and stores results between function calls.

## 2. Original C

```
int add(int a, int b) {
    return a + b;
}

int multiply(int a, int b) {
    return a * b;
}

int subtract(int a, int b) {
    return a - b;
}

int main(void) {
    int x = add(10, 20);
    int y = multiply(5, 6);
    int z = subtract(50, 15);

    printf("Add: %d\n", x);
    printf("Multiply: %d\n", y);
    printf("Subtract: %d\n", z);

    return 0;
}
```

## 3. Behavioral prediction

- **Expected output:**
    
    ```
    Add: 30
    Multiply: 30
    Subtract: 35
    ```
    
- **Expected return value:** `main` returns `0`.
    
- **Important variable values:** `x = 30`, `y = 30`, `z = 35`.
    
- **Expected function calls:** `add(10,20)`, `multiply(5,6)`, `subtract(50,15)`, followed by three `printf` calls.
    

## 4. Compilation

- **Compiler/version:** GCC 15.2.0 (MSYS2 UCRT64)
    
- **Architecture:** x86-64 Windows
    
- **Build configuration:** `-O0 -g`
    
- **Compilation command:**
    
    ```
    gcc -O0 -g -o 07-mul_fun_calls.exe 07-mul_fun_calls.c
    ```
    

## 5. Assembly observations

|Instruction|Register or memory effect|My interpretation|
|---|---|---|
|`mov edx, 14h`|EDX = 20|Second argument for `add`|
|`mov ecx, 0Ah`|ECX = 10|First argument for `add`|
|`call add`|Calls `add()`|Executes addition|
|`mov [rbp-4], eax`|Stores return value|Saves `x = 30`|
|`mov edx, 6` / `mov ecx, 5`|Sets arguments|Prepares `multiply(5,6)`|
|`call multiply`|Calls `multiply()`|Executes multiplication|
|`mov [rbp-8], eax`|Stores return value|Saves `y = 30`|
|`mov edx, 0Fh` / `mov ecx, 32h`|Sets arguments|Prepares `subtract(50,15)`|
|`call subtract`|Calls `subtract()`|Executes subtraction|
|`mov [rbp-0Ch], eax`|Stores return value|Saves `z = 35`|
|`mov eax, [rbp-4]`|Loads `x`|Prepares value for `printf`|
|`lea rcx, [address]`|Loads format-string address|First argument to `printf`|
|`mov edx, eax`|Moves result into EDX|Second argument to `printf`|
|`mov eax, 0`|Sets EAX to zero|Return value of `main`|

**Important observations:**

- Windows x64 passes the first two integer arguments through `ECX` and `EDX`.
    
- Each function returns its integer result in `EAX`.
    
- `main` stores each result in a separate stack location before calling the next function.
    
- `call` transfers execution to a function; `ret` returns to the instruction after the call.
    
- The three arithmetic functions have separate entry points and execute independently.
    

## 6. C reconstruction

```
#include <stdio.h>

int add(int a, int b)
{
    return a + b;
}

int multiply(int a, int b)
{
    return a * b;
}

int subtract(int a, int b)
{
    return a - b;
}

int main(void)
{
    int a = add(10, 20);
    int b = multiply(5, 6);
    int c = subtract(50, 15);

    printf("Add: %d", a);
    printf("Multiply: %d", b);
    printf("Subtract: %d", c);

    return 0;
}
```

## 7. Verification

- **Does it compile?** Yes.
    
- **Does it behave like the original?** Yes, it calculates and prints the same three results.
    
- **Test cases:** `add(10,20) = 30`, `multiply(5,6) = 30`, `subtract(50,15) = 35`.
    
- **Differences discovered:** My reconstruction omits newline characters (`\n`) in the `printf` statements, so the output formatting differs. The calculations and return values are equivalent.
    

## 8. Key RE lesson

A function call prepares arguments in the appropriate registers, transfers execution using `call`, and receives the return value in `EAX`. The caller can then store that result in memory and reuse it later. By tracking these operations, we can identify multiple function calls and reconstruct the order of execution.

## 9. Checkpoint

- I can explain every relevant instruction.
    
- I can reconstruct the program without the source.
    
- I can explain why my reconstruction is equivalent.
    
- I can solve a slightly modified version independently.