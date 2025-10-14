---
author: 'lanxinmob' 
postSlug: 'python项目管理'
title: 'Python 项目代码管理与发布流程'
featured: true
draft: false
tags:
  - '笔记'
  - 'Python'
description: '以及代码规范化操作'
pubDatetime: 2025-10-12T23:00:00Z
---

### Python 项目代码管理与发布流程

#### 安装项目管理工具

```
pip install uv uv_build twine
```

- **`uv`**: 一个用 Rust 编写的极快的 Python 包和项目管理器，一个工具即可替代 pip、pip-tools、pipx、poetry、pyenv、twine、virtualenv 等更多工具。提供全面的项目管理，并带有通用锁文件。

![Shows a bar chart with benchmark results.](https://github.com/astral-sh/uv/assets/1309177/03aa9163-1c79-4a87-a31d-7a9311ed9310#only-dark)

- **`uv_build`**: 一个 Python 项目构建工具。在当前目录中构建项目，并将构建产物放置在 `dist/` 子目录中。
- **`twine`**: 将 Python 包上传到 PyPI (Python Package Index) 或其他仓库的工具。它使用经过验证的连接通过 HTTPS 向 PyPI 验证身份确保了安全性。

#### 更新版本号

```
uv version --bump patch
```

- 这个命令会自动增加项目的“补丁版本号”。例如，如果当前版本是 `v0.1.0`，执行此命令后，它会自动更新为 `v0.1.1`。这通常会修改你项目中的 `pyproject.toml` 文件。

```
uv version 1.0.0
0.0.2 => 1.0.0
uv version --bump minor
1.2.3 => 1.3.0
```

- 也可以提供参数，指定更新版本号，或用 `--bump` 来包含语义。

#### 构建项目包

更新版本后，将你的项目代码打包成可分发的格式。

```
uv build
```

- **`uv build`**: 此命令会为你的项目创建两种标准的包格式：
  - **Wheel (`.whl`)**: 预编译的二进制包，安装速度快。
  - **Source Distribution (`.tar.gz`)**: 源代码包。

#### 发布到 PyPI

将构建好的包上传到 PyPI，让其他人可以通过 `pip` 或 `uv` 来安装你的库。

```
twine upload dist/* -u token -p pypi-AgEI
```

- **`twine upload dist/*`**: 命令 `twine` 上传 `dist/` 目录下的所有文件。
- **`-u token`**: 指定用户名为 `token`，这是使用 API 令牌上传时的标准做法。
- **`-p pypi-AgEI...`**: 提供你的 PyPI API 令牌。

#### 更新与同步依赖

发布完成后，更新项目的依赖锁文件并同步开发环境，以确保所有依赖都是最新的。

```
uv lock --upgrade
```

- **`uv lock --upgrade`**: 这个命令会检查你的 `pyproject.toml` 文件，并根据其中指定的版本范围，将所有依赖项更新到最新的兼容版本，然后将这些确切的版本号记录在 `uv.lock` 文件中。

```
uv sync
```

- **`uv sync`**: 此命令会根据 `uv.lock` 文件中的版本号，精确地安装所有依赖到你当前的虚拟环境中。这确保了团队中每个成员以及生产环境都使用完全一致的依赖版本。

#### 版本控制

为新发布的版本创建一个 Git 标签，并推送到远程仓库。

```
git tag v0.1.1
```

- **`git tag v0.1.1`**: 这会在你当前的 Git commit 上创建一个名为 `v0.1.1` 的标签。这个标签是一个重要的标记，代表了这个版本的源代码状态，方便未来追溯和检出。

```
git push origin --tags
```

- **`git push origin --tags`**: 将本地创建的所有 Git 标签（包括 `v0.1.1`）推送到名为 `origin` 的远程代码仓库。

#### 代码质量自动化（Ruff & Pre-commit）

运行逻辑是通过 `pre-commit` 的配置文件 (`.pre-commit-config.yaml`) 来告诉`precommit`在每次提交代码前，用 Ruff 检查修改过的文件，来保障项目代码质量和风格一致性，在项目初始化时设置一次即可。

```
uv tool install pre-commit
```

- **`uv tool install pre-commit`**: 使用 `uv` 的工具安装功能来安装 `pre-commit`。这样做可以将其安装在一个独立于项目依赖的环境中，避免了潜在的冲突。

```
pre-commit install
```

- **`pre-commit install`**: 这个命令会在你的本地 Git 仓库中安装 `pre-commit` 钩子（hooks）。
- 如果检查不通过（例如，代码格式不规范），`commit` 将会被中断，你需要根据提示修正代码后才能重新提交。
- Ruff 是用 Rust 编写的极快的 Python linter 和代码格式化程序，可用于替代 [Flake8](https://pypi.org/project/flake8/) 等数十个插件，且快上几十甚至数百倍。

![Shows a bar chart with benchmark results.](https://user-images.githubusercontent.com/1309177/232603514-c95e9b0f-6b31-43de-9a80-9e844173fd6a.svg#only-dark)

---

参考：

[uv](https://docs.astral.sh/uv/)  [uv build ](https://docs.astral.sh/uv/guides/package/) [twine](https://twine.readthedocs.io/en/stable/) [ruff](https://docs.astral.sh/ruff/)