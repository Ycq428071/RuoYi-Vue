# ruoyi-naive-ui 项目记录

## node 版本管理

一直使用的是 nvm, 创建该项目时想用其他版本发现下载 node 超时, 直接通过浏览器访问 nvm 配置中的下载地址却是正常的; 因为此前有了解过其他的 node 版本管理工具, 所以这次直接进行尝试; 更换为 volta 发现同样下载超时, 而使用 fnm 则能正常下载, 对比后发现 volta 和 nvm 中配置的 node 下载地址与 fnm 的有所区别, 推测可能是 node 官网对下载地址有所变动, 更新 nvm 和 volta 的 node 下载地址后应该就能解决问题.

### fnm 配置

- 环境变量:

  - 添加 `FNM_NODE_DIST_MIRRORs` 和 `FNM_DIR` 两个变量, 值都为 node 安装路径 `D:\fnm\node`;
  - 在 Path 中添加 fnm 的安装路径 `D:\fnm`;

- 终端: 配置后使 node 能够在对应终端中运行;

  - PowerShell: 通过 `notepad $profile` 指令创建文件, 内容为 `fnm env --use-on-cd --shell power-shell | Out-String | Invoke-Expression`;
  - WinCMD: 在 fnm 的安装路径下创建 `fnm_init.cmd`, 内容如下; 通过 win + R 输入 `regedit` 打开注册表, 在 `计算机\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Command Processor` 下新建字符串, 名称为 `AutoRun`, 值为 `D:\fnm\fnm_init.cmd`.

  ```fnm_init.cmd
  @echo off
  :: for /F will launch a new instance of cmd so we create a guard to prevent an infnite loop
  if not defined FNM_AUTORUN_GUARD (
      set "FNM_AUTORUN_GUARD=AutorunGuard"
      FOR /f "tokens=*" %%z IN ('fnm env --use-on-cd') DO CALL %%z
  )
  ```

### 工作区

fnm 支持通过在项目根目录下创建 `.nvmrc` 和 `.node-version` 文件写入版本号 `22.7.0` 来设置工作区, 即打开终端时, fnm 会读取文件中记录的 node 版本自动切换到对应的版本, 且不影响 fnm 默认的版本.

## 代码规范

在 Eslint 最新版本中, 有关代码风格的规则 (如: 句末分号 `semi`) 已被弃用, 所以为了遵循官方方针, 语法检测使用 Eslint, 代码风格使用 Prettier;

### Prettier

- vscode 插件: 下载 **Prettier - Code formatter**, 并将其设置为默认格式化工具: `"editor.defaultFormatter": "esbenp.prettier-vscode"`;
- 项目依赖: `npm install --save-dev --save-exact prettier`;
- 规则: 在 `prettier.config.js` 文件中配置;
- 缺陷: 配置了 `singleAttributePerLine` 为 `false`, 但是当一行的字符数超过 `printWidth` 值时, html 标签中的属性还是会独占一行.
