---
title: '将Neovim打磨成前端开发利器'
date: '2022-10-06'
# lastmode: '2022-10-06'
tags: ['log', 'vim', 'frontend']
draft: false
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
├─ _after_  
│ └─ _plugin_ 插件配置文件  
├─ _lua_ lua 脚本  
├─ _plugin_ 会被 Neovim 自动加载  
└─ init.lua 初始配置文件

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

-- 不自动换行（不改变缓冲区里的文本）
vim.opt.wrap = false

-- 添加当前目录下的所有子目录到path路径中
vim.opt.path:append { '**' } -- Finding files - Search down into subfloders

-- 忽略文件模式列表
vim.opt.wildignore:append { '*/node_modules/*' }

-- 底色高亮
vim.cmd([[let &t_Cs = "\e[4:3m"]])
vim.cmd([[let &t_Ce = "\e[4:0m"]])

-- 离开插入模式时，清空剪切板
vim.api.nvim_create_autocmd("InsertLeave", {
  pattern = '*',
  command = "set nopaste"
})

-- 自动排版模式，在插入模式按回车时，自动插入当前注释前导符
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

-- 使用Ctrl+a全选
keymap.set('n', '<C-a>', 'gg<S-v>G')

-- 创建tab
keymap.set('n', 'te', ':tabedit<Return>', { silent = true })
keymap.set('n', 'ss', ':split<Return><C-w>w', { silent = true })
keymap.set('n', 'sv', ':vsplit<Return><C-w>w', { silent = true })

-- 在子窗口之间移动
keymap.set('n', '<Space>', '<C-w>w')
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

  -- 实用工具
  use 'nvim-lua/plenary.nvim'
  -- 文件图标
  use 'kyazdani42/nvim-web-devicons'

  -- Neosolarized: 颜色主题管理
  use {
    'svrana/neosolarized.nvim',
    requires = { 'tjdevries/colorbuddy.nvim' }
  }

  -- Lualine: 状态栏
  use 'hoob3rt/lualine.nvim'

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

  -- nvim-lspconfig: 配置Lsp
  use 'neovim/nvim-lspconfig'

  -- 自动补全括号
  use 'windwp/nvim-autopairs'
  -- 自动补全html标签
  use 'windwp/nvim-ts-autotag'

  -- 模糊查找器
  use {
    'nvim-telescope/telescope.nvim',
    'nvim-telescope/telescope-file-browser.nvim',
  }

  -- 更好看的tabs栏
  use {
    'akinsho/bufferline.nvim',
    tag = "v2.*",
  }

  -- 更好看的LSP UI
  use({
    "glepnir/lspsaga.nvim",
    branch = "main",
    config = function()
      local saga = require("lspsaga")

      saga.init_lsp_saga({
        -- your configuration
      })
    end,
  })

  --
  use 'jose-elias-alvarez/null-ls.nvim'

  --
  use 'MunifTanjim/prettier.nvim'

  -- 显示buffer区被修改的行
  use 'lewis6991/gitsigns.nvim'

  -- git相关
  use 'dinhhuy258/git.nvim'

  -- mason
  use 'williamboman/mason.nvim'
  use 'williamboman/mason-lspconfig.nvim'

  -- 快速注释
  use 'terrortylor/nvim-comment'

  -- 预览RBG颜色
  use 'norcalli/nvim-colorizer.lua'
end)
```

## 2. Plugins

- 如果没有标注特定配置，则相应的配置文件只需普通`setup`即可。
- 所有的配置文件都已托管至[Github](https://github.com/BjChacha/myConfigs/tree/main/nvim)

### /after/plugin/neosolarized.rc.lua: 颜色主题管理插件

```lua
local status, n = pcall(require, "neosolarized")
if (not status) then return end

n.setup({
  comment_italics = true,
})

local cb = require('colorbuddy.init')
local Color = cb.Color
local colors = cb.colors
local Group = cb.Group
local groups = cb.groups
local styles = cb.styles

Color.new('black', '#000000')
Group.new('CursorLine', colors.none, colors.base03, styles.NONE, colors.base1)
Group.new('CursorLineNr', colors.yellow, colors.black, styles.NONE, colors.base1)
Group.new('Visual', colors.none, colors.base03, styles.reverse)

