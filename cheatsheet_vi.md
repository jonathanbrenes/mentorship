# vi / vim Cheat Sheet (works on basically any Linux box)

## Purpose

vi/vim is the “always available” editor you can rely on when a machine is half-broken (no GUI, minimal packages, rescue mode).
This sheet is meant for fast, practical editing during troubleshooting: open, move, edit, search, replace, save, and get out.

Key idea:
- Normal/Command mode = you navigate and run commands
- Insert mode = you type text
- ESC always gets you back to Normal mode

---

## Single table of vi/vim commands

| Area | Command / Key | What it does |
|---|---|---|
| Start / open | vi <file> | Open a file (vi) |
| Start / open | vim <file> | Open a file (vim) |
| Start / open | vi <file1> ... <fileN> | Open multiple files |
| Save / exit | :w | Save (write) |
| Save / exit | :w <name> | Save as a different name |
| Save / exit | :q | Quit (only if no unsaved changes) |
| Save / exit | :q! | Quit and discard unsaved changes |
| Save / exit | :wq | Save and quit |
| Save / exit | :x | Save and quit (like :wq) |
| Save / exit | ZZ | Save and quit (normal mode shortcut) |
| Save / exit | ZQ | Quit without saving (normal mode shortcut) |
| Modes | ESC | Back to Normal mode (the “panic button”) |
| Insert mode | i | Insert before cursor |
| Insert mode | I | Insert at beginning of line |
| Insert mode | a | Append after cursor |
| Insert mode | A | Append at end of line |
| Insert mode | o | Open new line below and enter insert |
| Insert mode | O | Open new line above and enter insert |
| Movement (basic) | h / j / k / l | Left / Down / Up / Right |
| Movement (line) | 0 | Start of line |
| Movement (line) | ^ | First non-blank char of line |
| Movement (line) | $ | End of line |
| Movement (words) | w | Next word start |
| Movement (words) | b | Previous word start |
| Movement (words) | e | End of word |
| Movement (file) | gg | First line of file |
| Movement (file) | G | Last line of file |
| Movement (file) | :<n>  OR  <n>G  OR  <n>gg | Go to line n |
| Movement (screen) | Ctrl+f / Ctrl+b | Page down / Page up |
| Movement (screen) | Ctrl+d / Ctrl+u | Half-page down / Half-page up |
| Movement (screen) | zz | Center cursor line on screen |
| Movement (screen) | zt / zb | Put cursor line at top / bottom of screen |
| Find char | f<char> | Jump to next occurrence of char on line |
| Find char | F<char> | Jump to previous occurrence of char on line |
| Find char | t<char> | Jump to just before next occurrence of char |
| Find char | T<char> | Jump to just after previous occurrence of char |
| Find char | ; / , | Repeat last f/t (same / reverse direction) |
| Delete | x | Delete character under cursor |
| Delete | X | Delete character before cursor |
| Delete | dd | Delete current line |
| Delete | <n>dd | Delete n lines |
| Delete | dw | Delete word |
| Delete | D  (or d$) | Delete from cursor to end of line |
| Change (edit) | cw | Change word (delete word, enter insert) |
| Change (edit) | cc | Change whole line |
| Change (edit) | C  (or c$) | Change to end of line |
| Copy/Yank | yy  (or Y) | Yank (copy) current line |
| Copy/Yank | <n>yy | Yank n lines |
| Paste | p | Paste after cursor / below line |
| Paste | P | Paste before cursor / above line |
| Undo/redo | u | Undo last change |
| Undo/redo | U | Undo changes on the current line |
| Undo/redo | Ctrl+r | Redo |
| Repeat | . | Repeat last change |
| Search | /text | Search forward |
| Search | ?text | Search backward |
| Search | n / N | Next match / previous match |
| Search | :noh | Clear search highlight (vim) |
| Replace | :s/old/new/ | Replace first match on current line |
| Replace | :s/old/new/g | Replace all matches on current line |
| Replace | :%s/old/new/g | Replace all matches in file |
| Replace | :%s/old/new/gc | Replace all matches in file w/ confirm |
| Visual select | v | Visual mode (select chars) |
| Visual select | V | Visual line mode (select lines) |
| Visual select | Ctrl+v | Visual block mode (columns) |
| Visual actions | y | Yank selection |
| Visual actions | d | Delete selection |
| Visual actions | > / < | Indent right / left selection |
| Visual actions | ~ | Toggle case of selection |
| File insert | :r <file> | Insert another file after current line |
| Settings | :set number  (or :set nu) | Show line numbers |
| Settings | :set nonumber  (or :set nonu) | Hide line numbers |
| Settings | :set list | Show hidden characters (tabs/EOL/trailing) |
| Settings | :set nolist | Hide hidden characters |
| Redraw | Ctrl+l | Redraw screen |
| Info | Ctrl+g | Show file + position info |
| Shell escape | :sh | Drop to shell (exit shell with Ctrl+d) |

---

## Tiny “survival workflow” (the one you use in rescue calls)

1) Open file: vi /etc/fstab
2) Enable line numbers: :set nu
3) Find what you need: /nofail   (or /UUID=)
4) Edit: i / a / o  (ESC when done)
5) Save + exit: :wq
