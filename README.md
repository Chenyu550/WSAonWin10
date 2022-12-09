# Windows 10 的 WSA 补丁

这是一个 WSA 补丁，可以让 WSA（Android 的 Windows 子系统）在 Windows 10 上运行。

我已经在 `Windows 10 22H2 10.0.19045.2311 x64` 和 `WSA 2210.40000.7.0` 上进行了测试。

### 注意

这不是我的作品，这是我从`别人的`[存储库](https://github.com/cinit/WSAPatch)里克隆过来的，来以防万一

### 指示

1. 获取 WSA appx zip。 您可以按照 [这里](https://github.com/LSPosed/MagiskOnWSALocal) 中的说明执行此操作
    （您需要使用本地 WSL2 自己“构建”它）。
2. 从 Windows 11 22H2 获取“icu.dll”。 请注意，您必须使用 Windows 11 中的 icu.dll。
    Windows 10 中的 icu.dll 将无法运行。
    （我在 `original.dll.win11.22h2` 目录中制作了这些 DLL 的副本。它们由 Microsoft 进行了数字签名。）
3. 使用此 repo 中的源代码构建 WsaPatch.dll。
    （使用 MSVC 工具链构建，而不是 MinGW 或其他工具。）
4. 补丁icu.dll：添加WsaPatch.dll作为icu.dll的导入DLL。
5. 将打补丁的icu.dll 和WsaPatch.dll 复制到WsaClient 目录。
6. 修补 AppxManifest.xml：找到 `TargetDeviceFamily` 节点并将 `MinVersion` 属性更改为您的 Windows版本。
    <details>

    在 AppxManifest.xml 中找到以下行。
    ```xml
    <TargetDeviceFamily Name="Windows.Desktop" MinVersion="10.0.22000.120" MaxVersionTested="10.0.22000.120"/>
    ```

    将 `MinVersion` 从 `10.0.22000.120` 更改为您的 Windows 版本，例如 `10.0.19045.2311`。
    </details>
7. 补丁AppxManifest.xml：删除AppxManifest.xml中所有关于“customInstall”扩展的节点。
    <details>
    从 AppxManifest.xml 中删除以下内容。

    ```xml
    <rescap:Capability Name="customInstallActions"/>
    ```

    ```xml
    <desktop6:Extension Category="windows.customInstall">
        <desktop6:CustomInstall Folder="CustomInstall" desktop8:RunAsUser="true">
            <desktop6:RepairActions>
                <desktop6:RepairAction File="WsaSetup.exe" Name="Repair" Arguments="repair"/>
            </desktop6:RepairActions>
            <desktop6:UninstallActions>
                <desktop6:UninstallAction File="WsaSetup.exe" Name="Uninstall" Arguments="uninstall"/>
            </desktop6:UninstallActions>
        </desktop6:CustomInstall>
   </desktop6:Extension>
    ```

    </details>
8. 运行“Run.bat”来注册您的 WSA appx。
9. 您现在应该可以运行 WSA。

如果您不想自己构建 WsaPatch.dll 和修补 icu.dll，
您可以从 [发布页](https://github.com/cinit/WSAPatch/releases) 下载预构建的二进制文件。
（它们被标记为“预发布”，因为我不知道它们是否足够稳定。）

### 我遇到的问题

1. 当使用WSA 2209.40000.26.0时，我能够在WSA中运行应用程序，
    但是在启用开发人员模式后我无法连接到 WSA ADB，
    因为 netstat 显示没有进程正在侦听端口 58526。
    升级到 WSA 2210.40000.7.0 后，我能够连接到 WSA ADB。
2. WSA 设置窗口不可拖动。
    移动 WSA 设置窗口的临时解决方案是按 Alt+Space，然后单击上下文菜单中的“移动”。 [#1](https://github.com/cinit/WSAPatch/issues/1)
3. 如果您的 WSA 在启动时崩溃（或突然消失），请尝试将您的 Windows 升级到 Windows 10 22H2 10.0.19045.2311。
    （有人反映WSA在22H2 19045.2251启动失败，但升级到19045.2311后正常。）


### Screenshot

![screenshot](./pic/screenshot_20221202.png)