local cError = groups.Error.fg
local cInfo = groups.Information.fg
local cWarn = groups.Warning.fg
local cHint = groups.Hint.fg

Group.new("DiagnosticVirtualTextError", cError, cError:dark():dark():dark():dark(), styles.NONE)
Group.new("DiagnosticVirtualTextInfo", cInfo, cInfo:dark():dark():dark(), styles.NONE)
Group.new("DiagnosticVirtualTextWarn", cWarn, cWarn:dark():dark():dark(), styles.NONE)
Group.new("DiagnosticVirtualTextHint", cHint, cHint:dark():dark():dark(), styles.NONE)
Group.new("DiagnosticUnderlineError", colors.none, colors.none, styles.undercurl, cError)
Group.new("DiagnosticUnderlineWarn", colors.none, colors.none, styles.undercurl, cWarn)
Group.new("DiagnosticUnderlineInfo", colors.none, colors.none, styles.undercurl, cInfo)
Group.new("DiagnosticUnderlineHint", colors.none, colors.none, styles.undercurl, cHint)
```

### /after/plugin/lualine.rc.lua: 状态栏自定义插件

```lua
local status, lualine = pcall(require, "lualine")
if (not status) then return end

lualine.setup {
  options = {
    icons_enabled = true,
    theme = 'solarized_dark',
    section_separators = { left = '', right = '' },
    component_separators = { left = '', right = '' },
    disabled_filetypes = {}
  },
  sections = {
    lualine_a = { 'mode' },
    lualine_b = { 'branch' },
    lualine_c = { {
      'filename',
      file_status = true, -- displays file status (readonly status, modified status)
      path = 0 -- 0 = just filename, 1 = relative path, 2 = absolute path
    } },
    lualine_x = {
      { 'diagnostics', sources = { "nvim_diagnostic" }, symbols = { error = ' ', warn = ' ', info = ' ',
        hint = ' ' } },
      'encoding',
      'filetype'
    },
    lualine_y = { 'progress' },
    lualine_z = { 'location' }
  },
  inactive_sections = {
    lualine_a = {},
    lualine_b = {},
    lualine_c = { {
      'filename',
      file_status = true, -- displays file status (readonly status, modified status)
      path = 1 -- 0 = just filename, 1 = relative path, 2 = absolute path
    } },
    lualine_x = { 'location' },
    lualine_y = {},
    lualine_z = {}
  },
  tabline = {},
  extensions = { 'fugitive' }
}
```

### /plugin/lspconfig.rc.lua: 方便地配置 Neovim 内置的 LSP 系统

```lua
local status, nvim_lsp = pcall(require, "lspconfig")
if (not status) then return end

local protocol = require('vim.lsp.protocol')

-- Use an on_attach function to only map the following keys
-- after the language server attaches to the current buffer
local on_attach = function(client, bufnr)
  local function buf_set_keymap(...) vim.api.nvim_buf_set_keymap(bufnr, ...) end

  local function buf_set_option(...) vim.api.nvim_buf_set_option(bufnr, ...) end

  --Enable completion triggered by <c-x><c-o>
  buf_set_option('omnifunc', 'v:lua.vim.lsp.omnifunc')

  -- Mappings.
  local opts = { noremap = true, silent = true }

  -- See `:help vim.lsp.*` for documentation on any of the below functions
  buf_set_keymap('n', 'gD', '<Cmd>lua vim.lsp.buf.declaration()<CR>', opts)
  --buf_set_keymap('n', 'gd', '<Cmd>lua vim.lsp.buf.definition()<CR>', opts)
  buf_set_keymap('n', 'gi', '<cmd>lua vim.lsp.buf.implementation()<CR>', opts)
  --buf_set_keymap('n', 'K', '<Cmd>lua vim.lsp.buf.hover()<CR>', opts)
end

protocol.CompletionItemKind = {
  '', -- Text
  '', -- Method
  '', -- Function
  '', -- Constructor
  '', -- Field
  '', -- Variable
  '', -- Class
  'ﰮ', -- Interface
  '', -- Module
  '', -- Property
  '', -- Unit
  '', -- Value
  '', -- Enum
  '', -- Keyword
  '﬌', -- Snippet
  '', -- Color
  '', -- File
  '', -- Reference
  '', -- Folder
  '', -- EnumMember
  '', -- Constant
  '', -- Struct
  '', -- Event
  'ﬦ', -- Operator
  '', -- TypeParameter
}

