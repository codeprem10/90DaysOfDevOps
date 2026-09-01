# Day 06 – Linux File I/O Practice

## Objective

Practice creating, writing, appending, and reading text files using basic Linux commands.

## Commands Practiced

### 1. Create an empty file

```bash
touch notes.txt
```

Creates an empty file named `notes.txt`.

### 2. Write to a file

```bash
echo "Line 1" > notes.txt
```

`>` writes output to the file and overwrites existing content.

### 3. Append to a file

```bash
echo "Line 2" >> notes.txt
```

`>>` adds content to the end of the file without overwriting existing content.

### 4. Write and display using `tee`

```bash
echo "Line 3" | tee -a notes.txt
```

`|` sends the output of `echo` to `tee`.

`tee -a` displays the text on the terminal and appends it to the file.

### 5. Read the complete file

```bash
cat notes.txt
```

Displays the entire contents of the file.

### 6. Read the first 2 lines

```bash
head -n 2 notes.txt
```

Displays the first two lines of the file.

### 7. Read the last 2 lines

```bash
tail -n 2 notes.txt
```

Displays the last two lines of the file.

## Final File Content

```text
Line 1
Line 2
Line 3
```

## Key Takeaways

* `touch` → create an empty file
* `>` → write/overwrite
* `>>` → append
* `tee` → display and write
* `cat` → read the entire file
* `head` → read the beginning
* `tail` → read the end

## Why This Matters in DevOps

Linux configuration files, logs, scripts, and application data are commonly stored as text files. Knowing how to quickly create, modify, and inspect them is essential for troubleshooting and automation.
