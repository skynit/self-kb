---
title: Samba 部署场景
created: 2026-06-23
updated: 2026-06-23
type: reference
domain: Samba
tags:
  - OS
  - Samba
  - 部署
  - AD
---

# Samba 部署场景

> 属于 [[Samba学习索引]] | 相关：[[smb-conf配置详解]]、[[Samba架构与组件]]

---

## 五种部署模式

```
┌──────────────────────────────────────────────────────────────┐
│  ① 独立服务器     ② NT4域成员    ③ AD域成员    ④ AD域控  ⑤ NT4 PDC │
│  security=USER     security=     security=    server role=  server role=    │
│                    DOMAIN         ADS          DC            PDC           │
│  简单共享          旧式(弃用)     企业标准      全功能DC      旧式(弃用)    │
│  ★推荐入门        ★不推荐       ★推荐        ★高级        ★不推荐       │
└──────────────────────────────────────────────────────────────┘
```

---

## ① 独立服务器（security = USER）

### 适用场景

家庭/小公司文件共享，无域控制器。

### 步骤

```bash
# 1. 安装
apt install samba      # Debian/Ubuntu
yum install samba      # RHEL/CentOS

# 2. 配置
cat > /etc/samba/smb.conf << 'EOF'
[global]
    workgroup = WORKGROUP
    server min protocol = SMB2
    security = USER
    map to guest = Bad User

[data]
    path = /srv/samba/data
    read only = no
    valid users = alice, bob
EOF

# 3. 创建目录
mkdir -p /srv/samba/data
chown root:root /srv/samba/data
chmod 0775 /srv/samba/data

# 4. 创建用户
smbpasswd -a alice     # 添加 Samba 用户（需先有系统用户）
smbpasswd -a bob

# 5. 验证配置
testparm

# 6. 启动
systemctl restart smbd nmbd

# 7. 测试
smbclient -L localhost -U alice
smbclient //localhost/data -U alice
```

---

## ③ AD 域成员（security = ADS）

### 适用场景

企业环境，Samba 服务器加入 AD 域，域用户直接访问共享。

### 步骤

```bash
# 1. 安装
apt install samba winbind krb5-config

# 2. 配置 Kerberos
cat > /etc/krb5.conf << 'EOF'
[libdefaults]
    default_realm = MYDOMAIN.COM
    dns_lookup_realm = true
    dns_lookup_kdc = true

[realms]
    MYDOMAIN.COM = {
        kdc = dc1.mydomain.com
        admin_server = dc1.mydomain.com
    }
EOF

# 3. 测试 Kerberos
kinit administrator@MYDOMAIN.COM

# 4. 配置 Samba
cat > /etc/samba/smb.conf << 'EOF'
[global]
    workgroup = MYDOMAIN
    realm = MYDOMAIN.COM
    security = ADS
    server min protocol = SMB2
    kerberos method = secrets and keytab

    idmap config * : backend = tdb
    idmap config * : range = 3000-7999
    idmap config MYDOMAIN : backend = rid
    idmap config MYDOMAIN : range = 10000-999999

    winbind use default domain = yes
    winbind enum users = yes
    winbind enum groups = yes

[data]
    path = /srv/samba/data
    read only = no
    valid users = @"MYDOMAIN\Domain Users"
EOF

# 5. 加入域
net ads join -U administrator

# 6. 启动
systemctl restart smbd winbind

# 7. 验证
wbinfo -t               # 测试域信任
wbinfo -u               # 列出域用户
id MYDOMAIN\\alice      # 查看域用户 UID/GID
```

### NSS/PAM 配置

```bash
# /etc/nsswitch.conf 修改
passwd:         files winbind
group:          files winbind

# 这样域用户可以用域身份登录 Linux
ssh alice@linux-server   # alice 是域用户
```

---

## ④ AD 域控制器

### 适用场景

Samba 作为完整 AD DC，替代 Windows Server。

### 步骤

```bash
# 1. 安装
apt install samba winbind krb5-config

# 2. 初始化 DC
samba-tool domain provision \
    --realm=mydomain.com \
    --domain=MYDOMAIN \
    --server-role=dc \
    --dns-backend=SAMBA_INTERNAL \
    --use-rfc2307

# 3. 启动（不用 smbd，用 samba 进程）
systemctl start samba    # 或 samba-ad-dc

# 4. 管理
samba-tool user create alice
samba-tool user list
samba-tool group add developers
samba-tool group addmembers developers alice
```

> AD DC 模式下，`smbd` 不单独运行，`samba` 进程包含所有功能（DNS、KDC、LDAP、文件共享）。

---

## 客户端访问

### Windows

```
资源管理器 → \\server\share
或映射网络驱动器 → Z: → \\192.168.1.10\data
```

### Linux

```bash
# smbclient（命令行）
smbclient //server/data -U alice

# mount（挂载）
mount -t cifs //server/data /mnt/data \
    -o user=alice,vers=3.0,sec=ntlmssp

# /etc/fstab 持久挂载
//server/data  /mnt/data  cifs  user=alice,password=xxx,vers=3.0  0 0

# 自动挂载（autofs）
# /etc/auto.master
/mnt  /etc/auto.smb

# /etc/auto.smb
data  -fstype=cifs,vers=3.0 ://server/data
```

---

> 回到 [[Samba学习索引]]
