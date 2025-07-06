# <span class ="text-crimson-red ">Labsheet 1 - Umasks</span>

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