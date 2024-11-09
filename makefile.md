
## GCC (GNU Compiler Collection)

GCC is indeed a compiler. It's one of the most widely used compilers for the C, C++, and other programming languages. \
GCC supports various architectures and platforms and provides a suite of tools for compiling, assembling, linking, and debugging code.

## How to Compile

Compiling a C file with GCC involves several steps.
\
Each of these steps can be performed separately by explicitly specifying the intermediate files, \
or GCC can handle them automatically if you provide only the source file.
\
Take the follwoing structure as example:
```console
project/
├── sources/
│   ├── source1.c
│   ├── source2.c
│   └── main.c
└── include/
    └── header.h
```

### Preprocessing
GCC first runs the preprocessor, which handles directives like #include, #define, etc. \
The preprocessor replaces these directives with actual code from header files or macro definitions, producing an expanded source code file.
```console
gcc -E source.c -o source.i
```

### Compilation
The preprocessed file is then compiled into assembly code specific to the target architecture. \
This step involves lexical analysis, parsing, optimization, and code generation.
```consle
gcc -S source.i -o source.s
```

### Assembly
The assembly code generated in the previous step is then assembled into machine code (object files).
```console
gcc -c source.s -o source.o
```

### Linking
If your program consists of multiple source files or external libraries, the object files are linked together to form an executable. \
This step resolves references to external symbols and creates the final executable.
```console
gcc source1.o source2.o -o program
```

## MakeFile

Here is a complete sample makefile:

```console
# Define variables
CC = gcc
CFLAGS = -Iinclude
SRCDIR = sources
OBJDIR = obj
BINDIR = bin


# List of source files
SRCS := $(wildcard $(SRCDIR)/*.c)

# Corresponding object files
OBJS := $(SRCS:$(SRCDIR)/%.c=$(OBJDIR)/%.o)

# Executable
TARGET = $(BINDIR)/myprogram


# Create

all: $(TARGET)

$(TARGET): $(OBJS)
	$(CC) $(CFLAGS) $^ -o $@

$(OBJS): $(OBJDIR)/%.o : $(SRCDIR)/%.c | $(OBJDIR)
	$(CC) $(CFLAGS) -c $< -o $@


# Create folder

$(OBJDIR):
	mkdir -p $(OBJDIR)

$(BINDIR):
	mkdir -p $(BINDIR)


# Clear

.PHONY: clean
clean:
	rm -rf $(OBJDIR) $(BINDIR)
```

## Online MakeFile

https://onecompiler.com/c/3zzbfuqs6

