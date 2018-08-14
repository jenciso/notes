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
