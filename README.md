# how-to-zsh

This is going to be a guide on how to get started with [zsh](https://en.wikipedia.org/wiki/Zsh).

### Rationale
There are projects available such as [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh), [prezto](https://github.com/sorin-ionescu/prezto), and [antigen](https://github.com/zsh-users/antigen), which are highly convenient and already offer a myriad of plugins and knobs to facilitate setting up zsh after you have experience. This project is not meant to be a replacement for those, but rather, something that will aid new zsh users in understanding the basics, so that they can know to choose whether they will want to roll out their own config or whether they're going to use a framework.

## The Prompt

If you've just installed zsh, you have been received by this welcoming prompt:
```
hostname% █
```

Where `hostname` is the name of your machine. Not very pretty or informative, is it?

There are two ways to customize your shell prompt; the first and most widely compatible way is to set the variables `PS1`, `PS2`, `PS3` and `PS4` in your `~/.profile`, like so:
```
export PS1="$(hostname) ; "
export PS2='➘ '
export PS3='\ '
export PS4='| '
```

PSx variables are part of the POSIX standard and will work in bash, zsh and many other shells. `man bash` and look for the `PROMPTING` section for more info.

TODO: the second way, using `PROMPT`.

## Pasting URLs in zsh sometimes causes weird errors

Ever had this happen to you?
```
$ youtube-dl https://www.youtube.co/watch?v=dQw4w9WgXcQ
zsh: no matches found: https://www.youtube.com/watch?v=dQw4w9WgXcQ
```
Zsh interprets some characters like `?` and `=` in a special way and tries to expand them into all sorts of useful things like filenames, history entries (see <http://zsh.sourceforge.net/Doc/Release/Expansion.html>).

Of course we want our URLs to be interpreted as they are, so the simplest solution is to encapsulate them in quotes:
```
$ youtube-dl 'https://www.youtube.com/watch?v=dQw4w9WgXcQ'
[youtube] dQw4w9WgXcQ: Downloading webpage
(...)
```
But that's annoying and you'll forget to do it all the time.

That's where the [```url-quote-magic```](https://github.com/johan/zsh/blob/master/Functions/Zle/url-quote-magic) module comes in. As you type (or paste) any URL, it will automatically escape any of those special expansion characters:
```
% youtube-dl https://www.youtube.com/watch\?v\=wFWDGTVYqE8
[youtube] wFWDGTVYqE8: Downloading webpage
(...)
```

To get that working, add this to your ```.zshrc```:
```
autoload -Uz url-quote-magic bracketed-paste bracketed-paste-magic
zle -N self-insert url-quote-magic bracketed-paste bracketed-paste-magic
```

TODO: why do we need bracketed-paste-magic?

## My Home/End keys don't work!

zsh comes with non-standard bindings, and that can be quite offputting. shells like ```bash``` 
behave similarly to how most other programs behave when it comes to those keys.

The simplest fix is to bind the keys manually, with zkbd. The following snippet runs zkbd automatically
and binds the keys to their common behaviors. Add this to your zshrc to get the desired behavior.

```
autoload zkbd
[[ ! -f ${ZDOTDIR:-$HOME}/.zkbd/$TERM-${${DISPLAY:t}:-$VENDOR-$OSTYPE} ]] && zkbd
source ${ZDOTDIR:-$HOME}/.zkbd/$TERM-${${DISPLAY:t}:-$VENDOR-$OSTYPE}
[[ -n ${key[Backspace]} ]] && bindkey "${key[Backspace]}" backward-delete-char
[[ -n ${key[Insert]} ]] && bindkey "${key[Insert]}" overwrite-mode
[[ -n ${key[Home]} ]] && bindkey "${key[Home]}" beginning-of-line
[[ -n ${key[PageUp]} ]] && bindkey "${key[PageUp]}" up-line-or-history
[[ -n ${key[Delete]} ]] && bindkey "${key[Delete]}" delete-char
[[ -n ${key[End]} ]] && bindkey "${key[End]}" end-of-line
[[ -n ${key[PageDown]} ]] && bindkey "${key[PageDown]}" down-line-or-history
[[ -n ${key[Up]} ]] && bindkey "${key[Up]}" up-line-or-search
[[ -n ${key[Left]} ]] && bindkey "${key[Left]}" backward-char
[[ -n ${key[Down]} ]] && bindkey "${key[Down]}" down-line-or-search
[[ -n ${key[Right]} ]] && bindkey "${key[Right]}" forward-char
```

## TAB Completions

```
# autocompletion by zsh-completions (installed via pacaur -S zsh-completions)
fpath=(
  /usr/share/zsh/site-functions
  $fpath
)
# had to include this
autoload -Uz compinit
compinit
```
