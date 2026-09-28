<div align="left">
  <table>
    <tr>
      <td>
        <img src="icons/winboat_logo.svg" alt="WinBoat Logo" width="150">
      </td>
      <td>
        <h1 style="color: #7C86FF; margin: 0; font-size: 32px;">WinBoat</h1>
        <p style="color: oklch(90% 0 0); font-size: 14px; margin: 5px 0;">给企鹅的 Windows。<br>
        在 🐧 Linux 上运行 Windows 应用，并拥有 ✨ 无缝集成体验</p>
      </td>
    </tr>
  </table>
</div>

> 📄 本文档是 [WinBoat](https://github.com/TibixDev/winboat) 上游 README 的简体中文翻译，仅供中文用户参考。若翻译与英文原版有出入，一切以[英文原版](README.md)为准。

## 界面截图

<div align="center">
  <img src="gh_assets/features/feat_dash.png" alt="WinBoat 仪表盘" width="45%">
  <img src="gh_assets/features/feat_apps.png" alt="WinBoat 应用列表" width="45%">
  <img src="gh_assets/features/feat_native.png" alt="原生 Windows 窗口" width="45%">
</div>

## ⚠️ 开发中 ⚠️

WinBoat 目前处于 Beta 阶段，因此偶尔会遇到小毛病和 Bug。如果你打算尝试，最好具备一定的故障排查能力；不过我们仍然鼓励你试一试。

## 功能特性

- **🎨 优雅的界面**：简洁直观的界面，把 Windows 无缝融入你的 Linux 桌面环境，用起来就像原生体验
- **📦 自动化安装**：通过界面完成简单的安装流程——选好你的偏好与规格，剩下的交给我们
- **🚀 运行任何应用**：只要能在 Windows 上跑，就能在 WinBoat 上跑。你可以在 Linux 环境中以原生系统级窗口的形式，使用完整的 Windows 应用生态
- **🖥️ 完整 Windows 桌面**：需要时可以进入完整的 Windows 桌面体验，也可以只运行单个应用并把它无缝融入你的 Linux 工作流
- **📁 文件系统集成**：你的家目录（home）会挂载到 Windows 中，两个系统之间可以毫无阻碍地共享文件
- **✨ 以及更多**：智能卡直通（Smartcard passthrough）、资源监控，以及持续新增的更多功能

## 工作原理

WinBoat 是一个 Electron 应用，它通过容器化方案让你在 Linux 上运行 Windows 应用。Windows 以虚拟机的形式运行在 Docker/Podman 容器内，我们通过 [WinBoat Guest Server](https://github.com/TibixDev/winboat/tree/main/guest_server) 与它通信，从 Windows 中取回我们需要的数据。为了把应用合成为原生系统级窗口，我们使用 FreeRDP 配合 Windows 的 RemoteApp 协议。

## 前置要求

在运行 WinBoat 之前，请确认你的系统满足以下要求：

- **内存**：至少 4 GB 内存
- **CPU**：至少 2 个 CPU 线程
- **存储**：你所选安装目录对应的磁盘上至少 32 GB 可用空间
- **虚拟化**：在 BIOS/UEFI 中启用 KVM
    - [如何启用虚拟化](https://duckduckgo.com/?t=h_&q=how+to+enable+virtualization+in+%3Cmotherboard+brand%3E+bios&ia=web)
- **如果使用 Docker：**
  - **Docker**：容器化所必需
      - [安装指南](https://docs.docker.com/engine/install/)
      - **⚠️ 注意：** 不支持 Docker Desktop，使用它会遇到问题
  - **Docker Compose v2**：兼容 docker-compose.yml 文件所必需
      - [安装指南](https://docs.docker.com/compose/install/#plugin-linux-only)
  - **Docker 用户组**：把你的用户加入 `docker` 组
      - [配置说明](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user)
- **如果使用 Podman：**
  - **Podman**：容器化所必需
      - [安装指南](https://podman.io/docs/installation#installing-on-linux)
      - 在 Debian/Ubuntu 及其衍生版上，用 `apt install` 装到的 Podman 版本可能过旧。请确保版本为 **4.x.x** 或更高，以保证安装顺利完成。
  - **Podman Compose**：兼容 podman-compose.yml 文件所必需
      - [安装指南](https://github.com/containers/podman-compose?tab=readme-ov-file#installation)
- **FreeRDP**：远程桌面连接所必需（请确保版本为 **3.x.x** 且包含声音支持）
    - [安装指南](https://github.com/FreeRDP/FreeRDP/wiki/PreBuilds)
- [可选] **内核模块**：可以加载 `iptables` / `nftables` 内核模块以获得更好的网络性能，但在较新版本的 WinBoat 中并非必需
    - [模块加载说明](https://rentry.org/rmfq2e5e)

## 下载

你可以在 [Releases](https://github.com/TibixDev/winboat/releases) 标签页下载最新的 Linux 构建版本。我们目前提供四种形式：

- **AppImage：** 一种流行且便携的应用格式，在大多数发行版上应该都能正常运行
- **Unpacked（未打包）：** 原始的未打包文件，直接运行可执行文件即可（`linux-unpacked/winboat`）
- **.deb：** Debian 系发行版的官方格式
- **.rpm：** Fedora 系发行版的官方格式
- **Nix (Nixpkgs)**
    1. 把 winboat 包加入你的配置（确保使用 nixpkgs-unstable）
    使用 `environment.systemPackages = [pkgs.winboat];`；若使用 home manager 则用 `home.packages = [pkgs.winboat];`。
    2. 在你的 Nix 配置中加入以下内容
    ```nix
    virtualisation.docker.enable = true;
    users.users.{yourUser}.extraGroups = ["docker"];
    ```

## 容器运行时的已知问题

- 目前**不支持** Docker Desktop
- 目前**不支持**通过 Podman 进行 USB 直通

## 构建 WinBoat

- 构建需要系统已安装 Bun 与 Go
- 克隆仓库（`git clone https://github.com/TibixDev/WinBoat`）
- 安装依赖（`bun i`）
- 使用 `bun run build:linux-gs` 构建应用与 guest server
- 构建产物可在 `dist` 目录下找到，包含一个 AppImage 和一个 Unpacked 版本

## 以开发模式运行 WinBoat

- 确认已满足[前置要求](#前置要求)
- 此外，开发还需系统已安装 Bun 与 Go
- 克隆仓库（`git clone https://github.com/TibixDev/WinBoat`）
- 安装依赖（`bun i`）
- 构建 guest server（`bun run build:gs`）
- 运行应用（`bun run dev`）

## 参与贡献

欢迎贡献！无论是修 Bug、改进功能，还是更新文档，我们都感谢你帮助 WinBoat 变得更好。

**请注意**：我们只关注技术层面的贡献。包含政治/色情内容，或其他敏感/争议话题的 Pull Request 将不被接受。让我们把注意力集中在做出优秀软件上！🚀

欢迎你：

- 报告 Bug 和问题
- 提交功能需求
- 贡献代码改进
- 协助编写文档
- 分享反馈和建议

查看我们的 issues 页面开始参与，或者如果你发现了需要关注的问题，直接开一个新的 issue。

## 许可证

WinBoat 采用 [MIT](https://github.com/TibixDev/winboat/blob/main/LICENSE) 许可证。

## 灵感来源 / 同类替代品

过去几年出现了一些概念相似的很酷的项目，我们也从中获得了一些灵感。\
它们都很棒，值得你去看看：

- [WinApps](https://github.com/winapps-org/winapps)
- [Cassowary](https://github.com/casualsnek/cassowary)
- [dockur/windows](https://github.com/dockur/windows)（🌟 同样被 WinBoat 所使用）

## 社交媒体与联系方式

- 官网：https://www.winboat.app/
- Twitter/X：[@winboat_app](https://x.com/winboat_app)
- Mastodon：[@winboat](https://fosstodon.org/@winboat)
- Bluesky：[winboat.app](http://bsky.app/profile/winboat.app)
- Discord：[加入社区](http://discord.gg/MEwmpWm4tN)
- 邮箱：staff@winboat.app
- [向 DeepWiki 提问](https://deepwiki.com/TibixDev/winboat)

## Star 历史

<a href="https://www.star-history.com/?repos=winboat-org%2Fwinboat&type=date&legend=top-left">
 <picture>
   <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/chart?repos=winboat-org/winboat&type=date&theme=dark&legend=top-left" />
   <source media="(prefers-color-scheme: light)" srcset="https://api.star-history.com/chart?repos=winboat-org/winboat&type=date&legend=top-left" />
   <img alt="Star History Chart" src="https://api.star-history.com/chart?repos=winboat-org/winboat&type=date&legend=top-left" />
 </picture>
</a>
