# 传输协议搭建

## FTP Linux

FTP 的搭建过程是三种文件传输协议中最繁琐的，因此我直接写了一个入门一件 shell 脚本

```shell
yum remove -y vsftpd
rm -rf /etc/vsftpd/
rm -rf /etc/pam.d/vsftpd*
userdel ftpuser
rm -rf /ftpdata
yum install -y vsftpd
systemctl enable vsftpd.service
useradd ftpuser -d /ftpdata -s /bin/false
chmod -R 777 /ftpdata
cat > /etc/vsftpd/vsftpd.conf <<EOF
# Example config file /etc/vsftpd/vsftpd.conf
#
# The default compiled in settings are fairly paranoid. This sample file
# loosens things up a bit, to make the ftp daemon more usable.
# Please see vsftpd.conf.5 for all compiled in defaults.
#
# READ THIS: This example file is NOT an exhaustive list of vsftpd options.
# Please read the vsftpd.conf.5 manual page to get a full idea of vsftpd's
# capabilities.
#
# Allow anonymous FTP? (Beware - allowed by default if you comment this out).
anonymous_enable=NO
#
# Uncomment this to allow local users to log in.
# When SELinux is enforcing check for SE bool ftp_home_dir
local_enable=YES
#
# Uncomment this to enable any form of FTP write command.
write_enable=YES
#
# Default umask for local users is 077. You may wish to change this to 022,
# if your users expect that (022 is used by most other ftpd's)
local_umask=022
#
# Uncomment this to allow the anonymous FTP user to upload files. This only
# has an effect if the above global write enable is activated. Also, you will
# obviously need to create a directory writable by the FTP user.
# When SELinux is enforcing check for SE bool allow_ftpd_anon_write, allow_ftpd_full_access
#anon_upload_enable=YES
#
# Uncomment this if you want the anonymous FTP user to be able to create
# new directories.
anon_mkdir_write_enable=YES
#
# Activate directory messages - messages given to remote users when they
# go into a certain directory.
dirmessage_enable=YES
#
# Activate logging of uploads/downloads.
xferlog_enable=YES
#
# Make sure PORT transfer connections originate from port 20 (ftp-data).
connect_from_port_20=YES
#
# If you want, you can arrange for uploaded anonymous files to be owned by
# a different user. Note! Using "root" for uploaded files is not
# recommended!
chown_uploads=YES
#chown_username=whoever
#
# You may override where the log file goes if you like. The default is shown
# below.
#xferlog_file=/var/log/xferlog
#
# If you want, you can have your log file in standard ftpd xferlog format.
# Note that the default log file location is /var/log/xferlog in this case.
xferlog_std_format=YES
#
# You may change the default value for timing out an idle session.
#idle_session_timeout=600
#
# You may change the default value for timing out a data connection.
#data_connection_timeout=120
#
# It is recommended that you define on your system a unique user which the
# ftp server can use as a totally isolated and unprivileged user.
#nopriv_user=ftpsecure
#
# Enable this and the server will recognise asynchronous ABOR requests. Not
# recommended for security (the code is non-trivial). Not enabling it,
# however, may confuse older FTP clients.
async_abor_enable=YES
#
# By default the server will pretend to allow ASCII mode but in fact ignore
# the request. Turn on the below options to have the server actually do ASCII
# mangling on files when in ASCII mode. The vsftpd.conf(5) man page explains
# the behaviour when these options are disabled.
# Beware that on some FTP servers, ASCII support allows a denial of service
# attack (DoS) via the command "SIZE /big/file" in ASCII mode. vsftpd
# predicted this attack and has always been safe, reporting the size of the
# raw file.
# ASCII mangling is a horrible feature of the protocol.
ascii_upload_enable=YES
#ascii_download_enable=YES
#
# You may fully customise the login banner string:
ftpd_banner=Welcome to blah FTP service.
#
# You may specify a file of disallowed anonymous e-mail addresses. Apparently
# useful for combatting certain DoS attacks.
#deny_email_enable=YES
# (default follows)
#banned_email_file=/etc/vsftpd/banned_emails
#
# You may specify an explicit list of local users to chroot() to their home
# directory. If chroot_local_user is YES, then this list becomes a list of
# users to NOT chroot().
# (Warning! chroot'ing can be very dangerous. If using chroot, make sure that
# the user does not have write access to the top level directory within the
# chroot)
chroot_local_user=YES
#chroot_list_enable=YES
# (default follows)
#chroot_list_file=/etc/vsftpd/chroot_list
#
# You may activate the "-R" option to the builtin ls. This is disabled by
# default to avoid remote users being able to cause excessive I/O on large
# sites. However, some broken FTP clients such as "ncftp" and "mirror" assume
# the presence of the "-R" option, so there is a strong case for enabling it.
#ls_recurse_enable=YES
#
# When "listen" directive is enabled, vsftpd runs in standalone mode and
# listens on IPv4 sockets. This directive cannot be used in conjunction
# with the listen_ipv6 directive.
listen=YES
#
# This directive enables listening on IPv6 sockets. By default, listening
# on the IPv6 "any" address (::) will accept connections from both IPv6
# and IPv4 clients. It is not necessary to listen on *both* IPv4 and IPv6
# sockets. If you want that (perhaps because you want to listen on specific
# addresses) then you must run two copies of vsftpd with two configuration
# files.
# Make sure, that one of the listen options is commented !!
listen_ipv6=NO

pam_service_name=vsftpd
userlist_enable=YES
tcp_wrappers=YES

# 新加
use_localtime=YES
listen_port=21
idle_session_timeout=300
guest_enable=YES
guest_username=ftpuser
user_config_dir=/etc/vsftpd/vconf
data_connection_timeout=1
virtual_use_local_privs=YES
pasv_min_port=40000
pasv_max_port=40010
accept_timeout=5
connect_timeout=1
allow_writeable_chroot=YES
EOF
echo -e "ftpuser\n123456" > /etc/vsftpd/virtusers
db_load -T -t hash -f /etc/vsftpd/virtusers /etc/vsftpd/virtusers.db
chmod 600 /etc/vsftpd/virtusers.db 
cp /etc/pam.d/vsftpd /etc/pam.d/vsftpd.bak
cat > /etc/pam.d/vsftpd <<EOF
#%PAM-1.0
session    optional     pam_keyinit.so    force revoke
#auth       required	pam_listfile.so item=user sense=deny file=/etc/vsftpd/ftpusers onerr=succeed
#auth       required	pam_shells.so
#auth       include	password-auth
#account    include	password-auth
session    required     pam_loginuid.so
session    include	password-auth
auth sufficient /lib64/security/pam_userdb.so db=/etc/vsftpd/virtusers 
account sufficient /lib64/security/pam_userdb.so db=/etc/vsftpd/virtusers 
EOF
mkdir /etc/vsftpd/vconf
cat > /etc/vsftpd/vconf/ftpuser <<EOF
local_root=/ftpdata
write_enable=YES
anon_world_readable_only=NO
anon_upload_enable=YES
anon_mkdir_write_enable=YES
anon_other_write_enable=YES
EOF
service vsftpd restart
```

