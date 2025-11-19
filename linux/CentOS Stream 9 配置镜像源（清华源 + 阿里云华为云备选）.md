### **CentOS Stream 9 配置镜像源（清华源 + 阿里云/华为云备选）**

以下是一份 **清晰、完整** 的配置流程，支持 **清华源（首选）**，如果清华源不可用则自动切换至 **阿里云** 或 **华为云**。

------

## **1. 备份原有仓库配置**

bash

```
sudo mkdir -p /etc/yum.repos.d/backup
sudo mv /etc/yum.repos.d/*.repo /etc/yum.repos.d/backup/
```

------

## **2. 配置清华源（首选）**

创建 `/etc/yum.repos.d/CentOS-Stream.repo`：

bash

```
sudo tee /etc/yum.repos.d/CentOS-Stream.repo <<'EOF'
[base]
name=CentOS Stream $releasever - Base - Tsinghua
baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos-stream/9-stream/BaseOS/$basearch/os/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial
enabled=1

[appstream]
name=CentOS Stream $releasever - AppStream - Tsinghua
baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos-stream/9-stream/AppStream/$basearch/os/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial
enabled=1

[extras]
name=CentOS Stream $releasever - Extras - Tsinghua
baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos-stream/9-stream/extras/$basearch/os/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial
enabled=0  # 默认禁用，避免 404 错误
EOF
```

------

## **3. 测试清华源是否可用**

bash

```
sudo dnf clean all
sudo dnf makecache
```

如果出现 `404` 错误（如 `extras` 仓库不可用），可以 **临时禁用**：

bash

```
sudo sed -i 's|^enabled=1|enabled=0|g' /etc/yum.repos.d/CentOS-Stream.repo
```

------

## **4. 配置备选镜像源（阿里云/华为云）**

如果清华源访问不稳定，可以 **一键切换** 至阿里云或华为云：

### **切换至阿里云**

bash

```
sudo sed -i 's|mirrors.tuna.tsinghua.edu.cn|mirrors.aliyun.com|g' /etc/yum.repos.d/CentOS-Stream.repo
sudo dnf clean all
sudo dnf makecache
```

### **切换至华为云**

bash

```
sudo sed -i 's|mirrors.tuna.tsinghua.edu.cn|repo.huaweicloud.com|g' /etc/yum.repos.d/CentOS-Stream.repo
sudo dnf clean all
sudo dnf makecache
```

------

## **5. 检查当前使用的镜像源**

bash

```
sudo dnf repolist -v | grep -E "Repo-id|baseurl"
```

输出示例：

text

```
Repo-id      : base
baseurl      : https://mirrors.tuna.tsinghua.edu.cn/centos-stream/9-stream/BaseOS/x86_64/os/
Repo-id      : appstream
baseurl      : https://mirrors.tuna.tsinghua.edu.cn/centos-stream/9-stream/AppStream/x86_64/os/
```

------

## **6. 更新系统**

bash

```
sudo dnf update -y
```

------

## **7. 自动切换策略（可选）**

如果希望 **自动检测并切换镜像源**，可以使用以下脚本：

下载

```
#!/bin/bash

# 尝试清华源
sudo sed -i 's|mirrors.aliyun.com|mirrors.tuna.tsinghua.edu.cn|g' /etc/yum.repos.d/CentOS-Stream.repo
sudo sed -i 's|repo.huaweicloud.com|mirrors.tuna.tsinghua.edu.cn|g' /etc/yum.repos.d/CentOS-Stream.repo

if ! sudo dnf makecache; then
    echo "清华源不可用，切换至阿里云..."
    sudo sed -i 's|mirrors.tuna.tsinghua.edu.cn|mirrors.aliyun.com|g' /etc/yum.repos.d/CentOS-Stream.repo
    sudo dnf clean all
    sudo dnf makecache
fi
```

保存为 `switch-repo.sh`，并赋予执行权限：

bash

```
chmod +x switch-repo.sh
./switch-repo.sh
```

------

## **总结**

| 步骤                     | 操作         | 命令                                                       |      |        |                                         |
| :----------------------- | :----------- | :--------------------------------------------------------- | ---- | ------ | --------------------------------------- |
| **1. 备份原有配置**      | 防止出错回滚 | `sudo mv /etc/yum.repos.d/*.repo /etc/yum.repos.d/backup/` |      |        |                                         |
| **2. 配置清华源**        | 首选镜像     | `sudo tee /etc/yum.repos.d/CentOS-Stream.repo <<'EOF'`     |      |        |                                         |
| **3. 测试清华源**        | 检查是否可用 | `sudo dnf makecache`                                       |      |        |                                         |
| **4. 备选阿里云/华为云** | 一键切换     | `sed -i 's                                                 | tuna | aliyun | g' /etc/yum.repos.d/CentOS-Stream.repo` |
| **5. 检查当前源**        | 确认生效     | `dnf repolist -v | grep -E "Repo-id|baseurl"`              |      |        |                                         |
| **6. 更新系统**          | 应用更改     | `sudo dnf update -y`                                       |      |        |                                         |

这样，你的 CentOS Stream 9 就可以 **稳定使用清华源**，并在异常时 **自动切换备选镜像**！🚀