-- Set up completion using nvim_cmp with LSP source
local capabilities = require('cmp_nvim_lsp').update_capabilities(
  vim.lsp.protocol.make_client_capabilities()
)

nvim_lsp.flow.setup {
  on_attach = on_attach,
  capabilities = capabilities
}

nvim_lsp.tsserver.setup {
  on_attach = on_attach,
  filetypes = { "typescript", "typescriptreact", "typescript.tsx", "javascript", "javascriptreact" },
--  cmd = { "typescript-language-server", "--stdio" },
  capabilities = capabilities
}

nvim_lsp.sourcekit.setup {
  on_attach = on_attach,
}

nvim_lsp.sumneko_lua.setup {
  on_attach = on_attach,
  settings = {
    Lua = {
      diagnostics = {
        -- Get the language server to recognize the `vim` global
        globals = { 'vim' },
      },

      workspace = {
        -- Make the server aware of Neovim runtime files
        library = vim.api.nvim_get_runtime_file("", true),
        checkThirdParty = false
      },
    },
  },
}

nvim_lsp.tailwindcss.setup {}

vim.lsp.handlers["textDocument/publishDiagnostics"] = vim.lsp.with(
  vim.lsp.diagnostic.on_publish_diagnostics, {
    underline = true,
    update_in_insert = false,
    virtual_text = { spacing = 4, prefix = "●" },
    severity_sort = true,
  }
)

-- Diagnostic symbols in the sign column (gutter)
local signs = { Error = " ", Warn = " ", Hint = " ", Info = " " }
for type, icon in pairs(signs) do
  local hl = "DiagnosticSign" .. type
  vim.fn.sign_define(hl, { text = icon, texthl = hl, numhl = "" })
end

vim.diagnostic.config({
  virtual_text = {
    prefix = '●'
  },
  update_in_insert = true,
  float = {
    source = "always", -- Or "if_many"
  },
})
```

### /after/plugin/lspkind.rc.lua: 类 VSCode 的图表显示

```lua
require('lspkind').init({
  -- DEPRECATED (use mode instead): enables text annotations
  --
  -- default: true
  -- with_text = true,

  -- defines how annotations are shown
  -- default: symbol
  -- options: 'text', 'text_symbol', 'symbol_text', 'symbol'
  mode = 'symbol_text',

  -- default symbol map
  -- can be either 'default' (requires nerd-fonts font) or
  -- 'codicons' for codicon preset (requires vscode-codicons font)
  --
  -- default: 'default'
  preset = 'codicons',

  -- override preset symbols
  --
  -- default: {}
  symbol_map = {
    Text = "",
    Method = "",
    Function = "",
    Constructor = "",
    Field = "ﰠ",
    Variable = "",
    Class = "ﴯ",
    Interface = "",
    Module = "",
    Property = "ﰠ",
    Unit = "塞",
    Value = "",
    Enum = "",
    Keyword = "",
    Snippet = "",
    Color = "",
    File = "",
    Reference = "",
    Folder = "",
    EnumMember = "",
    Constant = "",
    Struct = "פּ",
    Event = "",
    Operator = "",
    TypeParameter = ""
  },
})
```

### /after/plugin/cmp.rc.lua: 自动补全系统

- cmp-buffer
- cmp-nvim-lsp

```lua
local status, cmp = pcall(require, "cmp")
if (not status) then return end
local lspkind = require 'lspkind'

cmp.setup({
  snippet = {
    expand = function(args)
      require('luasnip').lsp_expand(args.body)
    end,
  },
  mapping = cmp.mapping.preset.insert({
    ['<C-d>'] = cmp.mapping.scroll_docs(-4),
    ['<C-f>'] = cmp.mapping.scroll_docs(4),
    ['<C-Space>'] = cmp.mapping.complete(),
    ['<C-e>'] = cmp.mapping.close(),
    ['<Tab>'] = cmp.mapping.confirm({
      behavior = cmp.ConfirmBehavior.Replace,
      select = true
    }),
  }),
  sources = cmp.config.sources({
    { name = 'nvim_lsp' },
    { name = 'buffer' },
  }),
  formatting = {
    format = lspkind.cmp_format({ with_text = false, maxwidth = 50 })
  }
})

