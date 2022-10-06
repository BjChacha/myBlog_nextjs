---
title: '将Neovim打磨成前端开发利器'
date: '2022-10-06'
# lastmode: '2022-10-06'
tags: ['log', 'vim', 'frontend']
# draft: true
summary: '抛开鼠标的束缚，让双手在键盘上灵动飞舞，愉快地装X（'
# images: ['']
authors: ['default']
layout: PostLayout
---

# 将 Neovim 打磨成前端开发利器

- 为什么要用 vim
  - 装逼（最主要目的）
  - 轻量
  - 减少右手在键盘-鼠标之间的切换时间，避免中断编码节奏和思路
    - 其实对我来说，这屁点时间在整个开发过程中可谓杯水车薪，并不会对整体开发效率有明显的提升
- 本文几乎所有配置均参考至[devaslife](https://www.youtube.com/watch?v=ajmK0ZNcM4Q&t=3149s)，并根据各插件最新版本的改动和个人喜好进行调整。

## 0.Structure

nvim

- after
  - plugin 插件配置文件
- lua lua 脚本
- plugin 会被 Neovim 自动加载
- _init.lua_ 初始配置文件

## 1. Base Configuration

### init.lua

- 引入一些 lua 脚本
  - 比如`require('base')...`
- 根据当前系统引入不同的脚本（多平台自适应适配）

### lua/windows.lua

> Windows 系统下的专属配置

```lua
-- 将""寄存器"*寄存器连接到系统剪切板中
vim.opt.clipboard:prepend { 'unnamed', 'unnamedplus' }
```

### lua/base.lua

> 主要负责基础配置，比如编码、缩进等。

```lua
-- 清空自动命令
vim.cmd('autocmd!')

-- 取消鼠标定位光标
vim.opt.mouse = 'r'

-- 编码相关
vim.scriptencoding = 'utf-8'
vim.opt.encoding = 'utf-8'
vim.opt.fileencoding = 'utf-8'

-- 启用行号
vim.wo.number = true

-- 显示标题（filestring变量的值）
vim.opt.title = true

-- 高亮查找模式匹配的地方
vim.opt.hlsearch = true

-- vim.opt.backup: 写回文件时建立备份
-- false: 给文件换名，然后写入一个新文件
vim.opt.backup = false

-- 在屏幕最后一行显示命令
vim.opt.showcmd = true

-- 屏幕显示命令的行数
vim.cmdheight = 1

-- 最后一个窗口总是显示状态行
vim.opt.laststatus = 2

-- 插入tab时使用合适数量的空格
vim.opt.expandtab = true

-- 光标上下两侧最少保留的屏幕行数
vim.opt.scrolloff = 10

-- 使用!和:!命令的shell名，windows这里用默认的(`cmd.exe`)就行
-- vim.opt.shell = 'fish'

-- 匹配模式则不会建立备份
vim.opt.backupskip = '/tmp/*./private/tmp/*'

-- 限制某些指令的可视化效果
vim.opt.inccommand = 'split'

-- 搜索模式中忽略大小写
vim.opt.ignorecase = true

-- 自动缩进
vim.opt.autoindent = true

-- 首行的Tab根据`shiftwidth`插入空白
vim.opt.smarttab = true
vim.opt.shiftwidth = 2

-- 回车后保持视觉上的缩进
vim.opt.breakindent = true

-- Tab代表的空格数
vim.opt.tabstop = 2

-- 这两个应该跟上面两个设置重复了
-- vim.opt.ai = true -- Auto indent
-- vim.opt.si = true -- Smart indent

-- 不自动换行（不改变缓冲区里的文本）
vim.opt.wrap = false -- No wrap lines

-- 允许退格的位置：在插入开始的位置上、在换行符上、在自动缩进上
-- 似乎是针对MacOS的
-- vim.opt.backspace = 'start,eol,indent'

-- 添加当前目录下的所有子目录到path路径中
vim.opt.path:append { '**' } -- Finding files - Search down into subfloders

-- 忽略文件模式列表
vim.opt.wildignore:append { '*/node_modules/*' }

-- 底色高亮
-- Undercurl
vim.cmd([[let &t_Cs = "\e[4:3m"]])
vim.cmd([[let &t_Ce = "\e[4:0m"]])

-- 离开插入模式时，清空剪切板
-- Turn off paste mode when leaving insert
vim.api.nvim_create_autocmd("InsertLeave", {
  pattern = '*',
  command = "set nopaste"
})

-- 自动排版模式，在插入模式按回车时，自动插入当前注释前导符
-- Add asterisks in block comments
vim.opt.formatoptions:append { 'r' }
```

### lua/highlighs.lua

> 主要负责文本高亮设置

```lua
-- 高亮光标所在行
vim.opt.cursorline = true

-- 使用终端颜色
vim.opt.termguicolors = true

-- 使用半透明（0为不透明）
vim.opt.winblend = 0

-- 使用弹出菜单来显示补全匹配
vim.opt.wildoptions = 'pum'

-- 弹出菜单的透明度
vim.opt.pumblend = 5

-- 尝试使用深色背景看起来舒服的颜色
vim.opt.background = 'dark'
```

### lua/maps.lua

> 主要负责键位映射

```lua
local keymap = vim.keymap

-- 使用黑洞寄存器来使用x
keymap.set('n', 'x', '"_x')

-- 用+/-来自增/自减光标下的数值
keymap.set('n', '+', '<C-a>')
keymap.set('n', '-', '<C-x>')

-- 使用dw时反向删除单词
-- keymap.set('n', 'dw', 'vb"_d')

-- 使用Ctrl+a全选
keymap.set('n', '<C-a>', 'gg<S-v>G')

-- 创建tab
keymap.set('n', 'te', ':tabedit<Return>', { silent = true })
keymap.set('n', 'ss', ':split<Return><C-w>w', { silent = true })
keymap.set('n', 'sv', ':vsplit<Return><C-w>w', { silent = true })

-- 在子窗口之间移动
keymap.set('n', '<Space>', '<C-w>w')
-- keymap.set('', 's<left>', '<C-w>h')
-- keymap.set('', 's<up>', '<C-w>k')
-- keymap.set('', 's<down>', '<C-w>j')
-- keymap.set('', 's<right>', '<C-w>l')
keymap.set('', 'sh', '<C-w>h')
keymap.set('', 'sj', '<C-w>j')
keymap.set('', 'sk', '<C-w>k')
keymap.set('', 'sl', '<C-w>l')

-- 调整子窗口大小
keymap.set('n', '<C-w>h', '<c-w><')
keymap.set('n', '<C-w>j', '<c-w>+')
keymap.set('n', '<C-w>k', '<c-w>-')
keymap.set('n', '<C-w>l', '<c-w>>')
```

### lua/plugins.lua

> 主要负责加载插件（不负责插件配置）
> 需要事先安装 Packer

```lua
-- 启用Pakcer
local status, packer = pcall(require, 'packer')
if (not status) then
  print("Pakcer is not installed")
  return
end

vim.cmd [[packadd packer.nvim]]

-- 应用插件列表
packer.startup(function(use)
  -- Packer
  use 'wbthomason/packer.nvim'

  -- Neosolarized: 颜色主题管理
  use {
    'svrana/neosolarized.nvim',
    requires = { 'tjdevries/colorbuddy.nvim' }
  }

  -- Lualine: 状态栏
  use {
    'hoob3rt/lualine.nvim', -- Statusline
    requires = { 'kyazdani42/nvim-web-devicons', opt = true }
  }

  -- 类VSCode的图表显示
  use 'onsails/lspkind-nvim' -- vscode-like pictograms

  -- 基于Lua的Neovim代码片段引擎
  use 'L3MON4D3/LuaSnip'

  -- Neovim自动补全引擎
  use 'hrsh7th/nvim-cmp' -- Completion
  -- buffer区域的自动补全
  use 'hrsh7th/cmp-buffer' -- nvim-cmp source for buffer words
  -- 内置LSP的自动补全
  use 'hrsh7th/cmp-nvim-lsp' -- nvim-cmp source for neovim's buil-in LSP

  -- Neovim中的treesitter配置和虚拟层
  -- treesitter: 增量解析系统，可以为源文件构建完整的语法树，在源文件被编辑时高效地更新
  use {
    'nvim-treesitter/nvim-treesitter',
    run = function() require('nvim-treesitter.install').update({ with_sync = true }) end,
  }

  -- nvim-lsp-installer: 自动安装nvim支持的lsp安装器
  -- nvim-lspconfig: 配置lsp
  -- LSP（Language Server Protocol），用于支持某个语言服务
  use {
    "williamboman/nvim-lsp-installer",
    "neovim/nvim-lspconfig",
  }
end)
```

## 2. Plugins

- Neosolarized: 颜色主题管理插件

- Lualine: 状态栏自定义插件

- Lspconfig: 方便地配置 Neovim 内置的 LSP 系统

- Autotag: 自动补全 html 标签

- Autopairs: 自动补全括号

- Telescope: 模糊文件查找(可以手动安装[ripgrep](https://github.com/BurntSushi/ripgrep)来提高搜索效率)

  - `;f`: 查找文件
  - `;r`: 实时查找
  - `\\`: 缓存区查找
  - `;t`: 标签帮助
  - `;;`: 恢复上一次操作
  - `;e`: 显示提示
  - `sf`: 启用文件浏览器
    - `N`: 创建文件
    - `h`: 返回上一级
    - `/`: 查找模式

- Bufferline: 更好看的标签栏

- Lspsaga: LSP UI

  - `Ctrl+j`: 跳转到提示处
  - `K`: 显示浮动文档
  - `gd`: 查找定义
  - `gp`: 预览定义
  - `gr`: 变量重命名

- null-ls: 使用 Neovim 作为语言服务器，通过 Lua 注入 LSP 诊断、代码操作等

  - 事先手动安装[eslint_d](https://github.com/mantoni/eslint_d.js)

- Prettier: 代码格式化

  - 事先手动安装[prettierd](phttps://github.com/fsouza/prettier)

- gitsigns: 在缓冲区显示哪些行被修改

- git: git 相关功能

- mason: 便携的 Neovim 包管理，管理 Lsp，还为 Lsp 提供额外的特定语言支持（比如 TailwindCSS）

  - mason-lspconfig: mason 专属的 lsp 快捷配置

- nvim-comment: 快捷注释
  - `gcc`: 注释当前行
  - `gc`: 可视模式下注释所选内容所在行
  - `gc{count}{motion}`：通用模式，如
    - `gcip`: 注释整段 -`gc4j`: 注释下面 4 行
- nvim-colorizer: RGB 预览
  - 需要用命令启用`:ColorizerToggle`
