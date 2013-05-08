---
layout: post
title: "Venture into tmux"
date: 2013-05-06 09:00
comments: true
categories: tmux cli brew
---
I've recently gotten into tmux after experimenting with `vim` as my main editor. I initially had a frustrating experience until I changed the keybindings and used it to have my editor, `guard`, and `rails server` running on a single screen.

## Definition
[__tmux__](http://tmux.sourceforge.net/) is defined as a Terminal multiplexer that allows you to switch easily between several programs in one terminal, detach them (they keep running in the background) and reattach them to a different terminal. And do a lot more.

## Installation
On a Mac you can use [Homebrew](http://mxcl.github.io/homebrew/) to install the latest version using the following command:

```bash
brew install tmux
```

Next we want to verify that it was installed properly. We can start up tmux and name the session using the `tmux new -s` command

##Customize tmux.conf
I like to customize my tmux configuration to change some of the default tasks and add [Powerline](https://github.com/Lokaltog/powerline) to the display bar.

```bash tmux.conf
##### Basic Usage #####

# First things first:  Remap the prefix key
unbind C-b

# By default, we'll use Control-A as the prefix key.
set -g prefix 'C-a' ; bind 'C-a' send-prefix

# Reload tmux config so we can pick up changes to this file without needing to restart tmux
bind r source-file ~/.tmux.conf \; display "Reloaded tmux configuration!"

# Index windows from 1, not 0, so they line up a little better
# with the order of the number keys on the keyboard
set -g base-index 1
setw -g pane-base-index 1

# Patch for OS X pbpaste and pbcopy under tmux.
set-option -g default-command "reattach-to-user-namespace -l zsh"

##### Scrollback Navigation #####

# Use vi-style navigation in Copy mode (which is also scrollback mode)
setw -g mode-keys vi

# No escape time for vi mode
set -sg escape-time 0

# Allow proper copy/paste
set-option -g default-command "reattach-to-user-namespace -l $SHELL -l"

##### Window/Pane Management #####

# Split windows more intuitively
bind | split-window -h # horizontal columns
bind - split-window -v # vertical rows

# Navigate panes vim-style!
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R

# And windows too!
bind -r C-l select-window -t :+
bind -r C-h select-window -t :-

# Quickly jump between two windows
bind i last-window

# Resizing panes
bind -r H resize-pane -L 5
bind -r J resize-pane -D 5
bind -r K resize-pane -U 5
bind -r L resize-pane -R 5

##### Colors || Visual #####
# Ensure we're using 256 colors
set -g default-terminal "screen-256color"


# color scheme (styled as vim-powerline)
set -g status on
set -g status-utf8 on
set -g status-interval 2
set -g status-fg colour231
set -g status-bg colour234
set -g status-left-length 20
set -g status-left '#[fg=colour16,bg=colour254,bold] #S #[fg=colour254,bg=colour234,nobold]#(/usr/local/share/python/powerline tmux left)'
set -g status-right '#(/usr/local/share/python/powerline tmux right)'
set -g status-right-length 150
set -g window-status-format "#[fg=colour244,bg=colour234]#I #[fg=colour240] #[fg=colour249]#W "
set -g window-status-current-format "#[fg=colour234,bg=colour31]#[fg=colour117,bg=colour31] #I  #[fg=colour231,bold]#W #[fg=colour31,bg=colour234,nobold]"

# Ring the bell if any background window rang a bell
set -g bell-action any

# Bigger history
set -g history-limit 10000

```  

## Useful commands

Zoom into a single pane: `Prefix + Z`  
Split the window into two vertical panes: `tmux split-window` or `Prefix + -`  
Split the window into two horizontal panes: `tmux split-window -h` or `Prefix + |`  
Create a new window: `tmux new-window` or `Prefix + c`  
Select a window: `tmux select-window -t :0-9` or `Prefix + 0-9`
Rename the current window: `tmux rename-window` or `Prefix + ,`
Split the window into two vertical panes: `Prefix + |`
Split the window into two horizontal panes: `Prefix + -`



## Resources
* [thoughtbot](http://robots.thoughtbot.com/post/2641409235/a-tmux-crash-course)
* [Hawk Host Blog](http://blog.hawkhost.com/2010/06/28/tmux-the-terminal-multiplexer/)

