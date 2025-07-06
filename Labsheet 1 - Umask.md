# Labsheet 1 - Umasks

---

## 1. Lab Objectives

By the end of this lab, students should be able to:

- Explain the concept of default file permissions in Linux.
- View and interpret the current `umask` value.
- Calculate default permissions based on `umask`.
- Change `umask` values temporarily and persistently.
- Understand the impact of `umask` in multi-user environments.

---

## 2. Prerequisites

<span class ="text-apple-red">Students should already be familiar with:</span>

- Basic Linux shell commands.
- File permission symbols (r, w, x) and numeric values (e.g., 755, 644).
- The `touch`, `mkdir`, and `ls -l` commands.

---

## 3. Background Theory

In Linux, when a new file or directory is created, it does **not** get full (`777`) permissions by default. The **umask** (user file creation mask) subtracts permissions from the system's default to define the final permissions:

- **Default permission for files**: `666` (rw-rw-rw-)  
- **Default permission for directories**: `777` (rwxrwxrwx)

**Formula**:  

> Final Permission = Default - umask
>

**Example**:
If `umask` is `022`:

- File: `666 - 022 = 644` → `rw-r--r--`
- Directory: `777 - 022 = 755` → `rwxr-xr-x

---

## 4. Lab Activities

### Activity 1: Check Current `umask`

Run the following command:

```bash
umask
```

Note the value shown.

Convert it into permission format and **predict** what the default permission would be for a file and a directory.

Answer

```bash
rakeshsonea@ubuntu-min:~$ umask
0002
rakeshsonea@ubuntu-min:~$
```

The output `0002` from the `umask` command represents the **default permission mask** used when a user creates new files or directories.

#### Understanding `umask 0002`

- The **umask** value defines which permission bits **should be \*removed\*** from the default permissions.
- Linux uses the following default permissions:
  - **Files**: `666` (read & write for all — no execute by default)
  - **Directories**: `777` (read, write & execute for all)

#### Calculate Effective Permissions:

To get the actual permission:

```tiki wiki
Effective Permission = Default Permission - umask
```

So for `umask 0002`:

| Resource  | Default | Umask | Effective |
| --------- | ------- | ----- | --------- |
| File      | 666     | 002   | 664       |
| Directory | 777     | 002   | 775       |

#### This means:

- **Files** will have: `rw-rw-r--`
- **Directories** will have: `rwxrwxr-x`

#### Practical Implication:

- **User and group** can read/write.

- **Others** can only read (and execute in case of directories).

- It’s often used in environments where users share files within the same group (e.g., project teams), allowing group collaboration.

  ---

### Activity 2: Create Files and Directories

```bash
touch myfile.txt
mkdir mydir
ls -l
```

- Observe and **record** the actual permissions.
- Compare with your prediction from Activity 1.

**Observation:**

```bash
drwxrwxr-x 2 rakeshsonea rakeshsonea 4096 Jul  6 12:45 mydir
-rw-rw-r-- 1 rakeshsonea rakeshsonea    0 Jul  6 12:44 myfile.txt
```

1. #### **Directory: `mydir`**

```bash
drwxrwxr-x  rakeshsonea rakeshsonea ...
```

#### Interpretation:

- `d` → it's a directory
- `rwxrwxr-x`:
  - **Owner**: `rwx` → read, write, execute
  - **Group**: `rwx` → read, write, execute
  - **Others**: `r-x` → read, execute

#### Explanation:

- Default permission for directories: `777` (full access for all)
- umask `0002` subtracts `--- --- -w-` (i.e. `002`)
- Resulting permission: `775` → `rwxrwxr-x`

2. #### **File: `myfile.txt`**

```bash
-rw-rw-r--  rakeshsonea rakeshsonea ...
```

#### Interpretation:

- `-` → it's a regular file
- `rw-rw-r--`:
  - **Owner**: `rw-` → read, write
  - **Group**: `rw-` → read, write
  - **Others**: `r--` → read-only

#### Explanation:

- Default permission for new files: `666` (no execute by default)
- umask `0002` subtracts `--- --- -w-`
- Resulting permission: `664` → `rw-rw-r--`

**Why no execute (`x`) on file?**
 By default, regular files (like text files) do not get execute permission for safety

### Summary

| Type      | Default Perm | umask 0002 Subtracted | Final Perm | Result      |
| --------- | ------------ | --------------------- | ---------- | ----------- |
| Directory | `777`        | `002`                 | `775`      | `rwxrwxr-x` |
| File      | `666`        | `002`                 | `664`      | `rw-rw-r--` |

---

### Activity 3: Temporarily Change `umask`

```bash
umask 077
touch secret.txt
mkdir secret_folder
ls -l
```

What permissions were applied?

Why would this `umask` be useful in a **secure environment**?

Answer:
```bash
rakeshsonea@ubuntu-min:~$ umask 077
rakeshsonea@ubuntu-min:~$ umask
0077
```

```bash
rakeshsonea@ubuntu-min:~$ touch secret.txt
rakeshsonea@ubuntu-min:~$ mkdir secret_folder
rakeshsonea@ubuntu-min:~$ ls -l
drwxrwxr-x 2 rakeshsonea rakeshsonea 4096 Jul  6 12:45 mydir
-rw-rw-r-- 1 rakeshsonea rakeshsonea    0 Jul  6 12:44 myfile.txt
-rw------- 1 rakeshsonea rakeshsonea    0 Jul  6 12:52 secret.txt
drwx------ 2 rakeshsonea rakeshsonea 4096 Jul  6 12:53 secret_folder
```

Explanation

1. ##### Umask Set to 0077

```bash
umask 077
```

This sets the **umask** to:

```bash
0077 → --- --- r-- r-- 
           (u)  (g) (o)
