# Sports Scorer 环境配置速查

## ✅ 已就绪（2026-06-21 最新状态）

| 工具 | 位置 | 版本 | 状态 |
|------|------|------|------|
| Node.js | `C:\Program Files\nodejs\` | v24.16.0 | ✅ |
| npm | `C:\Program Files\nodejs\` | 11.13.0 | ✅ |
| Claude Code | 全局 npm 包 | 2.1.185 | ✅ |
| JDK 17 | `C:\claude-code-learning\tools\jdk-17\jdk-17.0.2\` | 17.0.2+8 | ✅ |
| Android cmdline-tools | `C:\claude-code-learning\android-sdk\cmdline-tools\latest\` | latest | ✅ |
| JAVA_HOME | 用户环境变量 | `C:\claude-code-learning\tools\jdk-17\jdk-17.0.2` | ✅ |
| ANDROID_HOME | 用户环境变量 | `C:\claude-code-learning\android-sdk` | ✅ |
| PATH | 用户环境变量 | 已添加 JDK、SDK tools 路径 | ✅ |

## 验证环境

重新打开终端后运行：

```cmd
java -version
javac -version
sdkmanager --list
```

## 环境变量配置记录

```powershell
[Environment]::SetEnvironmentVariable('JAVA_HOME', 'C:\claude-code-learning\tools\jdk-17\jdk-17.0.2', 'User')
[Environment]::SetEnvironmentVariable('ANDROID_HOME', 'C:\claude-code-learning\android-sdk', 'User')
```

PATH 已追加：`%JAVA_HOME%\bin`、`%ANDROID_HOME%\platform-tools`、`%ANDROID_HOME%\cmdline-tools\latest\bin`

> ⚠️ 环境变量需要重新登录或重启终端生效
