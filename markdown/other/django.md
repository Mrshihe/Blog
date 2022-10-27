#### vscode配置Django开发环境
- ##### 安装python和django插件
- ##### 新建项目目录,运行终端新建虚拟环境
> python -m venv 'venv'
- ##### 选择Python解释器路径为虚拟环境
> 查看 -- 命令面板 选择 Python: Select Interpreter
- ##### 选择刚才自己创建的虚拟环境文件夹
- ##### 新建终端（前面带有（env）标识）
- ##### 新建终端，可能会遇见错误
```
~\venv\Scripts\Activate.ps1，因为在此系统上禁止运行脚本
管理员身份运行Powershell, 执行如下命令需改执行策略
Set-ExecutionPolicy -Scope CurrentUser RemoteSigned
```
- ##### 虚拟环境中安装django
- ##### django创建项目
### 执行python manage.py 命令需注意文件位置

#### vscode配置调试快捷方法
- ##### 点击调试按钮，选择创建launch.json文件
- ##### 选择python环境 调试配置选择django
- ##### 生成json文件，保存，即可完成快捷调试配置
```
{
  // 使用 IntelliSense 了解相关属性。 
  // 悬停以查看现有属性的描述。
  // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Python: Django",
      "type": "python",
      "request": "launch",
      "program": "${workspaceFolder}\\manage.py",
      "args": [
        "runserver",
        "http:127.0.0.1:8000"
      ],
      "django": true,
      "justMyCode": true
    }
  ]
}
```

#### vscode安装django插件后继续保持html语法提示
- ##### 打开设置，右上角"打开设置(json)"
```
"files.associations": {
  "**/*.html": "html",
  "**/templates/**/*.html": "django-html",
},
"emmet.includeLanguages": {
  "django-html": "html"
},
```

#### pip windows镜像设置
- ##### C:\Users\用户目录下，新建pip文件夹
- ##### pip文件夹下新建pip.ini,输入如下代码
```
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host = https://pypi.tuna.tsinghua.edu.cn
```