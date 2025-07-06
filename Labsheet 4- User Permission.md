# **Lab Sheet: User Account and Permission Management on Linux**

**Module**: Linux Administration
 **Lab Title**: User Account and Permission Management
 **Duration**: 1.5 Hours
 **Prerequisites**:

- Familiarity with Linux CLI
- Basic understanding of file ownership and user roles
- Root or sudo access

------

### 1. Objective

This lab introduces students to Linux user and group management, as well as file and directory permission settings. Students will gain hands-on experience creating users, assigning groups, changing ownerships, and setting various levels of access control.

------

### 2. Create a New User

```bash
sudo adduser alice
```

**Explanation**: The `adduser` command creates a new user named `alice` and prompts for password and other details. The user is also assigned a home directory by default (`/home/alice`).

------

### 3. Add a User to a Group

```bash
sudo groupadd developers
sudo usermod -aG developers alice
```

**Explanation**:

- `groupadd`: creates a new group named `developers`.
- `usermod -aG`: appends the user `alice` to the `developers` group without removing existing groups.

------

### 4. Switch Between Users

```bash
su - alice
```

**Explanation**: Switches to the user `alice`'s shell with her environment variables. The `-` ensures a full login shell.

To return to root:

```bash
exit
```

------

### 5. Set File Ownership

Create a file as root:

```bash
sudo touch /tmp/devfile
sudo chown alice:developers /tmp/devfile
```

**Explanation**:

- `chown alice:developers`: changes ownership of the file to user `alice` and group `developers`.

------

### 6. Modify File Permissions

```bash
sudo chmod 640 /tmp/devfile
```

**Explanation**: Sets permissions to:

- **6** (rw-) for owner (alice)
- **4** (r--) for group (developers)
- **0** (---) for others

Use `ls -l /tmp/devfile` to verify.

------

### 7. Create a Shared Group Directory

```bash
sudo mkdir /srv/project
sudo chown root:developers /srv/project
sudo chmod 2775 /srv/project
```

**Explanation**:

- `2775` enables the **setgid** bit (`2`) which ensures all new files created inside inherit the group ownership.
- `rwxrwsr-x` will be the permission set for the directory.

------

### 8. Create a Test File as Alice

```bash
sudo su - alice
cd /srv/project
nano test.txt
```

Save the file and verify:

```bash
ls -l
```

**Explanation**: The file should belong to user `alice` and group `developers` due to the setgid bit.

------

### 9. Change User Password

```bash
sudo passwd alice
```

**Explanation**: Allows the administrator to reset or change a user password manually.

------

### 10. Lock and Unlock a User Account

```bash
sudo passwd -l alice   # lock
sudo passwd -u alice   # unlock
```

**Explanation**:

- Locking disables user login without deleting the account.
- Useful for temporary suspensions.

------

### 11. Delete a User

```bash
sudo deluser --remove-home alice
```

**Explanation**:

- Removes the user and deletes their home directory.
- Use with care—ensure data backup if needed.

------

### 12. Lab Questions

1. What is the difference between `adduser` and `useradd`?
2. What is the purpose of the setgid bit on a directory?
3. What does the permission `640` mean?
4. How can you check which groups a user belongs to?

------

**End of Lab**