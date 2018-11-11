## Tmux and asciinema

If you wanna record your fancy tmux session with all windows and splits recorded, you can do it in the following way:
(setup your tmux session, create windows, splits, start processes in them)

    tmux new -s session-name
    
detach (prefix+d)

    run asciinema -c "tmux attach -t session-name" rec 

when you're finished, just detach the session again



## Tmux

Enable mouse: Edit `~/.tmux.conf` and put this line

```
set -g mouse on
tmux source-file ~/.tmux.conf
```

## Kill terminal sleep or freezed

``` 
CTRL + x 
```

## Change the buttom bar color

```
C-b, :set status-style "bg=red".
```
source: https://unix.stackexchange.com/questions/60968/tmux-bottom-bar-color-change


## tmux cheat sheet

(C-x means ctrl+x, M-x means alt+x)


## Prefix key

The default prefix is C-b. If you (or your muscle memory) prefer C-a, you need to add this to `~/.tmux.conf`:

    # remap prefix to Control + a
    set -g prefix C-a
    # bind 'C-a C-a' to type 'C-a'
    bind C-a send-prefix
    unbind C-b

I'm going to assume that C-b is your prefix.


## Sessions, windows, panes

Session is a set of windows, plus a notion of which window is current.

Window is a single screen covered with panes. (Once might compare it to a ‘virtual desktop’ or a ‘space’.)

Pane is a rectangular part of a window that runs a specific command, e.g. a shell.


## Getting help

Display a list of keyboard shortcuts:

    C-b ?

Navigate using Vim or Emacs shortcuts, depending on the value of `mode-keys`. Emacs is the default, and if you want Vim shortcuts for help and copy modes (e.g. j, k, C-u, C-d), add the following line to `~/.tmux.conf`:

    setw -g mode-keys vi

Any command mentioned in this list can be executed as `tmux something` or `C-b :something` (or added to `~/.tmux.conf`).


## Managing sessions

Creating a session:

    tmux new-session -s work

Create a new session that shares all windows with an existing session, but has its own separate notion of which window is current:

    tmux new-session -s work2 -t work

Attach to a session:

    tmux attach -t work

Detach from a session: `C-b d`.

Switch between sessions:

    C-b (          previous session
    C-b )          next session
    C-b L          ‘last’ (previously used) session
    C-b s          choose a session from a list

Other:

    C-b $          rename the current session
    C-b


## Managing windows

Create a window:

    C-b c          create a new window

Switch between windows:

    C-b 1 ...      switch to window 1, ..., 9, 0
    C-b 9
    C-b 0
    C-b p          previous window
    C-b n          next window
    C-b l          ‘last’ (previously used) window
    C-b w          choose window from a list

Switch between windows with a twist:

    C-b M-n        next window with a bell, activity or
                   content alert
    C-b M-p        previous such window


Other:

    C-b ,          rename the current window
    C-b &          kill the current window


## Managing split panes

Creating a new pane by splitting an existing one:

    C-b "          split vertically (top/bottom)
    C-b %          split horizontally (left/right)

Switching between panes:

    C-b left       go to the next pane on the left
    C-b right      (or one of these other directions)
    C-b up
    C-b down
    C-b o          go to the next pane (cycle through all of them)
    C-b ;          go to the ‘last’ (previously used) pane

Moving panes around:

    C-b {          move the current pane to the previous position
    C-b }          move the current pane to the next position
    C-b C-o        rotate window ‘up’ (i.e. move all panes)
    C-b M-o        rotate window ‘down’
    C-b !          move the current pane into a new separate
                   window (‘break pane’)
    C-b :move-pane -t :3.2
                   split window 3's pane 2 and move the current pane there

Resizing panes:

    C-b M-up, C-b M-down, C-b M-left, C-b M-right
                   resize by 5 rows/columns
    C-b C-up, C-b C-down, C-b C-left, C-b C-right
                   resize by 1 row/column

Applying predefined layouts:

    C-b M-1        switch to even-horizontal layout
    C-b M-2        switch to even-vertical layout
    C-b M-3        switch to main-horizontal layout
    C-b M-4        switch to main-vertical layout
    C-b M-5        switch to tiled layout
    C-b space      switch to the next layout


Other:

    C-b x          kill the current pane
    C-b q          display pane numbers for a short while


## Other config file settings

Force a reload of the config file on C-b r:

    unbind r
    bind r source-file ~/.tmux.conf

Some other settings that I use:

    setw -g xterm-keys on