## NFS Linux

准备 nfs.sh 一件执行安装

```shell
sudo yum install nfs-utils -y
sudo systemctl enable rpcbind
sudo systemctl enable nfs
sudo systemctl start rpcbind
sudo systemctl start nfs
sudo mkdir /nfsdata
# 设置目录的读写权限，不然在 Windows 目录挂载后
sudo chmod 777 /nfsdata
echo -e "/nfsdata/   *(rw,sync,no_root_squash,no_all_squash)" > /etc/exports
sudo systemctl restart nfs
chown -R nfsnobody:nfsnobody /nfsdata/ # 所有者:组，分别在 /etc/passwd 与 /etc/group 目录下
```

Windows 开启 Linux 子系统和 NFS 全套工具重启

```shell
mount \\192.168.10.191\nfsdata x:
net use x: /delete 
# 在 Linux 下直接 unmount x:
```

## Samaba Linux

```shell
yum -y install samba
systemctl enable smb.service
systemctl enable nmb.service
cp -p /etc/samba/smb.conf /etc/samba.conf.bak
```

```shell
cat > /etc/vsftpd/vsftpd.conf <<EOF
[global]
    workgroup = SAMBA
    security = user

    passdb backend = tdbsam

    printing = cups
    printcap name = cups
    load printers = yes 
    cups options = raw 

[homes]
    comment = Home Directories
    valid users = %S, %D%w%S
    browseable = No
    read only = No
    inherit acls = Yes 

[printers]
    comment = All Printers
    path = /var/tmp
    printable = Yes 
    create mask = 0600
    browseable = No

[print$]
    comment = Printer Drivers
    path = /var/lib/samba/drivers
    write list = @printadmin root
    force group = @printadmin
    create mask = 0664
    directory mask = 0775

[public]
    comment = Public Stuff
    path = /inner_share
    browseable = yes
    guest ok = yes
    writeable = yes
    public=yes
EOF
mkdir /inner_share
useradd smbuser
smbpasswd -a smbuser
chown -R smbuser:smbuser /inner_share
systemctl restart smb.service
systemctl status smb.service
```

重启连接上了之后 public 就是 /inner_share 目录, smbuser 就是 /home/smbuser 目录

## HTTP URL Method

在协议代理中有 HTTP Method 的拦截测试，使用 python 服务器与 curl

```python
from http.server import BaseHTTPRequestHandler, HTTPServer

class handler(BaseHTTPRequestHandler):
    def do_GET(self):
        self.send_response(200)
        self.send_header('Content-type','text/html')
        self.end_headers()

        message = "Hello, World! Here is a GET response\n"
        self.wfile.write(bytes(message, "utf8"))
    def do_POST(self):
        self.send_response(200)
        self.send_header('Content-type','text/html')
        self.end_headers()

        message = "Hello, World! Here is a POST response\n"
        self.wfile.write(bytes(message, "utf8"))

with HTTPServer(('', 80), handler) as server:
    server.serve_forever()
```

```shell
curl --location --request POST "http://192.168.11.183"
```

如果添加黑名单策列，返回的是

```shell
curl: (52) Empty reply from server
```

