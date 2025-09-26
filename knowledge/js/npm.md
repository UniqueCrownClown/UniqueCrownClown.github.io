---
title: npm
date: 2018-06-21 11:23:51
categories: js #文章分类
tags: [npm] #文章标签，可以一次添加多个标签
---

### npm 常用模块

### npm scripts

向 npm 脚本传入参数，要使用--标明。

```bash
npm run lint --  --reporter checkstyle > checkstyle.xml
```

#### 登录

```bash
# 登录npm源包账号
npm adduser

# 替换淘宝的镜像
npm config set registry https://registry.npmjs.org/
npm config set registry https://registry.npm.taobao.org/

# 只有在源 1 下才起作用会上传到 npm 官网的个人仓库
npm publish

npm publish --otp=852769
```

#### 打包文件上传

npm pack

#### npm 换源

```shell
# 全局安装(npm 源方便切换的模块)
nrm
nrm ls
nrm current
# 切换源
nrm use +name
# 添加私有源
nrm add qihoo http://registry.npm.360.org
# 测速
nrm test npm
# 删除私有源
nrm del qihoo

```

#### 包版本检测

```shell
# 全局安装 npm-check
npm-check -u
```

### lerna 使用

```shell
# lerna使用

# 初始化项目
lerna init
# lerna.json + packages

# 安装全部依赖
lerna bootstrap

# 安装子包依赖,不加scope所有的子包都会安装
lerna add package --scope=packagesName

# 子包相互依赖,一样用上面的bash,会用symlink 的方式关联过去
lerna add package --scope=packagesName

#变更提示
lerna updated

# 发包流程

# 登录

# commit，保持版本树干净

# 版本发布
lerna publish

# 执行lerna publish发生了什么
# 会打tag,上传git,上传npm
```

### lerna publish 常见问题

坑 1：分支 3 的情况，因为开发者自己打的一些标签会影响 lerna 查找变更，可能会造成一些变更的包没有发布
解决办法：

1. 尽量避免自己打 Tag
2. 或者只在一个专门的分支上，例如 master，专门运行 lerna publish 进行发布，这个分支不能自己打其他 Tag

坑 2：几条分支同时进行的情况，可能生成了相同的版本号，从而发生版本冲突
解决办法：

1. 分支开发者之间约定好各自版本号
2. 或者只在一个专门的分支上，例如 master，专门运行 lerna publish 进行发布

坑 3：运行 lerna publish 如果中途有包发布失败，再运行 lerna publish 的时候，因为 Tag 已经打上去了，所以不会再重新发布包到 NPM
解决办法：

1. 运行 **lerna publish from-git**,会把当前标签中涉及的 NPM 包再发布一次，PS：不会再更新 package.json，只是执行 npm publish
2. 运行 **lerna publish from-package**，会把当前所有本地包中的 package.json 和远端 NPM 比对，如果是 NPM 上不存在的包版本，都执行一次 npm publish

坑 4：由于 git add 失败导致发布失败，导致原因：warning: LF will be replaced by CRLF in \*\*
解决方法：

