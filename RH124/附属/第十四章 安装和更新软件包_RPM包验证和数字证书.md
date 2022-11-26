# Linux RPM包验证和数字证书

[louis](http://www.found5.com/user/index.html?id=2) • 2019-10-18 00:25:04 • [linux系统](http://www.found5.com/linux.html) • 阅读 199

执行rpm -qa命令可以看到，Linux系统中装有大量的RPM包，且每个包都含有大量的安装文件。因此，为了能够及时发现文件误删、误修改文件数据、恶意篡改文件内容等问题，Linux提供了以下两种监控（检测）方式：

- RPM 包校验：其实就是将已安装文件和 /var/lib/rpm/ 目录下的数据库内容进行比较，确定文件内容是否被修改。
- RPM 包数字证书校验：用来校验RPM包本身是否被修改。

## Linux RPM 包校验

RPM 包校验可用来判断已安装的软件包（或文件）是否被修改，此方式可使用的命令格式分为以下 3 种。

```
[root@localhost ~]# rpm -Va
```

-Va 选项表示校验系统中已安装的所有软件包。

```
[root@localhost ~]# rpm -V 已安装的包名
```

-V 选项表示校验指定 RPM 包中的文件，是 verity 的首字母。

```
[root@localhost ~]# rpm -Vf 系统文件名
```

-Vf 选项表示校验某个系统文件是否被修改。例如我们校验 apache 软件包中所有的安装文件是否被修改，可执行如下命令：

```
[root@localhost -]# rpm -V httpd
```

可以看到，执行后无任何提示信息，表明所有用 apache 软件包安装的文件均未改动过，还和从原软件包安装的文件一样。接下来尝试对 apache 的配置文件 /etc/httpd/conf/httpd.conf 做适当修改，修改格式如下：

```
[root@localhost ~]#vim /etc/httpd/conf/httpd.conf
...省略部分内容...
Directorylndex index.html index.html.var index.php
#这句话是定义apache可以识别的默认网页文件名。在后面加入了index.php
#这句话大概有400行左右
…省略部分内容...
```

由于我们还未学习如何配置 apache，为防止其崩溃，这里仅尝试修改 apache 的默认网页文件。按照以上格式对文件进行修改后保存退出，再次使用 rpm -V 命令对 apache 软件包进行验证：

```
[root@localhost ~]# rpm -V httpd
S.5....T. c /etc/httpd/conf/httpd.conf
```

可以看到，结果显示了文件被修改的信息。该信息可分为以下 3 部分：

1. 最前面的 8 个字符（S.5....T）都属于验证信息，各字符的具体含义如下：

2. - S：文件大小是否改变。
   - M：文件的类型或文件的权限（rwx）是否改变。
   - 5：文件MD5校验和是否改变（可以看成文件内容是否改变）。
   - D：设备的主从代码是否改变。
   - L：文件路径是否改变。
   - U：文件的属主（所有者）是否改变。
   - G：文件的属组是否改变。
   - T：文件的修改时间是否改变。
   - .：若相关项没发生改变，用 . 表示。

3. 被修改文件类型，大致可分为以下几类：

4. - c：配置文件（configuration file）。
   - d：普通文档（documentation）。
   - g："鬼"文件（ghost file），很少见，就是该文件不应该被这个 RPM 包包含。
   - l：授权文件（license file）。
   - r：描述文件（read me）。

5. 被修改文件所在绝对路径（包含文件名）。

由此，S.5....T. c S.5....T. c /etc/httpd/conf/httpd.conf 表达的完整含义是：配置文件 httpd.conf 的大小、内容、修改时间被人为修改过。

注意，并非所有对文件做修改的行为都是恶意的。通常情况下，对配置文件做修改是正常的，比如说配置 apache 就要修改其配置文件，而如果验证信息提示对二进制文件做了修改，这就需要小心，除非是自己故意修改的。

## Linux RPM数字证书验证

RPM 包校验方法只能用来校验已安装的 RPM 包及其安装文件，如果 RPM 包本身就被动过手脚，此方法将无法解决问题，需要使用 RPM 数字证书验证方法。

简单的理解，<span style="color:red">RPM 包校验其实就是将现有安装文件与最初使用 RPM 包安装时的初始文件进行对比</span>，如果有改动则提示给用户，因此这种方式无法验证 RPM 包本身被修改的情况。

数字证书，又称数字签名，<span style="color:red">由软件开发商直接发布</span>。Linux 系统安装数字证书后，若 RPM 包做了修改，此包携带的数字证书也会改变，将无法与系统成功匹配，软件无法安装。可以将数字证书想象成自己的签名，是不能被模仿的（厂商的数字证书是唯一的），只有我认可的文件才会签名（只要是厂商发布的软件，都符合数字证书验证）；如果我的文件被人修改了，那么我的签名就会变得不同（如果软件改变，数字证书就会改变，从而通不过验证。当然，现实中人的手工签名不会直接改变，所以数字证书比手工签名还要可靠）。使用数字证书验证 RPM 包的方法具有如下 2 个特点：

1. 必须找到原厂的公钥文件，然后才能进行安装。
2. 安装 RPM 包会提取 RPM 包中的证书信息，然后和本机安装的原厂证书进行验证。如果验证通过，则允许安装；如果验证不通过，则不允许安装并发出警告。

数字证书默认会放到系统中/etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6位置处，通过以下命令也可验证：

```
#系统中的数字证书位置
[root@localhost ~]# ll /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
-rw-r--r--.1 root root 1706 6 月 26 17:29 /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
```

安装数字证书的命令如下：

```
[root@localhost ~]# rpm --import /efc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
```

--import表示导入数字证书

数字证书安装完成后，可使用如下命令进行验证：

```
[root@localhost ~]# rpm -qa|grep gpg-pubkey
gpg-pubkey-c105b9de-4e0fd3a3
```

可以看到，数字证书已成功安装。在装有数字证书的系统上安装 RPM 包时，系统会自动验证包的数字证书，验证通过则可以安装，反之将无法安装（系统会报错）。数字证书本身也是一个 RPM 包，因此可以用 rpm 命令查询数字证书的详细信息，也可以将其卸载。查询数字证书详细信息的命令如下：

```
[root@localhost ~]# rpm -qi gpg-pubkey-c105b9de-4e0fd3a3
#查询数字证书包的详细信息
Name : gpg-pubkey
Relocations: (not relocatable)
Version : c105b9de Vendor: (none)
Release : 4e0fd3a3 Build Date: 2012年11月12日 星期一 23时05分20秒
Install Date: 2012年11月12日星期一23时05分20秒 Build Host: local host
Group : Public Keys
Source RPM: (none)
Size : 0
License: pubkey
…省略部分输出…
-----END PGP PUBLIC KEY BLOCK----
```

卸载数字证书可以使用 -e 选项，命令如下：

```
[root@localhost ~]# rpm -e gpg-pubkey-c105b9de-4ead3a3
```

虽然数字证书可以手动卸载，但不推荐大家将其卸载。

> http://www.found5.com/view/896.html