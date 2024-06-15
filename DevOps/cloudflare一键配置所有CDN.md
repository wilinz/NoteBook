# Cloudflare一键配置所有CDN 

Github 传送门：[https://github.com/wilinz/cloudflare_dns_proxy](https://github.com/wilinz/cloudflare_dns_proxy)

此 Python 脚本允许用户管理其 Cloudflare 账户中 DNS 记录的代理设置。具体来说，它提供了为给定域名的 DNS 记录启用或禁用代理状态的功能。

## 先决条件

在运行脚本之前，请确保你具备以下先决条件：

- 系统上安装了 Python 3.x。
- Python 中安装了 `requests` 库。你可以使用 `pip install requests` 命令来安装。
- 一个有效的 Cloudflare API 令牌，且该令牌具有读写 DNS 记录的必要权限。

## 配置

1. 将 `'your_api_token'` 替换为你的实际 Cloudflare API 令牌。
2. 将 `'example.com'` 替换为你想要管理的域名。

## 使用方法

要运行脚本，请使用以下命令：

```bash
python manage_dns_proxy.py
```

当系统提示时，输入你想要启用（`e`）或禁用（`d`）DNS 记录代理的操作。然后脚本将执行以下操作：

- 获取你 Cloudflare 账户中所有区域的列表。
- 找到与你域名对应的区域 ID。
- 获取该区域的 DNS 记录。
- 可选地将启用代理的 DNS 记录保存到名为 `proxied_records.json` 的 JSON 文件中。
- 根据你的选择启用或禁用代理。

如果已经存在一个 `proxied_records.json` 文件，系统会询问你是否使用它，或者从 Cloudflare API 获取新的记录。

## 文件描述

- `manage_dns_proxy.py`：与 Cloudflare API 交互的主要 Python 脚本。
- `proxied_records.json`：存储启用了代理的 DNS 记录的 JSON 文件，可用于离线管理代理设置。

## 注意事项

- 脚本在禁用代理时设置 TTL 为 600 秒，在启用时设置为 1 秒（自动）。
- 确保使用的 API 令牌具有适当的权限，以避免未经授权的错误。

## 免责声明

使用此脚本需自行承担风险。请始终确保你有备份，并且了解你对 DNS 记录所做的更改。

## 支持

如有任何问题或疑问，请在托管此脚本的 GitHub 仓库中提出问题。

## 许可证

此脚本根据 MIT 许可证发布。有关完整详情，请参阅仓库中的 `LICENSE` 文件。