vim.cmd [[
  set completeopt=menuone,noinsert,noselect
  highlight! default link CmpItemKind CmpItemMenuDefault
]]
```

### after/plugin/treesitter.rc.lua: 增量解析系统

```lua
local status, ts = pcall(require, "nvim-treesitter.configs")
if (not status) then return end

ts.setup {
  highlight = {
    enable = true,
    disable = {},
  },
  indent = {
    enable = true,
    disable = {},
  },
  ensure_installed = {
    "tsx",
    "toml",
    "fish",
    "php",
    "json",
    "yaml",
    "swift",
    "css",
    "html",
    "lua"
  },
  autotag = {
    enable = true,
  },
}

local parser_config = require "nvim-treesitter.parsers".get_parser_configs()
parser_config.tsx.filetype_to_parsername = { "javascript", "typescript.tsx" }
```

### after/plugin/ts-autotag.rc.lua: 自动补全 html 标签

### after/plugin/autopairs.rc.lua: 自动补全括号

```lua
local status, autopairs = pcall(require, "nvim-autopairs")
if (not status) then return end

autopairs.setup({
  disable_filetype = { "TelescopPrompt", "vim" },
})
```

### after/plugin/telescope.rc.lua: 模糊文件查找(可以手动安装[ripgrep](https://github.com/BurntSushi/ripgrep)来提高搜索效率)

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

```lua
local status, telescope = pcall(require, "telescope")
if (not status) then return end
local actions = require('telescope.actions')
local builtin = require('telescope.builtin')

local function telescope_buffer_dir()
  return vim.fn.expand('%:p:h')
end

local fb_actions = require "telescope".extensions.file_browser.actions

telescope.setup {
  defaults = {
    mappings = {
      n = {
        ["q"] = actions.close
      },
    },
  },
  extensions = {
    file_browser = {
      theme = 'dropdown',
      hijack_netrw = true,
      mappings = {
        ["i"] = {
          ["<C-w>"] = function() vim.cmd('normal vbd') end, -- 清空输入
        },
        ["n"] = {
          ["N"] = fb_actions.create,
          ["h"] = fb_actions.goto_parent_dir,
          ["/"] = function()
            vim.cmd('startinsert')
          end
        },
      },
    },
  },
}

telescope.load_extension("file_browser")

vim.keymap.set('n', ';f',
  function()
    builtin.find_files({
      no_ignore = false,
      hidden = true
    })
  end)

vim.keymap.set('n', ';r', function()
  builtin.live_grep()
end)

vim.keymap.set('n', '\\\\', function()
  builtin.buffers()
end)

vim.keymap.set('n', ';t', function()
  builtin.help_tags()
end)

vim.keymap.set('n', ';;', function()
  builtin.resume()
end)

vim.keymap.set('n', ';e', function()
  builtin.diagnostics()
end)

vim.keymap.set('n', 'sf', function()
  telescope.extensions.file_browser.file_browser({
    path = "%:p:h",
    cwd = telescope_buffer_dir(),
    respect_gitignore = false,
    hidden = true,
    grouped = true,
    previewer = false,
    initial_mode = "normal",
    layout_config = { height = 40 }
  })
end)
```

### /after/plugin/bufferline.rc.lua: 更好看的标签栏

```lua
local status, bufferline = pcall(require, "bufferline")
if (not status) then return end

bufferline.setup({
  options = {
    mode = "tabs",
    separator_style = 'slant',
    always_show_bufferline = false,
    show_buffer_close_icons = false,
    show_close_icon = false,
    color_icons = true
  },
  highlights = {
    separator = {
      fg = '#073642',
      bg = '#002b36',
    },
    separator_selected = {
      fg = '#073642',
    },
    background = {
      fg = '#657b83',
      bg = '#002b36'
    },
    buffer_selected = {
      fg = '#fdf6e3',
      bold = true,
    },
    fill = {
      bg = '#073642'
    }
  },
})

