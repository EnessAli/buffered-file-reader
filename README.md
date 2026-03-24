# Buffered File Reader

A single-function implementation of `get_next_line()` — reads one line at a time from a file descriptor using a static buffer, handling multiple file descriptors simultaneously without memory leaks.

## How It Works

```
BUFFER_SIZE bytes read into static buffer
        │
   ┌────▼────────────────────────────────┐
   │  Search for n in buffer          │
   │  If found → extract line, save rest │
   │  If not   → read more bytes         │
   └─────────────────────────────────────┘
        │
   Return line (heap-allocated, caller frees)
```

The static buffer persists between s, accumulating bytes until a newline or EOF is found.

## API

```c
// Returns next line from fd (without trailing newline)
// Returns NULL on EOF or error
char *get_next_line(int fd);
```

## Multi-FD Support (Bonus)

Uses a static array `char *stash[OPEN_MAX]` — each file descriptor maintains its own independent buffer, enabling interleaved reads from multiple files:

```c
char *line;
int fd1 = open("file1.txt", O_RDONLY);
int fd2 = open("file2.txt", O_RDONLY);

while ((line = get_next_line(fd1)) != NULL) { free(line); }
while ((line = get_next_line(fd2)) != NULL) { free(line); }
```

## Configurable Buffer Size

```bash
gcc -D BUFFER_SIZE=64 main.c get_next_line.c
```

## Tech Stack

`C` `File I/O` `Static Variables` `Dynamic Memory` `Buffer Management`

