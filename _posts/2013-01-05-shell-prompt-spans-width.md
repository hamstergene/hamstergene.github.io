---
title: "Shell prompt that spans console width"
redirect_from:
    - /blog/2013-01-05-shell-prompt/
    - /post/shell-prompt-spans-width/
---

This is how I solved the problem of separating commands from their output, once and for all.

This neat trick allows to stretch the prompt so it fills the width of the terminal window:

![](/img/posts/shell-prompt-spans-width.png)

Add to your `.bash_profile`

```bash
if [[ "$TERM" == xterm* ]]; then
    export PS1='\[\e[0;36m\n`/usr/bin/printf %${COLUMNS}s "+jobs:\j+    +\l+" | /usr/bin/tr " +" "- "`\r\u@\H:\[\e[0;32m\]\w\[ \]\n\[\e[1;33m\]  \$ \[\e[0m\]'
fi
```

