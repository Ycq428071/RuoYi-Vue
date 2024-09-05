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

### Eslint

- vscode 插件: 下载 **ESLint**;
- 项目依赖: `npm init @eslint/config@latest`;
- 命令: 配置查找 src 目录下 eslint 错误的指令;
- 规则: 在 `eslint.config.js` 文件中配置;
- 冲突: 在 `tsconfig.json` 配置文件和 eslint 及其不同的插件中存在多个作用重复的规则, 同时启用两个作用重复的规则会导致一处问题代码出现两次提示, 如, 设置 `'@typescript-eslint/no-unused-vars': 'error', 'no-unused-vars': 'warn'`, 会同时提示 `error (@typescript-eslint/no-unused-vars); warning (no-unused-vars)`, 应避免启用作用重复的规则; 在覆盖 eslint 配置文件中其他插件默认的规则时, 应注意需覆盖规则的名称, 如, 想覆盖 `no-unused-vars` 规则, 却配置了 `@typescript-eslint/no-unused-vars` 规则, 就会导致上述的规则冲突;
- 踩坑:
  - 由于使用的是最新的 eslint v9 版本, 配置文件相关使用方式变动较大, 在初始化 eslint 的依赖和配置后, 发现自己配置的规则通过命令行能够生效, 但是在编辑器中并未正确显示规则对应的提示信息; 后在 vscode 的配置文件中发现将 extensions 属性删除后便恢复正常, 经过查询了解 `eslint.options` 下并不支持 extensions 属性, 所以导致插件读取配置出错;
  - 直接设置 `eslint.useFlatConfig` 为 true 时, vscode 能够正确读取使用最新版本 eslint 项目的配置文件展示提示信息, 但在使用旧版本 eslint 项目中出错; 正确做法还是不要显式的设置 `eslint.useFlatConfig` 这个属性, eslint 插件会读取项目依赖中 eslint 的版本自动判断是否开启.
  ```settings.json
  "eslint.options": {
    "extensions": [".js", ".vue", ".jsx", ".tsx"] // 未了解含义, 直接从其他地方复制来的, 该层级下出现其他任意不支持的属性时, 都会导致上述错误; 与 ESLint CLI 有关, 可能是旧版本属性或配合其他插件使用的, 目前可以使用 "eslint.validate": [] 替代.
  },
  "eslint.useFlatConfig": true, // 启用对平面配置文件（如 eslint.config.js）的支持
  ```
