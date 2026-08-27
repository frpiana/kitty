# Configurazione di kitty per Debian

[kitty](https://sw.kovidgoyal.net/kitty/) è il terminale usato su Debian (su
macOS il terminale è Ghostty). La repository va clonata in `~/.config/kitty`,
la cartella di configurazione standard di kitty, come per gli altri repo
([zsh](https://github.com/frpiana/zsh), [tmux](https://github.com/frpiana/tmux),
[nvim](https://github.com/frpiana/nvim)).

## Come funziona

- `kitty.conf` (versionato qui) contiene la base indipendente dal tema —
  font, cursore, padding, opacità — speculare a `ghostty/base.conf` del
  [repo starship](https://github.com/frpiana/starship).
- In fondo, `include ../starship/active/kitty.conf` carica i colori del tema
  attivo: il link in `active/` è gestito dallo script `theme` del repo
  starship, come per tmux e Neovim. I temi kitty vivono lì, in
  `themes/<nome>/kitty.conf`.
- Il cambio tema è **live**: `bin/theme` manda `SIGUSR1` alle istanze kitty
  aperte (kitty ≥ 0.23 ricarica la configurazione, include compreso). Niente
  riavvio.

A differenza di Tabby, kitty non riscrive mai la propria configurazione:
`kitty.conf` resta versionato, non serve alcuna whitelist nel `.gitignore`.
E a differenza di Tabby, kitty supporta gli indici colore oltre il 15: la
palette estesa del pantone (16–21) è replicata per intero nei temi
tabby-matcha e tabby-matcha-latte.

## Installazione (Debian)

```sh
sudo apt install kitty
git clone git@github.com:frpiana/kitty.git ~/.config/kitty
~/.config/starship/bin/theme tabby-matcha   # attiva/ricarica il tema
kitty
```

kitty apre la **shell di login presa da `/etc/passwd`**, che su Debian è `bash`.
Se il prompt che vedi è `user@host:~$` invece di quello di Starship, non è un
problema di kitty né di Starship: è il file di avvio della shell a non essere
agganciato. Su Debian la shell resta bash e la config sta nel repo
[bash](https://github.com/frpiana/bash) (Starship supporta bash nativamente,
non serve `chsh`); il repo [zsh](https://github.com/frpiana/zsh) è l'alternativa
per chi preferisce passare a zsh con `chsh -s "$(command -v zsh)"`.

Volendo, kitty può anche forzare una shell diversa da quella di sistema:

```conf
shell /usr/bin/zsh
```

ma è meglio non usarlo per rimediare a una config non agganciata: maschera il
problema solo dentro kitty, lasciandolo intatto in tmux, via SSH e negli altri
terminali.

Font: servono **JetBrainsMono Nerd Font** e **Symbols Nerd Font Mono** da
[nerdfonts.com](https://www.nerdfonts.com) (vedi il README di starship,
sezione Linux). Se legature o stylistic set non si applicano, verificare i
nomi PostScript con `kitty +list-fonts --psnames` e correggere le righe
`font_features` in `kitty.conf`.

## Prova da macOS

Su macOS il terminale principale resta Ghostty, ma kitty si può provare in
parallelo:

```sh
brew install --cask kitty
git clone git@github.com:frpiana/kitty.git ~/.config/kitty   # se non già fatto
~/.config/starship/bin/theme tabby-matcha
open -a kitty
```

kitty legge `~/.config/kitty/kitty.conf` anche su macOS (percorso XDG),
quindi il meccanismo è identico; il cambio tema live via `SIGUSR1` funziona
anche lì.

## Note

- Le impostazioni durevoli vanno in `kitty.conf` qui; i colori nei
  `themes/<nome>/kitty.conf` del repo starship.
- `background_opacity`/`background_blur` dipendono dal compositor (come per
  Ghostty: KDE sì, GNOME tipicamente no).
- La tab bar di kitty è colorata dal tema in coordinamento con la status bar
  di tmux; con tmux attivo si vede raramente, ma resta coerente.
