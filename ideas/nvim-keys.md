---
title: nvim-keys
date: January 31, 2025
tags: [nvim]
---
## NVIM Binding

### Tmux Keys
   - CTRL-A SHIFT-T for starting to log the tmux screen. it will store in .tmux/logging
   - CTRL-A CTRL-o for shifting the screen location

###  VIM visual mode 
   - USE CTRL-V to invoke the visual mode and use the arrow keys to select the desired block.

###  NVIM - List all buffers
   - hitting "`" key will list all the buffers

###  List all registers
   - hitting " key will list all the registers

###  Spell check
   - hitting z= key will enter the spell mode
   
### Snipe File Level Buffer handling 
   - gb for opening the buffer using snipe
   - 'D' for deleting the buffer inside snipe menu

### Toggle comment / uncomment 
   - visual mode 'v' gc

### Open the saved session
   - inside nvim CTRL-P

### Toggle file browser
   - CTRL+n will toggle the file browser bar 
   - Press SHIFT-H to hide the dot files
   - Press '?' to get the key help

### Token listing of the file
   - vim.keymap.set("n", "<leader>ts", "<cmd>SymbolsOutline<CR>", { desc = "Toggle the symbol outline" })

### Terminal
   - vim.keymap.set("n", "<leader>tt", "<cmd>ToggleTerm<CR>", { desc = "Toggle the terminal" })

### Undo list
   - vim.keymap.set("n", "<leader>tu>", vim.cmd.UndotreeToggle)

### Telescope    
   - vim.keymap.set("n", "<leader>ff", builtin.find_files, { desc = "Telescope find files" })
   - vim.keymap.set("n", "<leader>fg", builtin.live_grep, { desc = "Telescope live grep" })
   - vim.keymap.set("n", "<leader>fd", builtin.grep_string, { desc = "Telescope on curesor word live-grep" })
   - vim.keymap.set("n", "<leader>fb", builtin.buffers, { desc = "Telescope buffers" })
   - vim.keymap.set("n", "<leader>fh", builtin.help_tags, { desc = "Telescope help tags" })
   - vim.keymap.set("n", "<leader>ft", builtin.lsp_document_symbols, { desc = "Telescope current buffer tags" })

### DAP
   - vim.keymap.set("n", "<leader>db", "<cmd>DapToggleBreakpoint<CR>", { desc = "Toggle the break point" })
   - vim.keymap.set("n", "<F2>", require("dap").toggle_breakpoint)

   - vim.keymap.set("n", "<leader>dr", "<cmd>DapContinue<CR>", { desc = "run or continue the debugger" })
   - vim.keymap.set("n", "<F5>", require("dap").continue)

   - vim.keymap.set("n", "<leader>dn", require("dap").step_over)
   - vim.keymap.set("n", "<F7>", require("dap").step_over)

   - vim.keymap.set("n", "<leader>di", require("dap").step_into)
   - vim.keymap.set("n", "<F8>", require("dap").step_into)

   - vim.keymap.set("n", "<leader>do", require("dap").step_out)
   - vim.keymap.set("n", "<F9>", require("dap").step_out)
