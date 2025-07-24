---
author: 'Lanxinmob'
title: '暑期日记16'
postSlug: 'summer-diary-16'
featured: false
draft: false
tags:
  - '笔记'
  - '脚本'
ogImage: ''
description: 'paper link formatter'
pubDatetime: 2025-07-22T09:00:00Z
toc: true
---
### 脚本语言
#### Batch Script

- `Windows` 古老的批处理脚本(`.bat`)语言，通过`cmd.exe`解释器运行。

- `@echo off`：关闭命令回显，让脚本运行界面更干净。

- `for /f ["参数"] %%变量 in (数据源/命令) do (处理逻辑)`执行命令，逐行处理输出

  ```bat
  for /f "delims=" %%p in ('where python') do (
    set "PYTHON_EXE_PATH=%%p"
  goto :PythonFound
  )
  rem delims= 取消所有分隔符，将整行作为一个完整字段
  ```
    ```bat
    rem 提取第2个字段和剩余内容
    for /f "delims=, tokens=2*" %%a in ("a,b,c,d") do echo %%a %%b  # 输出：b c,d
    rem 虽然没有直接指定b，当tokens参数指定多个字段时，变量会按字母表顺序自动递增
    ```

- `pause`：批处理中用于暂停脚本执行，显示提示信息 “请按任意键继续. . .”

- `>nul 2>&1`把标准输出重定向到空设备，把错误输出重定向到标准输出。

- `powershell -Command " "`执行powershell命令

- `cd /d "%~dp0"`切换到脚本自身所在路径，`/d` 表示可以跨驱动器切换，`%~dp0` 是批处理变量，自动获取当前脚本的完整路径（`d` 代表驱动器，`p` 代表路径，`0` 指代当前脚本）。

- `if %ERRORLEVEL% equ 0 ()else()` 用于判断上一条命令的执行结果是否成功，上一条命令的退出码为0说明执行成功，非0说明失败。

- `set /p REPO_PATH=<"%TEMP_FILE%"`从输入读取第一行内容并将其赋值给变量

- ```bat
    echo @echo off > "%PROJECT_DIR%\run_watcher.bat"
    echo rem --- Auto-generated script, do not modify manually --- >>         "%PROJECT_DIR%\run_watcher.bat"
    echo echo --- Run at %%date%% %%time%% --- ^>^> "%PROJECT_DIR%\watcher_log.txt" >> "%PROJECT_DIR%\run_watcher.bat"
    
  rem 生成run_watcher.bat批处理文件：并写入@echo off
  rem 追加输出一行注释,>是创建文件
  rem >>是追加输出，^是转义符，确保>被当作文本写入而非重定向
  ```

#### PowerShell

- `PowerShell` 脚本(`.ps1`)，`Windows` 的现代化命令行外壳和脚本语言，基于`.NET`框架，可调用丰富的系统功能。
- ```powershell
   param (
    [string]$Title,
    [string]$OutputFile
  )
  ```
  传入两个字符串变量的参数
- `Add-Type -AssemblyName System.Windows.Forms` 加载 .NET 框架中的 `System.Windows.Forms` 程序集，该程序集包含了创建 Windows 图形界面的类和功能，加载后脚本即可调用这些界面组件。
- `$f = New-Object System.Windows.Forms.FolderBrowserDialog`：创建一个文件夹选择对话框对象，赋值给变量`$f`，用于让用户图形化选择文件夹。
- `$f.Description = $Title`：设置对话框的描述文本（显示在对话框顶部）。
- ```powershell
   if ($f.ShowDialog() -eq 'OK') {
     Set-Content -Path $OutputFile -Value $f.SelectedPath -Encoding OEM
   }
   ```
   用户点击OK后将用户选中的文件夹路径以 OEM 编码写入指定的文件中。

### Python核心库

#### `git`

- 像`git` 命令一样，直接操作 Git 仓库。
- `repo.index.diff(None)`找出所有已修改但未暂存 (`git add`) 的文件，并返回一个列表。

#### `re`

- 文本模式匹配工具
- `URL_REGEX = r'https?://[^\s()]+'` 定义了一个正则表达式：
  - `https?://` 匹配`http://`或`https://`
  - `[^\s()]+` 匹配空白字符（空格、换行等）和括号以外的字符，确保 URL 完整。
- `urls = re.findall(URL_REGEX, content)` 用上述正则在`content`文本中查找所有符合规则的 URL，结果以列表形式存到`urls`中。

#### `os`

- `os.path.join(dir, filename)` 拼接路径
- `os.path.dirname(filepath)` 获取文件所在的目录
- `os.path.basename(filepath)` 获取文件名。
- `os.path.exists(path)` 检查一个文件或文件夹是否存在
- `os.makedirs(dir_path)` 创建一个文件夹（如果父目录不存在也会一并创建）

- `os.listdir(dir_path) `列出目录下的所有文件

- `os.remove(filepath)` 删除文件 
- `os.getcwd()` 获取当前工作目录

#### `requests`

- `http` 请求库

- `response = requests.get(url, timeout=15)`：向指定`url`发送请求，设置超时时间 15 秒（超过则抛出异常），返回的响应对象赋值给`response`。

- `response.raise_for_status()`：检查响应状态码，2xx不用做任何操作，若为 4xx（客户端错误）或 5xx（服务器错误），则抛出 HTTP 错误异常。

- `response`对象包含响应状态码（`response.status_code`），响应内容（`response.text` (字符串形式)或 `response.content`(二进制形式)），响应头（`response.headers`）

- ```python
  headers = {'User-Agent': 'MyNotesProcessor/1.0'} 
  fields = 'title,authors,citationCount,url' 
  api_url = f'https://api.semanticscholar.org/graph/v1/paper/arXiv:{arxiv_id}?fields={fields}'
  ```
  
- `headers` 定义请求头，`User-Agent`字段标识请求为我设置的程序名称和版本号`MyNotesProcessor/1.0`，模拟客户端身份，避免被服务器识别为异常请求。

- `fields` 指定需要从 API 获取的论文信息字段（标题、作者、引用量、链接），减少冗余数据返回。

- `data = response.json()`：将 HTTP 响应的 JSON 格式内容解析为 Python 字典（`data`），方便后续提取字段。

- `title = data.get('title', 'N/A')`：从`data`中获取`title`字段值，若不存在则用`'N/A'`（未获取到）替代。

- `citations = data.get('citationCount', 0)`：获取引用量`citationCount`，默认值为 0。

- `url = data.get('url', '')`：获取论文链接`url`，默认值为空字符串。

#### `watchdog`

- ```python
   event_handler = MarkdownEventHandler()#创建实例
   observer = Observer()#创建实例
   observer.schedule(event_handler, repo_path, recursive=True)
   #监听到 repo_path 路径下的文件或目录发生变化时，会触发 event_handler 处理事件
  #recursive=True 表示递归监控该路径下的所有子目录及文件。
  observer.start()#开始监听
  ```

#### `argparse`
- ```python
   parser = argparse.ArgumentParser(description="持续监控指定目录中的 Markdown 文件变化，并自动处理。")#创建实例
   parser.add_argument('--repo', dest='repo_path', required=True, help="需要监控的 Git 仓库（Markdown 笔记）的路径。")#定义需要传入的参数
   args = parser.parse_args()#解析传入参数
    
   repo_path = args.repo_path#获取对应对应参数
  ```

