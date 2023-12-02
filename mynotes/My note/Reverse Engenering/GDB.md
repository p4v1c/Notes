### GDB (GNU Debugger) Commands

#### Basic Commands

- **`gdb [program]`**: Start GDB and load the specified program.
    
- **`run [args]`**: Start the program with optional arguments.
    
- **`break [location]`**: Set a breakpoint at a specified function or address.
    
    Example: `break main` - Set a breakpoint at the `main` function.
    
- **`continue`**: Continue program execution after hitting a breakpoint.
    
- **`next`**: Execute the current line of code and stop at the next line.
    
- **`step`**: Step into the current function.
    
- **`list [function]`**: List the source code for a function.
    
    Example: `list my_function` - Display the source code for the `my_function` function.
    

#### Information Commands

- **`info registers`**: Display the values of all registers.
    
- **`info functions`**: List all functions in the program.
    
- **`info variables`**: List all global and static variables.
    
- **`info breakpoints`**: List all set breakpoints.
    

#### Examining Memory

- **`x/[format] [address]`**: Examine memory at a specified address.
    
    - `x` stands for examine.
    - `/` separates the format from the address.
    - `[format]` defines the display format (e.g., `s` for string, `x` for hexadecimal).
    - `[address]` specifies the memory address to examine.
    
    Examples:
    
    - `x/32x $esp`: Display 32 words at the stack pointer in hexadecimal format.
    - `x/s 0xfff848`: Display a null-terminated string starting at memory address 0xfff848.
    - `x/lxw $ebx`: Interpret the value at the `ebx` register as a signed word in hexadecimal.
- **`x/l[silw] [address]`**: You can use different format specifiers to interpret memory as different data types. For example:
    
    - `x/lx` displays a 4-byte value as a hexadecimal number.
    - `x/ix` displays a 4-byte value as a signed integer.
    - `x/cx` displays a single character.
    - `x/sx` displays a string.
    

#### Stack Analysis

- **`bt`**: Display a back trace of the call stack.
    
- **`info frame`**: Display information about the current stack frame.
    
- **`frame [n]`**: Select a specific stack frame by number.
    

#### Advanced Debugging

- **`disassemble [function]`**: Disassemble a specified function.
    
    Example: `disassemble main` - Disassemble the `main` function.
    
- **`set disassembly-flavor [intel|att]`**: Set the assembly language flavor.
    
- **`display [expression]`: Evaluate and display an expression at every stop.
    
    Example: `display variable_name` - Display the value of a specific variable during debugging.
    
- **`record` and `reverse-record`**: Record program execution for debugging.
    

#### Manipulating Execution

- **`set $register=value`**: Set the value of a register.
    
    Example: `set $eax=10` - Set the value of the `eax` register to 10.
    
- **`call [function(args)]`**: Call a function during debugging.
    
    Example: `call my_function(42)` - Call the `my_function` with an argument of 42.
    
- **`finish`**: Continue execution until the current function returns.
    

#### Breakpoint Management

- **`delete [breakpoint]`**: Delete a specified breakpoint.
    
- **`enable [breakpoint]`**: Enable a disabled breakpoint.
    
- **`disable [breakpoint]`**: Disable an enabled breakpoint.


### Watchpoint Commands

- **`watch [expression]`**: Set a watchpoint to break when the value of an expression changes.
    
    Example: `watch *(int *)0x12345678` - This sets a watchpoint on the value stored at memory address `0x12345678`, and the program will break when the value changes.
    
- **`watch -l [expression]`**: Set a watchpoint on a specific location (e.g., memory address) rather than an expression.
    
    Example: `watch -l *(int *)0x12345678` - This watches the memory address `0x12345678` for changes.
    
- **`info watchpoints`**: Display information about currently set watchpoints.
    
- **`delete watch [watchpoint_number]`**: Delete a specific watchpoint by its number.
    
- **`delete watch`**: Delete all watchpoints.
