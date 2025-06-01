# Nikto-服务器安全漏洞扫描


<!--more-->
## 工具简介

Nikto 是一款开源的 web 服务器扫描工具，旨在检测 web 服务器的安全漏洞。它通过测试多个 web 服务器并扫描已知的漏洞，如危险文件、错误配置、缺失的补丁等，为安全研究人员和系统管理员提供了强大的扫描功能。Nikto 能够识别超过 6700 个潜在危险的文件/CGIs，检查超过 1250 个 web 服务器版本，并提供大量服务器和扫描插件。

## 功能与特点

- 开源免费：Nikto 是开源工具，免费供个人和企业使用。
- 多协议支持：支持 HTTP、HTTPS、HTTPd、SunONE、Apache、IIS 等多种协议和服务器类型。
- 广泛的漏洞库：拥有丰富的漏洞库，能够检测大量已知的漏洞和安全问题。
- 易于使用：简单的命令行界面，易于集成到自动化脚本中。
- 插件支持：支持使用插件进行扩展，提供自定义扫描功能。 

## 使用教程
### 安装 Nikto
#### 在 Linux 上安装
更新系统包管理器，打开终端，运行以下命令更新系统包管理器：

`sudo apt update`

安装 Nikto,使用包管理器安装 Nikto：

`sudo apt install nikto`

#### 在 Windows 上安装

**下载 Nikto：**

从 Nikto 的 官方 GitHub 仓库 下载最新版本的 Nikto 压缩包，并解压到本地目录。

**安装 Perl：**

Nikto 依赖于 Perl，请确保系统安装了 Perl。可以从 Strawberry Perl 下载并安装。

**配置环境变量：**

将 Perl 和 Nikto 的可执行文件路径添加到系统环境变量中，以便在命令提示符中直接运行 Nikto。

### 使用 Nikto 进行基本扫描

打开终端或命令提示符,运行 Nikto 扫描命令：

使用以下命令对目标 web 服务器进行扫描：

`nikto -h http://example.com`

其中，http://example.com 是目标服务器的 URL。执行此命令后，Nikto 将开始扫描目标服务器并输出扫描结果。

### 高级功能
#### 指定扫描端口：

可以通过 -p 参数指定要扫描的端口，例如：

`nikto -h http://example.com -p 8080`

#### 保存扫描结果：

使用 -o 参数将扫描结果保存到文件中，指定格式为 -Format，例如：

`nikto -h http://example.com -o scan_results.html -Format htm`

#### 忽略 SSL 证书检查：

使用 -ssl 参数忽略 SSL 证书检查，对 HTTPS 服务器进行扫描：

`nikto -h https://example.com -ssl`

#### 使用插件：

可以使用 -Plugins 参数加载特定插件进行扫描：

`nikto -h http://example.com -Plugins "apacheusers"`


### 生成详细报告

导出扫描结果：

使用 -output 参数导出扫描结果到指定文件中：

`nikto -h http://example.com -output /path/to/report.txt`

生成 HTML 报告：

使用 -Format 参数指定输出格式为 HTML，并保存报告：

`nikto -h http://example.com -o /path/to/report.html -Format htm`


## 总结

Nikto 是一款功能强大的 web 服务器扫描工具，通过其丰富的漏洞库和简单的命令行界面，可以有效地检测 web 服务器的安全问题。本文详细介绍了 Nikto 的安装、基本使用和高级扫描选项，帮助用户快速掌握该工具的使用方法。

在实际操作中，请务必遵守相关法律法规，仅在合法授权的情况下使用该工具。通过使用 Nikto，安全研究人员和系统管理员可以更加全面地了解 web 服务器的安全状况，及时发现并修复潜在的安全漏洞，提高系统的整体安全性。
