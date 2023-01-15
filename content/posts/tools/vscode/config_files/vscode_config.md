---
title: "vscode配置文件(config.json)设置"
date: 2023-01-15T17:52:52+08:00
draft: false
description: "vscode配置文件(config.json)设置"
lightgallery: true
tags: [vsoced]
categories: [tools]
---


参考：


[Using C++ on Linux in VS Code](https://code.visualstudio.com/docs/cpp/config-linux)

[Getting Started with Python in VS Code](https://code.visualstudio.com/docs/python/python-tutorial)

[【笔记】vscode做C++开发，launch.json、tasks.json、settings.json写法示例](https://blog.csdn.net/qq_29935433/article/details/103690243)

[教你如何快速解决需要在每个新项目新建launch.json文件的问题？](https://www.codenong.com/cs109037302/)



# Tips and Tricks

`Ctrl+R` : Displays a Quick Pick dropdown with the list from **File** > **Open Recent** with recently opened folders and workspaces followed by files.



## Command line

```bash
# open code with current directory
code .

# open the current directory in the most recently used code window
code -r .

# create a new window
code -n

# change the language
code --locale=es

# open diff editor
code --diff <file1> <file2>

# open file at specific line and column <file:line[:character]>
code --goto package.json:10:5

# see help options
code --help

# disable all extensions
code --disable-extensions .

```

## .vscode folder

Workspace specific files are in a `.vscode` folder at the root. For example, `tasks.json` for the Task Runner and `launch.json` for the debugger.



## Ignore files / folders

Removes these files / folders from your editor window.

```json
"files.exclude": {
    "somefolder/": true,
    "somefile": true
}
```

Remove these files / folders from search results.

```json
"search.exclude": {
    "someFolder/": true,
    "somefile": true
}
```

# launch.json、tasks.json、settings.json





# settings.json

[User and Workspace Settings](https://code.visualstudio.com/docs/getstarted/settingss)



- **User Settings** - Settings that apply globally to any instance of VS Code you open.
- **Workspace Settings** - Settings stored inside your workspace and only apply when the workspace is opened.



## **User Settings**

### Settings file locations[#](https://code.visualstudio.com/docs/getstarted/settings#_settings-file-locations)

Depending on your platform, the user settings file is located here:

- **Windows** `%APPDATA%\Code\User\settings.json`
- **macOS** `$HOME/Library/Application\ Support/Code/User/settings.json`
- **Linux** `$HOME/.config/Code/User/settings.json`



## **Workspace Settings**





# launch.json

[Configure C/C++ debugging](https://code.visualstudio.com/docs/cpp/launch-json-reference)