```

- This mask **removes all permissions** from **group** and **others**.
- Only the **owner** retains the default permissions (as much as possible).

2. ##### Create a File

```bash
touch secret.txt
```

#### File Permission Analysis:

- **Default file mode**: `666` (read & write for all)
- **Apply umask 0077**: subtract `077` → `666 - 077 = 600`

#### Final File Permission:

```bash
-rw-------  rakeshsonea rakeshsonea  secret.txt
```

- **Owner**: read, write
- **Group**: no permission
- **Others**: no permission

→ This ensures **complete privacy**; only the file owner can access it.

3. ##### Create a Directory

```bash
mkdir secret_folder
```

### Directory Permission Analysis:

- **Default dir mode**: `777` (full permissions)
- **Apply umask 0077**: subtract `077` → `777 - 077 = 700`

#### Final Directory Permission:

```bash
drwx------  rakeshsonea rakeshsonea  secret_folder
```

- **Owner**: read, write, execute
- **Group/Others**: no access at all

→ Only the owner can list or enter this directory.

#### Compare with Previously Created Files and Directories

| Item            | Permission   | Reason                                                       |
| --------------- | ------------ | ------------------------------------------------------------ |
| `mydir`         | `drwxrwxr-x` | Created under `umask 0002` → gives full group access         |
| `myfile.txt`    | `-rw-rw-r--` | Created under `umask 0002` → readable by others              |
| `secret.txt`    | `-rw-------` | Created under `umask 0077` → private to owner                |
| `secret_folder` | `drwx------` | Created under `umask 0077` → fully restricted to the owner only |

### **Use Case of umask 0077**

This mask is **ideal for secure environments**, such as:

- Storing confidential data
- Writing scripts that deal with passwords or tokens
- Limiting visibility of personal logs and notes

### Activity 4: Persistent `umask` Setting

Edit the shell configuration file:

```bash
nano ~/.bashrc
# Add the following line at the end:
umask 027
```

Then run:

```bash
source ~/.bashrc
umask
```

Explain the difference this setting makes.

Test the new behavior by creating files and directories.

## 5. Reflection Questions

1. Why should system administrators care about `umask` settings?
2. What are the risks of having a very permissive `umask` like `000`?
3. Why might a user set `umask` to `077` in a shared environment?
4. How can different users have different `umask` settings on the same system?