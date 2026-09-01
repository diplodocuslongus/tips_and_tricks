The more complete cheatsheet is in the vim2know repo.

leader is , (comma)

[leader] b 

### Custom 

| Command    | Action                                              |
| :---       | :---                                                |
| `leader b` | insert line break in normal mode at cursor location |
| `w` / `b`  | Forward/backward by word                            |
| `^` / `$`  | Move to first/last character of line                |
| `gg` / `G` | Move to top/bottom of file                          |
| `0`        | Move to the start of the line                       |

### Movement

| Command            | Action                               |
|--------------------|--------------------------------------|
| `h`, `j`, `k`, `l` | Move cursor left, down, up, right    |
| `w` / `b`          | Forward/backward by word             |
| `^` / `$`          | Move to first/last character of line |
| `gg` / `G`         | Move to top/bottom of file           |
| `0`                | Move to the start of the line        |

### Editing

|----------------|-----------------------------------------|
| Command        | Action                                  |
|----------------|-----------------------------------------|
| `i` / `a`      | Enter insert mode before/after cursor   |
| `o` / `O`      | Open new line below/above cursor        |
| `u` / `Ctrl+r` | Undo / Redo                             |
| `x`            | Delete character under cursor           |
| `dd` / `yy`    | Delete (cut) / Yank (copy) current line |
| `p` / `P`      | Paste below/above current line          |

### Search & Replace

|-----------------|--------------------------------------|
| Command         | Action                               |
|-----------------|--------------------------------------|
| `/text`         | Search forward for text              |
| `n` / `N`       | Next/previous search match           |
| `:%s/old/new/g` | Replace all 'old' with 'new' in file |

### Files & Windows

| Command       | Action                       |
| :---          | :---                         |
| `:w` / `:q`   | Save / Quit                  |
| `:wq` or `:x` | Save and quit                |
| `:e <file>`   | Open file                    |
| `:sp <file>`  | Split window horizontally    |
| `:vsp <file>` | Split window vertically      |
| `Ctrl+w + w`  | Switch between split windows |

