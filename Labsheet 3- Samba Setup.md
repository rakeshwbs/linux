**Lab Sheet: Install and Configure Samba Server on Linux**

**Module**: Linux Administration
 **Lab Title**: Install and Configure Samba Server with Anonymous and Authenticated Access
 **Duration**: 1.5 Hours
 **Prerequisites**:

- Understanding of Linux filesystem and permissions
- Basic knowledge of networking and IP addressing
- Root or sudo access to a Linux system

------

### 1. Objective

In this lab, students will learn to install and configure the Samba service on a Linux machine to share directories with Windows clients. Both anonymous (guest) and authenticated access will be configured. Students will also test connectivity using both Linux and Windows tools.

------

### 2. Install Samba

```bash
sudo apt update
sudo apt install samba
```

**Explanation**: The `apt update` command updates the local package list, while `apt install samba` installs the Samba software. Samba is an open-source implementation of the SMB/CIFS protocol used by Windows for file and printer sharing.

------

### 3. Configure a Directory for Anonymous Access

#### Step 1: Create a Shared Directory

```bash
sudo mkdir -p /srv/samba/anonymous
```

**Explanation**: This creates a directory that will be shared anonymously. The `-p` flag ensures that the full path is created if it doesn't exist.

#### Step 2: Set Permissions

```bash
sudo chown nobody:nogroup /srv/samba/anonymous
sudo chmod 0777 /srv/samba/anonymous
```

**Explanation**: Ownership is assigned to the `nobody` user and `nogroup` to allow guest access. The permission `0777` gives read, write, and execute permissions to everyone.

#### Step 3: Configure Samba

Edit the configuration file:

```bash
sudo nano /etc/samba/smb.conf
```

Add the following section at the bottom:

```ini
[Anonymous]
    path = /srv/samba/anonymous
    browsable = yes
    writable = yes
    guest ok = yes
    read only = no
```

**Explanation**:

- `path`: specifies the directory to be shared.
- `browsable`: allows it to be visible when browsing.
- `writable`: enables write access.
- `guest ok`: permits guest access without a password.
- `read only`: must be set to `no` if write access is required.

#### Step 4: Restart Samba Service

```bash
sudo systemctl restart smbd
```

**Explanation**: This command restarts the Samba daemon to apply the new configuration.

------

### 4. Configure a Directory for Authenticated Access

#### Step 1: Create a New Shared Directory

```bash
sudo mkdir -p /srv/samba/secure
sudo chown root:root /srv/samba/secure
sudo chmod 0770 /srv/samba/secure
```

**Explanation**: A directory is created for secure access. Only authenticated users will be allowed. Permissions `0770` ensure only the owner and group can access it.

#### Step 2: Create a Samba Group and User

```bash
sudo groupadd sambashare
sudo useradd -M -d /srv/samba/secure -s /usr/sbin/nologin user1
sudo smbpasswd -a user1
sudo usermod -aG sambashare user1
```

**Explanation**:

- `groupadd`: creates a group for Samba users.
- `useradd`: adds a user without a home directory.
- `smbpasswd`: sets the Samba password.
- `usermod`: adds the user to the `sambashare` group.

#### Step 3: Configure Samba

Edit the config file again:

```bash
sudo nano /etc/samba/smb.conf
```

Add this section:

```ini
[Secure]
    path = /srv/samba/secure
    valid users = user1
    guest ok = no
    writable = yes
    browsable = yes
```

**Explanation**:

- `valid users`: specifies allowed Samba users.
- `guest ok = no`: enforces authentication.

#### Step 4: Restart Samba

```bash
sudo systemctl restart smbd
```

**Explanation**: The service must be restarted for the new secure share to become active.

------

### 5. Test the Samba Shares

#### From Linux using `smbclient`

```bash
smbclient \\localhost\Anonymous -N
smbclient \\localhost\Secure -U user1
```

**Explanation**:

- `-N`: connects without a password (used for guest access).
- `-U`: specifies the username for authenticated access.

#### From Windows Explorer

1. Press `Win + R`, type `\\<Linux_IP>\Anonymous`, press Enter.
2. Repeat with `\\<Linux_IP>\Secure` and provide credentials when prompted.

**Explanation**: This demonstrates real-world interoperability between Linux and Windows systems using Samba.

------

### 6. Firewall Configuration (Optional)

```bash
sudo ufw allow samba
```

**Explanation**: This opens required ports (typically 137–139, 445) in the firewall to allow SMB traffic.

------

### 7. Lab Questions

1. What is the difference between anonymous and authenticated access in Samba?
2. Why is it important to restrict permissions on secure directories?
3. What ports need to be open for Samba to function properly on a firewall?
4. How does Samba maintain user authentication?

------

**End of Lab**