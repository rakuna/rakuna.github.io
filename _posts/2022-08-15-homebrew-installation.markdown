---
layout: post
title:  "Homebrew installation"
date:   2022-08-15 05:50:00 +0800
categories: jekyll homebrew
---

This blog is hosted on [GitHub Pages][github-pages], which uses [Jekyll][jekyll]. [Here's a quick video on what that means](https://youtu.be/2MsN8gpT6jY).

To test our blog posts look as expected we need a local installation of [Jekyll][jekyll].
> "Transform your plain text into static websites and blogs"

Jekyll is written in [Ruby][ruby]
> "A dynamic, open source programming language with a focus on simplicity and productivity. It has an elegant syntax that is natural to read and easy to write".

The [Jekyll documentation](https://jekyllrb.com/docs/installation/macos/#step-1-install-homebrew) does not recommend [using the system Ruby][system-ruby] (the installation of Ruby that comes pre-installed with macOS).Instead it recommends installing Ruby via [Homebrew][homebrew]
> "The Missing Package Manager for macOS"

Installation of Homebrew is done via a single command.
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

[ruby]: http://www.ruby-lang.org
[jekyll]: https://jekyllrb.com
[github-pages]: https://pages.github.com
[homebrew]: https://brew.sh
[system-ruby]: https://www.moncefbelyamani.com/why-you-shouldn-t-use-the-system-ruby-to-install-gems-on-a-mac/