1. 把自动转换功能关掉即可。
   输入命令 ：git config core.autocrlf false (仅对当前 git 仓库有效）
   git config --global core.autocrlf false (全局有效，不设置推荐全局）

[lerna 中文文档](https://chinabigpan.github.io/lerna-docs-zh-cn/)

### 硬链接和软链接

不论是硬链接或软链接都不会将原本的源文件复制一份，只会占用非常少量的磁盘空间

pnpm 有个根目录，一般都是保存在 user/.pnpm-store 下，pnpm 通过硬链接的方式保证了相同的包不会被重复下载，比如说我们已经在 repoA 中下载过一次 express@4.17.1 版本，那我们后续在 repoB 中安装 express@4.17.1 的时候是会被复用的，具体就是 repoA 中的 express 中的文件和 repoB 中的 express 中的文件指向的是同一个 inode。

### npm link & yarn link & yalc

#### npm link

在本地开发 npm 模块的时候，我们可以使用 npm link 命令，将 npm 模块链接到对应的运行项目中去，方便地对模块进行调试和测试
npm-link-module 下执行 npm link,会在全局的 node_modules 下生成执行该项目的软链
npm-link-example 下执行 npm link npm-link-module 就可以在不发布的情况下测试 npm 包的引用，实现本地 npm 块的调试

npm -g list

删除软链:

全局链接删除：npm-link-module 下,执行 npm uninstall
项目中删除安装的包：npm-link-example 下,执行 npm uninstall --no-save npm-link-module

#### yalc

包开发者流程开发工具 yalc 的使用
方便的 npm 本地组件库调试

```bash
npm i yalc -g
# or
yarn global add yalc

yalc publish

yalc add

yalc remove

yalc push

yalc update

yalc remove --all

```

### package.json

package.json 文件只能锁定大版本，也就是版本号的第一位，并不能锁定后面的小版本，你每次 npm install 都是拉取的该大版本下的最新的版本
为了稳定性考虑我们几乎是不敢随意升级依赖包的，这将导致多出来很多工作量，测试/适配等，所以 package-lock.json 文件出来了，当你每次安装一个依赖的时候就锁定在你安装的这个版本。

package.json 文件^和~区别是:

1. ^意思是将当前库的版本更新到第一个数字，例："^4.1.0"是库会更新到 4.X.X 的最新版本，但不会更新到 5.X.X 版本。
2. ~意思是更新到中间数字的版本，例："~4.7.4"是库会更新到 4.7.X 的最新版本，但不会更新到 4.8.X 版本。
3. 具体版本体现在 lock 文件

### npm dependencies

npm 会扁平化的安装依赖,安装在顶级 node_modules 中
当依赖和依赖之间依赖的 module 有冲突时,,依赖的依赖会装在依赖内部的部的 node_modules 中

扁平化依赖会导致依赖结构的不确定性,,例子: 项目依赖了 packageA 和 packageB,packageA 依赖 packageC1.0，packageB 依赖 packageC2.0,
那么依赖关系到底是根 node_modules-->packageC1.0-->packageB-->node_modules-->packageC2.0
还是根 node_modules-->packageC2.0-->packageA-->node_modules-->packageC1.0
都有可能。取决于 packageA 和 packageB 在 package.json 中的位置，如果 packageA 声明在前面，那么就是前面的结构，否则是后面的结构。
所以 package-lock.json(npm 5.x 才出现)出现，是为了保证 install 之后都产生确定的 node_modules 结构。

### npm && yarn

[![XpBr4J.md.png](https://s1.ax1x.com/2022/05/23/XpBr4J.md.png)](https://imgtu.com/i/XpBr4J)

### npm && yarn && pnpm

- npm 是单线程解析依赖项，因此安装速度可能相对较慢。(v7+后支持并行)
- yarn 可以并行下载和缓存软件包，因此安装速度通常比 npm 快。扁平化存储依赖
- pnpm 与 yarn 不同的是，它采用了一种不同的依赖项解析方法，即将依赖项安装在单个位置，并使用符号链接将它们链接到每个项目中。
- 由于 pnpm 可以共享依赖项，因此它可以更快地安装和更新模块。
- pnpm 与 yarn 类似，pnpm 也可以使用多线程解析依赖项。
- pnpm 使用更好的内存管理技术，可以更有效地利用系统资源。
- yarn 和 pnpm 支持离线模式
- 现代 npm（v3+）的依赖结构已从纯嵌套进化为扁平化 + 嵌套混合

### yarn workspace 的用法

1. 工作区定义 workspace

```json
{
  "private": true,
  "workspaces": ["packages/*"]
}
```

2. 依赖项安装在根目录

```shell
yarn install
```

3. 在所有子项目中运行相同命令

```shell
yarn workspaces run test
```

4. 项目 A 使用项目 B 的代码库

```shell
yarn workspace project-a add project-b
# 会将项目 B 安装为项目 A 的依赖项，并将其添加到项目 A 的 package.json 文件中。
```

### npm workspaces

npm 从版本 5.0.0 开始支持 workspaces 特性

1. 声明

```
  "private":"true",
  "workspaces": [
    "packages/*"
  ],

```

2. 创建子包

```shell
npm init -w packages/p1 -y

npm init -w packages/p2 -y
```

3. 交叉依赖

```shell
npm i p1 -w p2
```

安装，卸载等命令都是一样的，只是多了"--workspace="参数（简写-w），用来指定在哪个包中执行命令

workspaces 功能与 lerna 类似，如果只需简单地管理多个包，workspaces 足够了。lerna 具有版本管理，发包提示，简化多包项目发布流程等更多功能。

#### npm install 和 yarn install

1. error Tips
   npm ERR! Unsupported URL Type "workspace:": workspace:\*
   Maybe you need to check whether the project dependencies version in package.json appear [workspace:] or not. If u use npm i to install your packages, it will comeout "Unsupported URL Type "workspace:": workspace:". If u use yarn to install your packages, yarn will ask u which version you want to install.
   
#### yarn config set ignore-engines true

### 新的包管理工具: pnpm

[https://pnpm.io/zh/](https://pnpm.io/zh/)

```shell
pnpm add package -D
```

pnpm 天然内置支持仓库多包 monorepo
pnpm 内置了对 monorepo 的支持，只需在工作空间的根目录创建 pnpm-workspace.yaml 和.npmrc 配置文件，同时还支持多种配置，相比较 lerna 和 yarn workspace，pnpm 解决 monorepo 的同时，也解决了传统方案引入的问题。

pnpm 能有这么快的安装速度的得益于它的包管理机制，实现了节约磁盘空间并提升安装速度。

当使用 npm 或者 yarn 时，如果你有 100 个项目使用了某个依赖，就会有 100 份依赖的副本保存在磁盘上，对于 pnpm，依赖项将存储在一个内容可寻址的仓库中，要做到这点需要

所有文件都会在存储在硬盘上的同一个位置，当多个包被安装时，所有文件都会从同一位置创建硬链接，不会占用额外的磁盘空间，允许跨项目共享同一版本依赖。

#### pnpm 对比 npm/yarn 的优点

- 更快速的依赖下载
- 更高效的利用磁盘空间
- 更优秀的依赖管理

多个包依赖同一个包不同版本时,
路径很长，重复下载，在 npm3 之后，依赖就被扁平化管理了。依赖被顶到了顶层,按 json 文件的先后顺序
把引用次数多的包扁平化管理，但当两个引用次数一样的时候，那必然带来的不确定性
npm5 在 package.json 的基础上，又新增了 package-lock.json 文件。
npm@5.4.2版本后，如果 package.json 和 package.lock 文件不同那么，npm i 时会根据 package 的版本进行下载并更新 package-lock；如果两个文件相同则会根据 package-lock 文件下载，不管 package 有无更新
还是有问题，
幽灵依赖(项目直接用提到根部的依赖)和
依赖分身(多个包依赖同一个包不同版本时,一个被提升了,另一个在其他包里就会重复安装)

pnpm 号称 performance npm，与 npm 的依赖提升和扁平化不同。pnpm 采取了一套新的策略：内容寻址储存；

pnpm 拥有自己的.pnpm 目录，他会以平铺的方式来存储所有包，以依赖名加上版本号的名字为命名，实现了版本的复用。而且他不是通过拷贝机器缓存中的依赖到项目目录下，而是通过硬链接的方式，这能减少空间占用。

至于根目录下用于项目使用的依赖，则是通过符号链接的方式，链接到它的 .pnpm 目录下的对应位置。

默认情况下，通过 pnpm 的 node_modules 你只能访问到在 package.json 文件中声明的依赖，只有依赖项才能访问未声明的依赖项。你可能需要需要再.npmrc 文件中声明了 shamefully-host=true，他才会像 npm 平铺的方式，我们才能使用 package.json 没有显性声明的幽灵依赖。

.npmrc 文件配置 shamefully-hoist = true
配置 shamefully-hoist = true 意为我们需要第三方包中的依赖提升，也就是需要所谓的幽灵依赖。

### node-sass

node-sass 的版本需要和 node 对应，冲突可能会导致装不上的问题

<!-- ![](https://cdn.jsdelivr.net/gh/UniqueCrownClown/pic-bed@master/hexo/knowledge/nodeJsPic/nodeSass.png) -->

[![XpBDN4.png](https://s1.ax1x.com/2022/05/23/XpBDN4.png)](https://imgtu.com/i/XpBDN4)

### nexus 仓库管理器

(使用 Nexus 搭建 npm 私库，管理 npm 包，并在项目中下载)[https://blog.csdn.net/HuangsTing/article/details/125146287]

1. hosted 私有仓库

私有库是指 Nexus 中由用户创建和管理的 npm 仓库，可以用于存储公司或组织内部的 npm 包。私有库可以被其他开发者访问，但是需要提供相应的凭证才能下载其中的包。

2. proxy 代理仓库

代理库是指 Nexus 中代理公共 npm 仓库（如 npmjs.com），可以在本地缓存公共 npm 仓库中的包，加快包的下载速度。代理库会自动同步公共 npm 仓库中的更新，并将其缓存到本地。开发者可以直接从代理库中下载所需的包，无需访问公共 npm 仓库。

有的包代理库里没有,需要把 registry 恢复回来才能下到

3. group 组仓库(为了将私有库和代理库同时管理起来，在开发的时候就不需要不断的切换镜像源，只需要使用组仓库就可以了)

### 版本控制策略

- 主版本号（Major）: 当你做了不兼容的 API 修改
- 次版本号（Minor）: 当你做了向下兼容的功能性新增
- 补丁版本号（Patch）: 当你做了向下兼容的问题修复

### 渐进升级策略

兼容旧版本的策略取决于特定的需求和资源。一种常见的策略是在主版本升级后，继续维护旧版本的一个分支，以便在必要时进行修复和改进。例如，如果当前版本是 2.x.x，那么可以维护一个 1.x.x 的分支。
在实践中，以上的策略和方法可能需要根据具体的情况进行调整。一个好的渐进升级策略应能够平衡新功能的引入、旧功能的废弃以及向下兼容性的维护。
