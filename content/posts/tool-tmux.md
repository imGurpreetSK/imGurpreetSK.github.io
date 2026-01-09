+++
date = '2026-01-08T14:57:07-08:00'
draft = false
title = 'Tools I use - Tmux'
author = "Gurpreet"
tags = ["workflow", "tmux", "terminal"]
+++

[**Tmux**](https://github.com/tmux/tmux/wiki) is a Terminal Multiplexer - a program that allows you to run multiple terminals inside one terminal instance.
It's pretty cool. Especially if you are me from 3 months back, tired of juggling a dozen iTerm tabs between 4 different projects.

This document is a cheat-sheet for me and anyone else wanting to get started with Tmux. It is also a living doc holding my notes, learnings, favorite external posts and configs.

---

### Glossary

| Term | Definition |
|------|------------|
| **Server** | Background process managing all sessions |
| **Session** | Independent workspace containing windows (e.g., one per project) |
| **Window** | Tab within a session, contains panes |
| **Pane** | Split within a window, each runs a shell |
| **Prefix** | Key combination before commands (mine: `Ctrl+z`) |
| **Copy mode** | Mode for scrolling, searching, copying text |
| **Socket** | File used for tmux communication, enables session sharing |
| **Keybinding** | Keyboard shortcut to perform an action |

Visually:
```
tmux server
 └── Session ("my-session")
      ├── Window 1 ("editor")
      │    ├── Pane 0 (nvim)
      │    └── Pane 1 (terminal)
      └── Window 2 ("git")
           └── Pane 0 (git status)
```

---

## My config

Following is the config I use for my setup - it works best for me currently, is in no way or form perfect and is derived from a lot of other peoples' configs (with help from a couple LLMs). ymmv.

Some pointers:

1. `Ctrl + ?` is your friend. I kept a window permanently open for the first month of using Tmux; I still refer to it almost everyday.
2. `man tmux` is very thorough, easy to read and informative. Highly recommended while getting started.
3. I use zsh as my default shell.
4. I use vim mode bindings.
   - I still hate `yanking` and `putting`
5. I use and love lazygit integration as a popup - it fits fabulously in my workflow.
6. My prefix key is `Ctrl+z`. Many people prefer this over the default as it's easier to reach.

```
# Set prefix to Ctrl-z (easier to reach than Ctrl-b)
unbind C-a
set -g prefix C-z
bind C-z send-prefix

# Enable mouse support
set -g mouse on

# Start windows and panes at 1, not 0
set -g base-index 1
setw -g pane-base-index 1

# Re-number windows when one is closed
set -g renumber-windows on

# Increase scrollback buffer size - I don't want to lose a command _just_ by one.
set -g history-limit 100000

# Enable 256 colors
set -g default-terminal "screen-256color"
set -ga terminal-overrides ",xterm-256color:Tc"

# Reduce escape time (useful for vim)
set -sg escape-time 10

# Split panes intuitively using | and -
bind | split-window -h -c "#{pane_current_path}"
bind - split-window -v -c "#{pane_current_path}"
unbind '"'
unbind %

# quick pane cycling: C-z C-a
unbind ^A
bind ^A select-pane -t :.+

# Navigate panes with vim keys
# bind h select-pane -L
# bind j select-pane -D
# bind k select-pane -U
# bind l select-pane -R

# Resize panes with vim keys
bind -r H resize-pane -L 5
bind -r J resize-pane -D 5
bind -r K resize-pane -U 5
bind -r L resize-pane -R 5

# Reload config with r
bind r source-file ~/.tmux.conf \; display "Config reloaded!"

# Status bar styling
set -g status-position bottom
set -g status-style bg=colour235,fg=colour250
set -g status-left ' #S '
set -g status-right ' %Y-%m-%d %H:%M '
set -g status-left-length 20

# Window status styling
setw -g window-status-current-style bg=colour238,fg=colour255,bold
setw -g window-status-current-format ' #I:#W '
setw -g window-status-format ' #I:#W '

# Pane border styling
set -g pane-border-style fg=colour238
set -g pane-active-border-style fg=colour39

# Vim mode
setw -g mode-keys vi
bind -T copy-mode-vi v send -X begin-selection
bind -T copy-mode-vi y send -X copy-pipe-and-cancel "pbcopy" # Copy to clipboard.
bind -T copy-mode-vi Escape send -X cancel
bind -T copy-mode-vi MouseDragEnd1Pane send -X copy-pipe-and-cancel "pbcopy" # Copy to clipboard via mouse.

# Quick window switching (no prefix required)
bind -n M-1 select-window -t 1
bind -n M-2 select-window -t 2
bind -n M-3 select-window -t 3
bind -n M-4 select-window -t 4
bind -n M-5 select-window -t 5

# Create window in current directory
bind c new-window -c "#{pane_current_path}"

# Open lazygit in floating window
bind G display-popup -E -w 90% -h 90% "lazygit"

# Popup temporary terminal in floating window
bind T display-popup -E -w 80% -h 80% -d "#{pane_current_path}"

# Name split window after command by default
set-hook -g after-split-window 'select-layout; select-layout -E'

# ===== THEME =====

# set -g @plugin 'catppuccin/tmux'
# set -g @catppuccin_flavour 'mocha'  # latte, frappe, macchiato, mocha

# ===== PLUGINS =====

# Plugin manager
set -g @plugin 'tmux-plugins/tpm'

# Session persistence
set -g @plugin 'tmux-plugins/tmux-resurrect'
set -g @plugin 'tmux-plugins/tmux-continuum'
set -g @continuum-restore 'on'
set -g @resurrect-capture-pane-contents 'on'

# System clipboard
set -g @plugin 'tmux-plugins/tmux-yank'

# Better session management
set -g @plugin 'tmux-plugins/tmux-sessionist'

# Initialize TPM (MUST be at the very bottom)
run '~/.tmux/plugins/tpm/tpm'
```

---

## Useful links

1. Post which inspired this post: [Tools I use: Tmux](https://justin.abrah.ms/blog/configs/tmux/) by Justin Abrahms
2. `man tmux` [https://man.openbsd.org/OpenBSD-current/man1/tmux.1]
3. Got to know about `tmux list-keys` from [this post](https://thoughtbot.com/blog/a-tmux-crash-course)

---

## Keybindings

My keybindings. This is here last as others might not find this section useful.

Categorized and ordered in decreasing frequency of usage:

### Prefix

| Key | Action |
|-----|--------|
| `Ctrl+z` | Your prefix key |
| `Ctrl+z Ctrl+z` | Send literal Ctrl+z to terminal |

### Pane Management

| Keybinding | Action |
|------------|--------|
| `Ctrl+z Ctrl+a` | Cycle to next pane |
| `Ctrl+z ||` | Split vertically (left/right) |
| `Ctrl+z -` | Split horizontally (top/bottom) |
| `Ctrl+z z` | Zoom pane (toggle fullscreen) |
| `Ctrl+z x` | Kill pane (with confirmation) |
| `Ctrl+z h` | Navigate left |
| `Ctrl+z j` | Navigate down |
| `Ctrl+z k` | Navigate up |
| `Ctrl+z l` | Navigate right |
| `Ctrl+z H` | Resize left (repeatable) |
| `Ctrl+z J` | Resize down (repeatable) |
| `Ctrl+z K` | Resize up (repeatable) |
| `Ctrl+z L` | Resize right (repeatable) |
| `Ctrl+z space` | Cycle through layouts |
| `Ctrl+z {` | Move pane left |
| `Ctrl+z }` | Move pane right |

### Window Management

| Keybinding | Action |
|------------|--------|
| `Ctrl+z c` | Create new window |
| `Ctrl+z w` | Interactive window/session tree |
| `Ctrl+z ,` | Rename window |
| `Ctrl+z n` | Next window |
| `Ctrl+z p` | Previous window |
| `Ctrl+z 0-9` | Jump to window by number |
| `Ctrl+z &` | Kill window |
| `Ctrl+z .` | Move window to new number |
| `Ctrl+z G` | Open `lazygit` in popup window |
| `Ctrl+z T` | Open temporary terminal in popup window |

### Copy Mode

| Keybinding | Action |
|------------|--------|
| `h/j/k/l` | Navigate |
| `w` / `b` | Word forward / backward |
| `0` / `$` | Start / end of line |
| `gg` / `G` | Top / bottom of history |
| `/` | Search forward |
| `?` | Search backward |
| `v` | Start selection |
| `y` | Copy (yank) |

### Utility

| Keybinding | Action |
|------------|--------|
| `Ctrl+z r` | Reload config |
| `Ctrl+z ?` | List all keybindings |
| `Ctrl+z :` | Command mode |
| `Ctrl+z t` | Show clock |

### Session Management

| Keybinding | Action |
|------------|--------|
| `Ctrl+z s` | Interactive session picker |
| `Ctrl+z d` | Detach from session |
| `Ctrl+z $` | Rename session |
| `Ctrl+z (` | Previous session |
| `Ctrl+z )` | Next session |

