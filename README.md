# github-hosts-bat
✨ Bat脚本: Windows更新Hosts访问GitHub

## 📚 参考库
-   [GitHub520](https://github.com/521xueweihan/GitHub520)

## 🚀 使用方式
-   右键管理员运行此脚本。

## 📝 Bat脚本代码

```bat
@echo off
setlocal

:: =================================================================
:: 脚本功能:
:: 1. 从指定的 URL 下载最新的 hosts 文件内容。
:: 2. 备份当前的系统 hosts 文件。
:: 3. 将下载的内容覆盖到系统 hosts 文件。
:: 4. 刷新 DNS 缓存。
:: 5. 过程中打印操作步骤，完成后保持窗口开启。
::
:: 重要提示: 请右键单击此脚本，并选择 "以管理员身份运行"。
:: =================================================================

title 更新 Hosts 文件并刷新 DNS

:: 定义变量
set "HOSTS_URL=https://hk.gh-proxy.com/https://raw.githubusercontent.com/521xueweihan/GitHub520/main/hosts"
set "SYSTEM_HOSTS_PATH=%SystemRoot%\System32\drivers\etc\hosts"
set "TEMP_HOSTS_FILE=%TEMP%\hosts_downloaded.tmp"
set "BACKUP_HOSTS_FILE=%SystemRoot%\System32\drivers\etc\hosts.backup"

:: 检查管理员权限
echo.
echo 正在检查管理员权限...
net session >nul 2>&1
if %errorLevel% neq 0 (
    echo.
    echo 错误: 请以管理员身份运行此脚本。
    echo 按任意键退出...
    pause >nul
    exit /b
)
echo 权限检查通过。

:DOWNLOAD
echo.
echo -----------------------------------------------------------------
echo 步骤 1: 正在从 GitHub 下载最新的 hosts 文件...
echo URL: %HOSTS_URL%
echo.
curl -o "%TEMP_HOSTS_FILE%" "%HOSTS_URL%"
if %errorLevel% neq 0 (
    echo.
    echo 错误: 下载失败。请检查您的网络连接或 URL 是否正确。
    goto END
)
echo 下载成功，临时文件已保存到: %TEMP_HOSTS_FILE%

:BACKUP
echo.
echo -----------------------------------------------------------------
echo 步骤 2: 正在备份当前的 hosts 文件...
echo 备份文件将保存为: %BACKUP_HOSTS_FILE%
echo.
copy "%SYSTEM_HOSTS_PATH%" "%BACKUP_HOSTS_FILE%" /Y >nul
if %errorLevel% neq 0 (
    echo.
    echo 错误: 备份 hosts 文件失败。请检查文件权限。
    goto END
)
echo 备份成功。

:OVERWRITE
echo.
echo -----------------------------------------------------------------
echo 步骤 3: 正在将新内容覆盖到系统 hosts 文件...
echo.
copy "%TEMP_HOSTS_FILE%" "%SYSTEM_HOSTS_PATH%" /Y >nul
if %errorLevel% neq 0 (
    echo.
    echo 错误: 覆盖 hosts 文件失败。请确保脚本以管理员身份运行。
    goto END
)
echo Hosts 文件更新成功。

:FLUSHDNS
echo.
echo -----------------------------------------------------------------
echo 步骤 4: 正在刷新 DNS 缓存...
echo.
ipconfig /flushdns
echo DNS 缓存已刷新。

:CLEANUP
echo.
echo -----------------------------------------------------------------
echo 正在清理临时文件...
del "%TEMP_HOSTS_FILE%" >nul 2>&1
echo 清理完成。

:END
echo.
echo =================================================================
echo 所有操作已完成。
echo.

:: 保持命令提示符窗口打开
cmd /k
```
