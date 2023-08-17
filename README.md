# How I use Dardel (2023)
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

## Modules in Dardel
Modules are used to acquire functionalities in Dardel.

### Loading modules
Load modules with `ml <module name/version>`. Here I load `craype`, `PDC`,
`Java`, and `tmux`.
```
~$ ml craype PDC/22.06 Java/17.0.4 tmux/3.2a
```
I do not know what craype or PDC does but they are required for tmux and Java.
I *think* the PDC module makes other modules available, and craype has
something to do with C.

The module `Java` enables Java, obviously.

The module `tmux` is for enabling `tmux` - terminal multiplexing. The basic
functionality gives you multiple windows in one terminal/ssh session. More
advance use cases includes persistant sessions. I use tmux to open an editor
(vim), and compile code in one terminal.

### Persistance
When you quit the PDC session, all of your loaded modules gone when you log in
again. To have them persistant, execute `module s` or `module save`. This saves
the modules to the `default` collection. When you log in, the modules should be
automatically loaded.

### Useful commands
- `module` :: show module commands
- `module l` :: same as `ml`
- `module l Java/17.0.4` :: same as `ml Java/17.0.4`
- `module list` :: list loaded modules
- `module avail` :: list available modules
- `module s` ::  save current modules as default collection
- `module s <name>` :: save current modules as `name` collection
- `module r` :: restore modules from default collection
- `module r <name>` :: restore modules from `name` collection
- `module reset` :: restore modules from system default
- `module -r spider <name>` :: find modules with `<name>`

## Java

After loading the Java module we have `java` and `javac`:
```
~$ javac --version
javac 17.0.4
~$ java --version
openjdk 17.0.4 2022-07-19
OpenJDK Runtime Environment Temurin-17.0.4+8 (build 17.0.4+8)
OpenJDK 64-Bit Server VM Temurin-17.0.4+8 (build 17.0.4+8, mixed mode, sharing)

```

## Configuring the environment
When working in Dardel, I use `bash`, `tmux` and `vim`. Here are my *very*
basic configuration files I use for Dardel.

### Bash config
```
~$ cat ~/.bashrc
[[ $- != *i* ]] && return

alias ls='ls --color=auto'
export PS1="\[\033[1;34m\]\w\[\033[0m\]\$ "
```

### Tmux config
I find the default tmux configuration inconvenient. You can configure tmux by
`~/.tmux.conf`.
```
~$ cat ~/.tmux.conf
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
~$ cat ~/.vimrc
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
