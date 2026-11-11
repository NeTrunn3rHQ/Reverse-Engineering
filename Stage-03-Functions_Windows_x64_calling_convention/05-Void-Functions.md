# Program 05 — Void Functions

## 1. Objective

Understand how void functions work, how arguments are passed, and how to identify functions that perform operations without returning a meaningful value.

## 2. Original C

```
#include <stdio.h>

void display(int value)
{
    printf("Value: %d\n", value);
}

int main(void)
{
    display(100);
    return 0;
}
```

## 3. Behavioral prediction

- **Expected output:** `Value: 100`
    
- **Expected return value:** `display()` returns no value; `main()` returns 0.
    
- **Important values:** `value = 100`.
    
- **Expected function calls:** `main()` → `display(100)` → `printf()`.
    

## 4. Compilation

- **Compiler/version:** GCC 15.2.0 (MSYS2).
    
- **Architecture:** x86-64 Windows.
    
- **Build configuration:** `-O0 -g`.
    
- **Compilation command:**
    

```
gcc -O0 -g -o 05-void-fun.exe 05-void-function.c
```

## 5. Assembly observations

|Instruction|Register or memory effect|Interpretation|
|---|---|---|
|`mov [rbp+10h], ecx`|Saves ECX|Stores argument `value`.|
|`mov eax, [rbp+10h]`|Loads argument|Retrieves value (100).|
|`lea rcx, [address]`|Loads format string address|Prepares first argument for `printf()`.|
|`mov edx, eax`|EDX = 100|Prepares second argument for `printf()`.|
|`call printf`|Calls printf|Prints the value.|
|`nop`|No operation|Does not change program state.|
|`mov ecx, 64h`|ECX = 100|Prepares argument for `display()`.|
|`call display`|Calls display|Executes the void function.|
|`mov eax, 0`|EAX = 0|Sets `main()` return value.|
|`ret`|Returns to caller|Ends the function.|

## 6. C reconstruction

```
#include <stdio.h>

void display(int a)
{
    printf("Value: %d", a);
}

int main(void)
{
    display(100);

    return 0;
}
```

## 7. Verification

- **Does it compile?** Yes.
    
- **Does it behave like the original?** Yes.
    
- **Test case:** `display(100)` prints `Value: 100`.
    
- **Differences:** The reconstructed `printf()` omits the trailing newline. No functional difference in the displayed value.
    

## 8. Remember

- A void function does not return a meaningful value.
    
- The absence of a return value does not mean the function does nothing.
    
- `EAX` being used inside a void function does not mean that function returns an integer.
    

## 9. Key RE lesson

A void function performs an operation without returning a value to its caller. Here, `display()` receives its argument through ECX and passes the format string and value to `printf()` through RCX and EDX. Unlike an integer-returning function, it does not need to produce a meaningful result in EAX.

## 10. Checkpoint

- I can identify the argument-passing instruction.
    
- I can identify the printf() call.
    
- I can distinguish a void function from an integer-returning function.
    
- I can independently reconstruct a modified void function.