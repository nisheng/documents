## git规范

### git地址

> https://rdc.aliyun.com/

### git组介绍

1. baitai-game

   > 百泰游戏

2. laitui-web

   > 除游戏外的所有线上项目

3. titan-plugin

   > 发布系统插件

4. baitai-backups

   > 删除的分支用于放在这里备份

所有项目按严格规定创建仓库，只有需要的人才开放对应的组。

### 分支规范

1. master为主干分支，禁止手工提交。
2. 其他分支名称按照`Version+版本号`命名，如（Version0.0.1）。
3. 新的分支名称版本号必须大于之前的版本号。

### 项目规范

1. 每个项目必须有自己的简介。
2. 每个项目必须有README.md文件。
3. 每个项目必须有.gitignore文件。
4. 切记node_modules禁止提交到git。

### sshKeys

参考地址：http://www.jianshu.com/p/31cbbbc5f9fa/

将id_rsa.pub内容复制到https://code.aliyun.com/ > Profile > sshKeys





