### 1. Ltrace:

**Description:** Ltrace is a tool for tracing system calls and library calls of an application. It is primarily used to observe dynamic function calls during the execution of a program.

**Usage:** To use Ltrace, you can execute the following command in the terminal:

bash

`ltrace [command]`

This will display the function calls made by the specified program.

### 2. Strings:

**Description:** The Strings tool extracts printable character strings from a binary file. This can be useful for identifying sensitive information or references to functions.

**Usage:** The basic command is as follows:

bash

`strings [binary_file]`

### 3. File:

**Description:** The File tool determines the file type based on its content. This can help identify the type of file you are analyzing.

**Usage:** Simply run:

bash

`file [file_name]`

### 4. Objdump:

**Description:** Objdump displays information about object files, including assembly code.

**Usage:** For example, to disassemble an executable file, you can use the command:

bash

`objdump -d [file_name]`

### 5. Ghidra:

**Description:** Ghidra is an open-source reverse engineering tool that can be used to decompile binary files.

**Usage:** Ghidra has a user-friendly graphical interface. You can use it to analyze code, rename variables, add comments, etc.

### 6. Rename Variables and Add Comments:

When using tools like Ghidra, you can often rename variables and add comments to make the code more readable.

### 7. Download Decompilers:

Decompilers such as IDA Pro, Radare2, or Ghidra can be used to convert machine code into readable source code.

### 8. Hexdump:

**Description:** Hexdump displays the contents of a file in hexadecimal format.

**Usage:** To display the contents of a file in hexadecimal format, use the command:

bash

`hexdump -C [file_name]`

### 9. Python Tools:

Python scripts are often used in reverse engineering to automate specific tasks, analyze data, etc.

### 10. Frida:

**Description:** Frida is a dynamic instrumentation tool that allows injecting code into running applications.

**Usage:** Frida can be used to modify the behavior of an application by injecting JavaScript code.

### 11. Library Hijacking:

This involves replacing a library used by a program with a modified version to change its behavior.
Only possible when it's dynamic !

**Usage:** This can be done by replacing the path to the library in the `LD_LIBRARY_PATH` environment variable or using other library loading techniques.

These tools and concepts form a solid foundation for reverse engineering. Remember to comply with laws and regulations when using these tools, as reverse engineering may be subject to legal restrictions.