# how-to-zsh

This is going to be a guide on how to get started with [zsh](https://en.wikipedia.org/wiki/Zsh).

### Rationale
There are projects available such as [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh), [prezto](https://github.com/sorin-ionescu/prezto), and [antigen](https://github.com/zsh-users/antigen), which are highly convenient and already offer a myriad of plugins and knobs to facilitate setting up zsh after you have experience. This project is not meant to be a replacement for those, but rather, something that will aid new zsh users in understanding the basics, so that they can know to choose whether they will want to roll out their own config or whether they're going to use a framework.

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
