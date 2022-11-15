---
title: fileRight 文件权限
---

Linux 是支持多用户管理的，不同的用户有不同的操作文件权限，将文件划分为三个分类
拥有者（Owner/User）即创建该文件者
用户组（Group）
其他（Other）
root 拥有最高权限

<TerminalBlock title="查看文件权限">ls -l</TerminalBlock>

result: `drwxr-xr-x 5 macos staff 160 Feb 23 16:27 aliyun`
其中的 drwxr-xr-x 描述了当前文件的权限
将其切割为 4 个分段来理解 d rwx r-x r-x

- 第一个分段：d

  描述当前的文件类型。目录：d、文件：-

- 第二个分段：rwx

  描述文件(Owner\User)的权限

- 第三个分段：r-x

  描述文件(Group)的权限

- 第四个分段：r-x

  描述文件(Other)的权限

一个文件通常有两种操作：读(read)、写(write)、也就是权限信息中的`r`和`w`
`x`代表可执行(execute)
他们的顺序是：rwx
缺少时使用`-`占位

```
// 数字映射关系
{
    r: 4,
    w: 2,
    x: 1,
    -: 0
}
// 数字相加等于权限相加
// 例如: 7 = r + w + x
```

#### chmod

<div className="mb-4">
  <TerminalBlock title="只有文件的拥有者才可以读（read）和写（write）">
    chmod u=rw id_rsa
  </TerminalBlock>
</div>

<div className="mb-4">
  <TerminalBlock title="给组和其他用户赋予可读（read）权限">
    chmod go=r id_rsa
  </TerminalBlock>
</div>

<div className="mb-4">
  <TerminalBlock title="给文件拥有者和组有增加写入操作（write）、解除其他用户可写入（write）权限">
    chmod ug+w,o-w id_rsa
  </TerminalBlock>
</div>

<TerminalBlock title="给文件拥有者赋予所有的权限，而组和其他用户没有操作该文件的权限">
  chmod 700 id_rsa
</TerminalBlock>

[更详细](https://www.runoob.com/linux/linux-comm-chmod.html)
