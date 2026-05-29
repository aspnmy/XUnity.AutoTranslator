# XUnity Auto Translator

## 目录
 * [简介](#简介)
 * [插件框架](#插件框架)
 * [安装](#安装)
 * [快捷键映射](#快捷键映射)
 * [翻译器](#翻译器)
 * [文本框架](#文本框架)
 * [配置](#配置)
 * [IL2CPP 支持](#il2cpp-支持)
 * [常见问题](#常见问题)
 * [翻译 Mod](#翻译-mod)
 * [手动翻译](#手动翻译)
 * [关于再分发](#关于再分发)
 * [纹理翻译](#纹理翻译)
 * [集成到自动翻译器](#集成到自动翻译器)
 * [实现自定义翻译器](#实现自定义翻译器)
 * [实现资源重定向器](#实现资源重定向器)

## 简介
这是一个高级翻译插件，可用于自动翻译基于Unity的游戏，同时也提供了进行手动翻译所需的完整工具。

它会（显然）连接到互联网以实现自动翻译。如果你对此感到不适，请不要使用本插件。

如果你打算将此插件作为游戏翻译包的一部分进行再分发，请务必阅读[关于再分发](#关于再分发)和[手动翻译](#手动翻译)章节，以了解插件的实际工作原理。

## 插件框架
该模块可以独立安装，无需任何外部依赖；也可以作为以下插件管理器/模组加载器的插件使用：
- [BepInEx](https://github.com/bbepis/BepInEx) （推荐）
- [MelonLoader](https://melonwiki.xyz)
- [IPA](https://github.com/Eusth/IPA)
- UnityInjector

所有安装方法见下文。

## 安装
本插件可通过以下方式安装：

### 独立安装（ReiPatcher）
**要求**：无需其他工具，本下载已包含 ReiPatcher。

**重要提示：**使用此方法，只需点击两次即可让插件于大多数 Unity 游戏中正常工作。如果你已使用受支持的插件管理器，建议不要使用本安装方式，否则可能产生问题。

1. 阅读上面的 “重要提示”。
2. 从 [releases](../../releases) 下载 XUnity.AutoTranslator-ReiPatcher-{VERSION}.zip。
3. 直接解压到游戏目录，使 "SetupReiPatcherAndAutoTranslator.exe" 与其他 exe 文件同目录。
4. 运行 "SetupReiPatcherAndAutoTranslator.exe"，该操作将正确设置 ReiPatcher。
5. 执行在原有 exe 旁创建的快捷方式 {GameExeName} (Patch and Run).lnk，这会修补并启动游戏。
6. 后续可直接用 {GameExeName}.exe 启动游戏。
7. 为各种原因，并非所有文本钩子默认启用。如发现部分内容未被正确翻译，请进入配置文件，启用某些被禁用的文本框架。配置文件首次运行插件时自动生成。

**文件结构应如下：**
```
{GameDirectory}/ReiPatcher/Patches/XUnity.AutoTranslator.Patcher.dll
{GameDirectory}/ReiPatcher/ExIni.dll
{GameDirectory}/ReiPatcher/Mono.Cecil.dll
{GameDirectory}/ReiPatcher/Mono.Cecil.Inject.dll
{GameDirectory}/ReiPatcher/Mono.Cecil.Mdb.dll
{GameDirectory}/ReiPatcher/Mono.Cecil.Pdb.dll
{GameDirectory}/ReiPatcher/Mono.Cecil.Rocks.dll
{GameDirectory}/ReiPatcher/ReiPatcher.exe
{GameDirectory}/{GameExeName}_Data/Managed/ReiPatcher.exe
{GameDirectory}/{GameExeName}_Data/Managed/XUnity.Common.dll
{GameDirectory}/{GameExeName}_Data/Managed/XUnity.ResourceRedirector.dll
{GameDirectory}/{GameExeName}_Data/Managed/XUnity.AutoTranslator.Plugin.Core.dll
{GameDirectory}/{GameExeName}_Data/Managed/XUnity.AutoTranslator.Plugin.ExtProtocol.dll
{GameDirectory}/{GameExeName}_Data/Managed/MonoMod.RuntimeDetour.dll
{GameDirectory}/{GameExeName}_Data/Managed/MonoMod.Utils.dll
{GameDirectory}/{GameExeName}_Data/Managed/Mono.Cecil.dll
{GameDirectory}/{GameExeName}_Data/Managed/0Harmony.dll
{GameDirectory}/{GameExeName}_Data/Managed/ExIni.dll
{GameDirectory}/{GameExeName}_Data/Managed/Translators/{Translator}.dll
{GameDirectory}/AutoTranslator/Translation/AnyTranslationFile.txt（这些文件将由插件自动生成！）
```
**注意：** ReiPatcher 目录中的 `Mono.Cecil.dll` 和 Managed 目录中的不是同一个文件。

### BepInEx 插件
**要求**：[BepInEx 插件管理器](https://github.com/BepInEx/BepInEx)（请依其安装说明安装！）

1. 从 [releases](../../releases) 下载 XUnity.AutoTranslator-BepInEx-{VERSION}.zip。
2. 直接解压到游戏目录，使插件dll自动放入 BepInEx 文件夹中。
3. 启动游戏。
4. 并非所有文本钩子默认启用，如发现没有正确翻译，请进入配置文件启用被禁用的文本框架。配置文件会在首次启动插件时自动生成。

**文件结构应如下：**
```
{GameDirectory}/BepInEx/core/XUnity.Common.dll
{GameDirectory}/BepInEx/plugins/XUnity.ResourceRedirector/XUnity.ResourceRedirector.dll
{GameDirectory}/BepInEx/plugins/XUnity.ResourceRedirector/XUnity.ResourceRedirector.BepInEx.dll
{GameDirectory}/BepInEx/plugins/XUnity.AutoTranslator/XUnity.AutoTranslator.Plugin.Core.dll
{GameDirectory}/BepInEx/plugins/XUnity.AutoTranslator/XUnity.AutoTranslator.Plugin.BepInEx.dll
{GameDirectory}/BepInEx/plugins/XUnity.AutoTranslator/XUnity.AutoTranslator.Plugin.ExtProtocol.dll
{GameDirectory}/BepInEx/plugins/XUnity.AutoTranslator/ExIni.dll
{GameDirectory}/BepInEx/plugins/XUnity.AutoTranslator/Translators/{Translator}.dll
{GameDirectory}/BepInEx/core/MonoMod.RuntimeDetour.dll
{GameDirectory}/BepInEx/core/MonoMod.Utils.dll
{GameDirectory}/BepInEx/core/Mono.Cecil.dll
{GameDirectory}/BepInEx/Translation/AnyTranslationFile.txt（这些文件将由插件自动生成！）
```
#### BepInEx IL2CPP 插件
IL2CPP 的安装方法与标准版一致，只是需安装 BepInEx 6 for IL2CPP 版本（仅能从 [此处](https://builds.bepis.io/projects/bepinex_be) 获取最新开发构建）。
当前版本（5.4.0）在开发构建704下编译。

### MelonLoader 插件
**要求**：[Melon Loader](https://melonwiki.xyz)（请先按它的安装说明进行安装！）

1. 从 [releases](../../releases) 下载 XUnity.AutoTranslator-MelonMod-{VERSION}.zip。
2. 直接解压到游戏目录，使插件dll文件放在 Mods 和 UserLibs 文件夹。
3. 启动游戏。
4. 同上，因为各种原因，并非所有文本钩子默认启用，如未正确翻译，请手动配置。

**文件结构应如下：**
```
{GameDirectory}/Mods/XUnity.AutoTranslator.Plugin.MelonMod.dll
{GameDirectory}/UserLibs/XUnity.Common.dll
{GameDirectory}/UserLibs/XUnity.ResourceRedirector.dll
{GameDirectory}/UserLibs/XUnity.AutoTranslator.Plugin.Core.dll
{GameDirectory}/UserLibs/XUnity.AutoTranslator.Plugin.ExtProtocol.dll
{GameDirectory}/UserLibs/ExIni.dll
{GameDirectory}/UserLibs/Translators/{Translator}.dll
{GameDirectory}/AutoTranslator/Translation/AnyTranslationFile.txt（这些文件将由插件自动生成！）
```
当前版本（5.4.0）基于 v0.6.1 Open-Beta 构建。

#### MelonLoader IL2CPP 插件
IL2CPP 的安装方式与标准版相同，只需使用 `MelonMod-IL2CPP` 包代替。

### IPA 插件
**要求**：[IPA 插件管理器](https://github.com/Eusth/IPA)（请先依其安装说明进行安装）

1. 从 [releases](../../releases) 下载 XUnity.AutoTranslator-IPA-{VERSION}.zip。
2. 直接解压到游戏目录，使插件dll自动放入 Plugins 文件夹。
3. 启动游戏。
4. 配置和文本钩子的注意事项同上。

**文件结构应如下：**
```
{GameDirectory}/Plugins/XUnity.Common.dll
{GameDirectory}/Plugins/XUnity.ResourceRedirector.dll
{GameDirectory}/Plugins/XUnity.AutoTranslator.Plugin.Core.dll
{GameDirectory}/Plugins/XUnity.AutoTranslator.Plugin.IPA.dll
{GameDirectory}/Plugins/XUnity.AutoTranslator.Plugin.ExtProtocol.dll
{GameDirectory}/Plugins/MonoMod.RuntimeDetour.dll
{GameDirectory}/Plugins/MonoMod.Utils.dll
{GameDirectory}/Plugins/Mono.Cecil.dll
{GameDirectory}/Plugins/0Harmony.dll
{GameDirectory}/Plugins/ExIni.dll
{GameDirectory}/Plugins/Translators/{Translator}.dll
{GameDirectory}/Plugins/Translation/AnyTranslationFile.txt（这些文件将由插件自动生成！）
```

### UnityInjector 插件
**要求**：UnityInjector（先参照其安装说明进行安装）

1. 从 [releases](../../releases) 下载 XUnity.AutoTranslator-UnityInjector-{VERSION}.zip。
2. 直接解压到游戏目录，使插件dll放在 UnityInjector 文件夹下（**注意：不一定是游戏根目录！**）
3. 启动游戏。
4. 配置和使用同前述内容。

**文件结构如下**：
```
{GameDirectory}/UnityInjector/XUnity.Common.dll
{GameDirectory}/UnityInjector/XUnity.ResourceRedirector.dll
{GameDirectory}/UnityInjector/XUnity.AutoTranslator.Plugin.Core.dll
{GameDirectory}/UnityInjector/XUnity.AutoTranslator.Plugin.UnityInjector.dll
{GameDirectory}/UnityInjector/XUnity.AutoTranslator.Plugin.ExtProtocol.dll
{GameDirectory}/UnityInjector/0Harmony.dll
{GameDirectory}/UnityInjector/Translators/{Translator}.dll
{GameDirectory}/UnityInjector/Config/Translation/AnyTranslationFile.txt（这些文件将由插件自动生成！）
```
**注意:** 以此方式安装时，不支持 MonoMod 钩子，因为 Sybaris 使用的 `Mono.Cecil.dll` 版本较旧。

---

## 快捷键映射
下列按键功能已分配：

- **ALT + 0**：切换 XUnity AutoTranslator 的用户界面（注意是数字0，不是字母O）
- **ALT + 1**：切换 Translation Aggregator UI
- **ALT + T**：在所有由本插件提供的文本中，切换已翻译和未翻译版本
- **ALT + R**：重新加载翻译文件。如果你在游戏运行时修改了文本和纹理文件，这个功能很有用（但不能保证对所有纹理都生效）
- **ALT + U**：手动钩取。默认钩子不会总是捕捉文本，此操作会尝试手动查找文本。但不会钩取未启用的文本框架里的文本组件
- **ALT + F**：当配置了 OverrideFont 时，切换自定义字体和默认字体
- **ALT + Q**：如果插件因连续翻译端点出错而被关闭，可重启插件。仅当因错误被关闭时有效

**仅用于调试的快捷键：**
- **CTRL + ALT + NP9**: 模拟同步错误
- **CTRL + ALT + NP8**: 模拟异步延迟1秒的错误
- **CTRL + ALT + NP7**: 将已加载的场景名和ID输出到控制台
- **CTRL + ALT + NP6**: 将完整的 GameObject 层级结构输出到文件 `hierarchy.txt`

---

## 翻译器
插件通过“翻译端点”获得翻译结果，本质上每个端点就是一个插件，放在 `Translators` 目录下。

### 内置支持

以下为内置支持的翻译器/端点：
- **GoogleTranslate** (网页 Google 翻译)
- **GoogleTranslateV2** (新版 Google，正在测试中)
- **GoogleTranslateCompat** (某些Unity版本需求)
- **GoogleTranslateLegitimate** (Google Cloud，需API Key，一年/300美元，1.5千万字符)
- **BingTranslate** (网页 Bing 翻译)
- **BingTranslateLegitimate** (Azure 机器翻译)
- **DeepLTranslate** (网页 DeepL)
- **DeepLTranslateLegitimate** (DeepL官方API，需要API Key)
- **PapagoTranslate** (Naver Papago，可用日韩中)
- **BaiduTranslate** (百度，需要AppId和AppSecret，首5万字免费)
- **YandexTranslate** (Yandex，需API Key)
- **WatsonTranslate** (IBM Watson，需API Key)
- **LecPowerTranslator15**（需安装正版LEC软件）
- **ezTrans XP**（韩日互译，需安装EzTrans XP及Ehnd）
- **LingoCloudTranslate**（彩云小译，注册认证后前100万字/月免费）
- **CustomTranslate**（自定义 HTTP GET 请求，需开发能力，见后面 FAQ）

> 注意：无需认证的免费在线翻译极易失效/被ban，不建议生产环境依赖。

### 第三方扩展

用户可根据 [开发文档](#实现自定义翻译器) 开发自定义翻译器 DLL，已知可用第三方插件如 SugoiOfflineTranslatorEndpoint（离线Sugoi服务），LlmTranslators（OpenAI/OLLAMA本地大模型）等。

> 使用第三方插件需自行承担风险，安全/兼容性无法官方保障。

### 关于账号安全

如需使用需Key/Secret的服务，切勿泄露密钥，泄露后应立即吊销。付费前请确认目标语言对你的目的是否支持。

### 防Spam（滥用保护）

1. 新文本出现后等待1秒才发翻译请求，防止高频变化被发送
2. 单局游戏不超过8000次请求（每次字符数配置，默认200）
3. 任意时刻仅允许1个请求并发
4. 队列翻译数超4000条或5次无回复等，自动关闭插件
5. 检测到“每帧排队文本/滚动文本”自动关闭
6. 缓存全部翻译（内存+磁盘），不重复请求
7. 尝试自动批量请求、用内置短语库、合并并发等优化行为

---

## 文本框架

支持的Unity文本系统如下：
- UGUI
- NGUI
- IMGUI（默认关闭）
- TextMeshPro
- TextMesh（默认关闭，常用于3D文本）
- FairyGUI（常用于中国某些二次元游戏）
- Utage（常用ADV游戏引擎）

如遇某些文本无法翻译，可在配置文件中根据具体 UI 框架启用对应的字段。

---

## 配置

默认配置文件长这样（仅罗列核心示例，详细文档建议查原文及代码注释）：

```ini
[Service]
Endpoint=GoogleTranslate
FallbackEndpoint=

[General]
Language=en
FromLanguage=ja

[Files]
Directory=Translation\{Lang}\Text
OutputFile=Translation\{Lang}\Text\_AutoGeneratedTranslations.txt

[TextFrameworks]
EnableUGUI=True
EnableNGUI=True
EnableIMGUI=False
...

[Behaviour]
MaxCharactersPerTranslation=200
IgnoreWhitespaceInDialogue=True
EnableBatching=True
UseStaticTranslations=True
...

[Texture]
TextureDirectory=Translation\{Lang}\Texture
EnableTextureTranslation=False
EnableTextureDumping=False
CacheTexturesInMemory=True
...
```

### 配置参数说明

- `[Service] Endpoint`：使用的翻译端点，如 GoogleTranslate/Baidu 等
- `[General] Language`：目标语言，常见如 en、zh
- `[Files] Directory`：缓存/手动翻译文本目录
- `[TextFrameworks] Enable...`：按需启用文本系统钩子
- `[Behaviour]` 下的各参数允许细致限制自动翻译、UI调整、日志等行为
- `[Texture]` 可控制是否翻译图片及关联策略

详细参数及作用见原文。
