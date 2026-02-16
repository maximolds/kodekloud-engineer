### Solution

```shell

sshpass -p Ir0nM@n ssh -o StrictHostKeyChecking=no tony@172.16.238.10


# 1. Create user 'ammar' in 'ftp' group
sudo useradd -m -g ftp -s /bin/bash ammar

# 2. Set password to 'LQfKeWWxWD'
echo "ammar:LQfKeWWxWD" | sudo chpasswd

# 3. Verify creation
id ammar


# 4. Create user with nologin shell (blocks SSH)
sudo useradd -m -g ftp -s /sbin/nologin ammar

# 5. Setup chroot directory structure
sudo chown root:root /home/ammar
sudo chmod 755 /home/ammar
sudo mkdir -p /home/ammar/upload
sudo chown ammar:ftp /home/ammar/upload
sudo chmod 755 /home/ammar/upload

# 6. Add to /etc/ssh/sshd_config (at the end)
sudo tee -a /etc/ssh/sshd_config > /dev/null << 'EOF'

Match Group ftp
    ChrootDirectory /home/%u
    ForceCommand internal-sftp
    AllowTcpForwarding no
    X11Forwarding no
EOF

# 7. Test and restart SSH
sudo sshd -t
sudo systemctl restart sshd

# ✅ SFTP should work
sftp ammar@localhost

# ❌ SSH should fail with "sftp connections only"
ssh ammar@localhost

```