vim.keymap.set('n', '<Tab>', '<Cmd>BufferLineCycleNext<CR>', {})
vim.keymap.set('n', '<S-Tab>', '<Cmd>BufferLineCyclePrev<CR>', {})
```

### /plugin/lspsaga.rc.lua: LSP UI

- `Ctrl+j`: 跳转到提示处
- `K`: 显示浮动文档
- `gd`: 查找定义
- `gp`: 预览定义
- `gr`: 变量重命名

```lua
local status, saga = pcall(require, "lspsaga")
if (not status) then return end

saga.init_lsp_saga {
  server_filetype_map = {
    typescript = 'typescript'
  }
}

local opts = { noremap = true, silent = true }
vim.keymap.set('n', '<C-j>', '<Cmd>Lspsaga diagnostic_jump_next<CR>', opts)
vim.keymap.set('n', 'K', '<Cmd>Lspsaga hover_doc<CR>', opts)
vim.keymap.set('n', 'gd', '<Cmd>Lspsaga lsp_finder<CR>', opts)
-- deprecated
-- vim.keymap.set('i', '<C-k>', '<Cmd>Lspsaga signature_help<CR>', opts)
vim.keymap.set('n', 'gp', '<Cmd>Lspsaga peek_definition<CR>', opts)
vim.keymap.set('n', 'gr', '<Cmd>Lspsaga rename<CR>', opts)
```

### plugin/null-ls.rc.lua: 使用 Neovim 作为语言服务器，通过 Lua 注入 LSP 诊断、代码操作等

- 事先手动安装[eslint_d](https://github.com/mantoni/eslint_d.js)

```lua
local status, null_ls = pcall(require, "null-ls")
if (not status) then return end

local augroup_format = vim.api.nvim_create_augroup("Format", { clear = true })

null_ls.setup ({
  sources = {
    null_ls.builtins.diagnostics.eslint_d.with({
      diagnostics_format = '[eslint] #{m}\n(#{c})'
    }),
    null_ls.builtins.diagnostics.fish
  },
  on_attach = function(client, bufnr)
    if client.server_capabilities.documentFormattingProvider then
      vim.api.nvim_clear_autocmds { buffer = 0, group = augroup_format }
      vim.api.nvim_create_autocmd("BufWritePre", {
        group = augroup_format,
        buffer = 0,
        callback = function() vim.lsp.buf.formatting_seq_sync() end
      })
    end
  end,
})
```

### after/plugin/prettier.rc.lua: 代码格式化

- 事先手动安装[prettierd](https://github.com/fsouza/prettierd)

```lua
local status, prettier = pcall(require, "prettier")
if (not status) then return end

prettier.setup {
  bin = 'prettierd',
  filetypes = {
    "css",
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact",
    "json",
    "scss",
    "less"
  }
}
```

### after/plugin/gitsigns.rc.lua: 在缓冲区显示哪些行被修改

### after/plugin/git.rc.lua: git 相关功能

```lua
local status, git = pcall(require, "git")
if (not status) then return end

git.setup({
  keymaps = {
    -- Open blame window
    blame = "<Leader>gb",
    -- Open file/folder in git repository
    browse = "<Leader>go",
  }
})
```

### after/plugin/mason.rc.lua: 便携的 Neovim 包管理，管理 Lsp，还为 Lsp 提供额外的特定语言支持（比如 TailwindCSS）

- mason-lspconfig: mason 专属的 lsp 快捷配置

```lua
local status, mason = pcall(require, "mason")
if (not status) then return end
local status2, lspconfig = pcall(require, "mason-lspconfig")
if (not status2) then return end

mason.setup {}


lspconfig.setup {
  ensure_installed = { "sumneko_lua", "tailwindcss" },
}
```

### after/plugin/nvim-comment.rc.lua: 快捷注释

- `gcc`: 注释当前行
- `gc`: 可视模式下注释所选内容所在行
- `gc{count}{motion}`：通用模式，如
  - `gcip`: 注释整段 -`gc4j`: 注释下面 4 行

### after/plugin/nvim-colorizer.rc.lua: RGB 预览

- 需要用命令启用`:ColorizerToggle`
