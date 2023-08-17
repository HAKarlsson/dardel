# How I use Dardel
In this repository, I will attempt to demonstrate how you can use Dardel for
the course DD2443. I hope this will help you.

My system: 
- Arch Linux (Linux 6.4.10-arch1-1).
- OpenSSH_9.3p2 (openssh-gssapi from AUR).
    - SSH with GSSAPI support required for SSHing Dardel.
    - Debian/Ubuntu's SSH has GSSAPI support.
    - Arch Linux's SSH *does not* have GSSAPI by default
    - For Arch Linux, replace `openssh` with `openssh-gssapi` from AUR.
- Intel i5-7200U (2 cores, 4 threads), 8GB RAM.
- Java 17.0.8 (jdk17-openjdk).
- Kerberos 5 release 1.20.1 (krb5)

## Login

First setup your ssh config:
```
$ cat ~/.ssh/config
Host dardel.pdc.kth.se
        User <kth username>
        SetEnv TERM=xterm-256color
        PreferredAuthentications gssapi-keyex,gssapi-with-mic
        GSSAPIAuthentication yes
        GSSAPIDelegateCredentials yes
        GSSAPIKeyExchange yes
```
I need the `SetEnv TERM=term-256color`, maybe you do not need it`.

Install kerberos 5 and get a kerberos ticket:
```
kinit -f <kth username>@NADA.KTH.SE
```

SSH to Dardel.
```
ssh dardel.pdc.kth.se
```

## Setting up modules

Load modules with `ml <module name/version>`. Here I load `craype`, `PDC`,
`Java`, and `tmux.
```
ml craype PDC/22.06 Java/17.0.4 tmux/3.2a
```
I do not know what craype or PDC does but they are requirements for tmux and
Java.

The module `tmux` is for enabling `tmux` - terminal multiplexing. The basic
functionality gives you multiple windows in one terminal/ssh session. More
advance use cases includes persistant sessions. I use tmux to open an editor
(vim), and compile code in one terminal.

When you quit the PDC session, all of your loaded modules gone when you log in
again. To have them persistant, execute `module s`. This saves the modules to a
default collection. When you log in, the modules should be automatically
loaded.

## Configuring the environment
When working in Dardel, I use `tmux` and `vim`.
### Tmux config
I find the default tmux configuration inconvenient. You can configure tmux by
`~/.tmux.conf`.
```
$ cat ~/.tmux.conf
# Enable mouse
set -g mouse on

# Select pane using M-h/j/k/l. (M = Meta/Alt key)
bind -n M-h select-pane -L
bind -n M-j select-pane -D
bind -n M-k select-pane -U
bind -n M-l select-pane -R

# Resize pane using S-M-h/j/k/l. (S = Shift)
bind -n M-H resize-pane -L
bind -n M-J resize-pane -D
bind -n M-K resize-pane -U
bind -n M-L resize-pane -R

# Split pane using M-v/b.
bind -n M-v split-window
bind -n M-b split-window -h
```

### Vim config
Here is a basic vim configuration:
```
$ cat ~/.vimrc
set bg=light
syntax on
set number

nnoremap j gj
nnoremap k gk
vnoremap j gj
vnoremap k gk

set list
set listchars=tab:»―,trail:×
hi Whitespace ctermfg=15 ctermbg=7
hi Visual cterm=reverse

```

## Testing Java
