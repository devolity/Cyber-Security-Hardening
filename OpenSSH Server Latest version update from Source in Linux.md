# OpenSSH Server Latest version update from Source in Linux

#### OpenSSH server version upgrade

##### Package Dependency Install

-------------- RHEL 8 and Fedora 22+ --------------
dnf group install 'Development Tools'
dnf install zlib-devel openssl-devel
dnf install pam-devel libselinux-devel     ## PAM and SELinux Headers

-------------- Debian/Ubuntu --------------  
apt update  
apt install build-essential zlib1g-dev libssl-dev  
apt install libpam0g-dev libselinux1-dev   #**PAM and SELinux Headers**

cd /tmp

wget -c https://cdn.openbsd.org/pub/OpenBSD/OpenSSH/portable/openssh-9.5p1.tar.gz  
tar -xvzf openssh-*p1.tar.gz  
cd openssh-8.0p1/

#### For Ubuntu 23.X

```bash
install -v -g sys -m700 -d /var/lib/sshd &&

groupadd -g 50 sshd        &&
useradd  -c 'sshd PrivSep' \
         -d /var/lib/sshd  \
         -g sshd           \
         -s /bin/false     \
         -u 50 sshd

./configure --prefix=/usr                            \
            --sysconfdir=/etc/ssh                    \
            --with-privsep-path=/var/lib/sshd        \
            --with-default-path=/usr/bin             \
			--with-pam								\
            --with-superuser-path=/usr/sbin:/usr/bin \
            --with-pid-dir=/run                      &&
make

### Now as the root User:-

make install &&
install -v -m755    contrib/ssh-copy-id /usr/bin     &&

install -v -m644    contrib/ssh-copy-id.1 \
                    /usr/share/man/man1              &&
install -v -m755 -d /usr/share/doc/openssh-9.5p1     &&
install -v -m644    INSTALL LICENCE OVERVIEW README* \
                    /usr/share/doc/openssh-9.5p1
```

```bash
Command Explanations
--sysconfdir=/etc/ssh: This prevents the configuration files from being installed in /usr/etc.

--with-default-path=/usr/bin and --with-superuser-path=/usr/sbin:/usr/bin: These set PATH consistent with LFS and BLFS Shadow package.

--with-pid-dir=/run: This prevents OpenSSH from referring to deprecated /var/run.

--without-zlib-version-check: This prevents OpenSSH from checking the version of the system Zlib. We need to use this switch or the version check would mistakenly report the latest Zlib 1.13 “too old” and reject it.

--with-pam: This parameter enables Linux-PAM support in the build.

--with-xauth=$XORG_PREFIX/bin/xauth: Set the default location for the xauth binary for X authentication. The environment variable XORG_PREFIX should be set following Xorg build environment. This can also be controlled from sshd_config with the XAuthLocation keyword. You can omit this switch if Xorg is already installed.

--with-kerberos5=/usr: This option is used to include Kerberos 5 support in the build.

--with-libedit: This option enables line editing and history features for sftp.
```

```bash
echo "PermitRootLogin no" >> /etc/ssh/sshd_config
echo "PasswordAuthentication no" >> /etc/ssh/sshd_config &&
echo "KbdInteractiveAuthentication no" >> /etc/ssh/sshd_config

sed 's@d/login@d/sshd@g' /etc/pam.d/login > /etc/pam.d/sshd &&
chmod 644 /etc/pam.d/sshd &&
echo "UsePAM yes" >> /etc/ssh/sshd_config
```

To test the results, issue: make -j1 tests.
## For RedHat

###### make install &&install -v -m755    contrib/ssh-copy-id /usr/bin     &&

install -v -m644    contrib/ssh-copy-id.1
/usr/share/man/man1              &&
install -v -m755 -d /usr/share/doc/openssh-9.4p1     &&
install -v -m644    INSTALL LICENCE OVERVIEW README*
/usr/share/doc/openssh-9.4p1

**** For RedHat  OS

mkdir /var/lib/sshd
chmod -R 700 /var/lib/sshd/
chown -R root:sys /var/lib/sshd/
useradd -r -U -d /var/lib/sshd/ -c "sshd privsep" -s /bin/false sshd
### To check all custom configurable options use:

./configure -h
### Compile and Install SSH from Sources

./configure --with-md5-passwords --with-pam --with-selinux --with-privsep-path=/var/lib/sshd/ --sysconfdir=/etc/ssh
make
sudo make install ## sudo is required
### Verify ssh version

ssh -V
OpenSSH_8.0p1, OpenSSL 1.1.0g  2 Nov 2017
#### The various OpenSSH configuration files located at:

~/.ssh/* – this directory stores user specific ssh client configurations (ssh aliases) and keys.
/etc/ssh/ssh_config – this file contains system wide ssh client configurations.
/etc/ssh/sshd_config – contains sshd service configurations.

### Fix Weak Algo

macs umac-64-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,umac-64@openssh.com,umac-128@openssh.com,hmac-sha2-256,hmac-sha2-512
