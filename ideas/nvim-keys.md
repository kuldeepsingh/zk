---
title: Ideas - nvim-keys
date: January 31, 2025
tags: [nvim]
---

## NVIM Binding

### Tmux Keys

- CTRL-a SHIFT-T for starting to log the tmux screen. it will store in .tmux/logging
- CTRL-a CTRL-o for shifting the screen location
- CTRL-a-a is used to go to beginning of the shell line

### VIM visual mode

- USE CTRL-v to invoke the visual mode and use the arrow keys to select the desired block.
- More mapping are defined in nvim/lua/mapping.lua

### NVIM - List all buffers

- hitting "`" key will list all the buffers

### List all registers

- hitting " key will list all the registers

### Spell check

- hitting z= key will enter the spell mode

### Snipe File Level Buffer handling

- gb for opening the buffer using snipe
- 'D' for deleting the buffer inside snipe menu

### Toggle comment / uncomment

- visual mode 'v' gc

### Open the saved session

- inside nvim CTRL-p
- CTRL-d is used to delete the saved session one by one.

### Toggle file browser

- CTRL+n will toggle the file browser bar
- Press SHIFT-H to hide the dot files
- Press '?' to get the key help

### Token listing of the file

- vim.keymap.set("n", "sym", "<cmd>SymbolsOutline<CR>", { desc = "Toggle the symbol outline" })

### Terminal

- vim.keymap.set("n", "ter", "<cmd>ToggleTerm<CR>", { desc = "Toggle the terminal" })

### Undo list

- vim.keymap.set("n", "undo", vim.cmd.UndotreeToggle)

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

### CODE Folding

- za for toggling the fold under the cursor
- zf for creating the fold under the cursor
- zo for open the fold under the cursor
- zc for close the fold under the cursor
- zE for deleting all the folds
- zM for closing all the folds
- zO for Opening all the folds

### Diff files

- <leader>d1 for diff file with current file
- <leader>d2 for choosing the 2 file to do the diff

### Zen mode

- under normal mode press 'zen' to toggle the zen mode.

### Yazi mode

- yazi command can be used to open the file browser, 
- <CR> for opening current selected file as buffer
- <C-v> For opening the current file using vertical Split
- <C-x for opening the file in the horizontal split

### Cscope

- <leader>cc and <leader>cd for checkinfg the call stack inside or outside.
- saving a file will trigger the DB building in the root
- ctags are also built.

