# macOS 缓存、临时文件和用户数据

这些路径是检查入口。只处理现场确认存在且属于当前软件版本的项目。路径中的 `<ID>`、`<版本>`、`<配置档>` 用现场值替换。

| 来源 | 常见位置和回长触发 | 怎么处理 |
|---|---|---|
| 通用缓存与日志 | `~/Library/Caches/<ID>`、`~/Library/Logs/<应用>`；应用更新或重启会新建 | 验证归属及重建能力，退出相关进程后只清理具体缓存或过期日志。区分单纯日志与需要保留的诊断记录。 |
| 沙盒容器 | `~/Library/Containers/<ID>/Data/Library/Caches`、`…/Data/tmp`；`~/Library/Group Containers` 多为共享数据 | 优先使用应用内清理。只有确认为可重建且无占用的具体子目录才可考虑处理；整容器和共享容器包含数据、会话与设置，保留系统访问保护。 |
| Electron / Chromium / Codex | `~/Library/Application Support/<应用>/` 下的 `Cache`、`Code Cache`、`GPUCache`、`Dawn*`、`GraphiteDawnCache`、`component_crx_cache`、`extensions_crx_cache`；也查 `Default` 和新增配置档 | 清浏览器缓存优先用界面，仅选缓存类别。其余明确的缓存先退出主应用及相关写入者再处理。保留 Cookies、Local Storage、IndexedDB、Service Worker、Extensions、Preferences 和活跃 SQLite。Codex 的 `artifact-sessions` 可能承载可回看的产物，按数据迁移并验证，不按缓存直接删。 |
| Chrome 更新 | `~/Library/Application Support/Google/GoogleUpdater/crx_cache`、同层版本目录及系统级 Google 路径；更新后检查 `/Applications/Google Chrome.app` | 核对活跃更新进程与注册路径。确认闲置下载包可重建后才清该包；保留正在使用的版本和更新程序。无法识别的版本目录通过厂商管理方式处理。 |
| 罗技更新 | `/Library/Application Support/Logi/LogiOptionsPlus/cache`、`depots/<版本>` | cache 是候选，先识别其内容与活动更新；depots 可能包含正在运行的组件，按厂商维护方式处理。 |
| Xcode 构建 | `~/Library/Developer/Xcode/DerivedData`、`Archives`、`Products`、`DeviceLogs`、各项目的 `build`、`.xcresult`、`ModuleCache.noindex` | DerivedData 和临时构建产物通常能重建，停止相关构建后按项目清理。Archives、dSYM、发布包和测试报告按成果归档。为新构建使用 Xcode Locations 或显式 `-derivedDataPath`、`-resultBundlePath`、`-archivePath`，用真实构建检查项目覆盖。 |
| 模拟器和真机 | `~/Library/Developer/CoreSimulator/{Devices,Caches,Temp}`、`/Library/Developer/CoreSimulator`、系统管理的 runtime 映像；CoreDevice 真机缓存见本机基线 | 设备数据、运行环境与临时缓存分别盘点。用 `xcrun simctl runtime list -j`、`xcrun simctl list devices -j` 检查 build、最近使用与依赖设备；确认不用后从 Xcode Components 移除指定 runtime。使用设备管理功能处理过时设备，避免批量删除有测试状态的设备。真机安装缓存优先按受支持方式处理。 |
| 包管理器 | `~/.npm/{_cacache,_npx,_logs}`、pnpm store、`~/Library/Caches/pip`、Homebrew cache、uv cache、SwiftPM | 用工具自身命令回读实际目录，如 `npm config get cache`、`pnpm store path`、`python3 -m pip cache dir`、`uv cache dir`、`brew --cache`。优先使用各工具的清理命令，能预览时先预览，如 `brew cleanup --dry-run`。pnpm store 跨卷后留意硬链接或复制行为；保留已装工具链和虚拟环境。 |
| Final Cut Pro | 各资源库的 `.fcpbundle`、外置 `.fcpcache`、`~/Movies/Final Cut Backups` 或自定义位置 | 在资源库属性中设储存位置；用“删除生成的资源库文件”按需清渲染文件，代理与优化媒体只有在原素材可读并接受重建时才清。保留原素材、工程与必要备份；逐个验证实际使用的资源库。 |
| Blender / Godot / 生成工具 | `~/Library/Caches/Blender`、`~/Library/Caches/Godot`、应用偏好中的临时目录、项目内导入缓存及用户指定的输出目录 | 优先在应用或项目中设置 SSD 位置。确认渲染、烘焙、导入结果是否可重建；保留源素材、工程、自动保存及需要留档的输出。GUI 与后台进程分别验证。 |
| 下载、云盘与媒体 | `~/Downloads`、`~/Library/CloudStorage`、`~/Library/Mobile Documents`、`~/Pictures` | 通过软件设置改默认输出。云盘先确认同步完成，使用服务自身的“释放空间/移除下载”，避免通过 Finder 删除同步文件；对离线必需目录保留下载。Photos 用受支持的图库迁移流程，区分图库、云同步和备份。 |
| Outlook / Apple Mail | `~/Library/Group Containers/UBF8T346G9.Office/Outlook/Outlook 15 Profiles`、`~/Library/Containers/com.microsoft.Outlook`、`~/Library/Mail`、Mail Downloads | 应用本体与邮件库分开统计。用客户端设置管理下载，附件另存 SSD。清理旧客户端前验证同步、POP/“在我的 Mac 上”等本机资料及可恢复备份。Outlook 偏离默认资料目录可能影响搜索。 |
| iPhone 本地备份 | `~/Library/Application Support/MobileSync/Backup`；设备重新备份会增长 | 用 Finder 的备份管理识别、归档或删除明确不用的备份。迁移备份时保留整个备份结构，核对可识别性和备份日期，区分“能列出”与“已验证可恢复”。 |
| macOS 系统与快照 | `/private/var/folders`、`/private/var/vm`、Spotlight 索引、系统更新、APFS 本地快照 | 盘点可见占用，交给系统或所属软件管理。Time Machine 会按需回收本地快照；确有空间需求时再核对最近独立备份并用受支持方式处理，区分系统快照和 Time Machine 本地快照。 |

参考：[Apple 本地快照](https://support.apple.com/en-gb/102154) · [Outlook 资料路径与搜索](https://support.microsoft.com/en-us/outlook/troubleshoot-search-issues-in-outlook-for-mac) · [Outlook 仅本机文件夹](https://support.microsoft.com/en-us/outlook/about-on-my-computer-folders-in-outlook-for-mac) · [OneDrive 按需文件](https://support.microsoft.com/en-us/onedrive/save-disk-space-with-onedrive-files-on-demand-for-mac) · [Xcode 模拟器运行环境管理](https://developer.apple.com/documentation/safari-developer-tools/adding-additional-simulators)。
