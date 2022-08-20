---
layout: post
title:  "Updating a Jekyll gemfile"
date:   2022-08-17 08:00:00 +0800
categories: jekyll update
---

bundle outdated
Outdated gems included in the bundle:
  * concurrent-ruby (newest 1.1.10, installed 1.1.5)
  * em-websocket (newest 0.5.3, installed 0.5.1)
  * ffi (newest 1.15.5, installed 1.12.2)
  * http_parser.rb (newest 0.8.0, installed 0.6.0)
  * i18n (newest 1.12.0, installed 0.9.5)
  * jekyll (newest 4.2.2, installed 3.8.6, requested ~> 3.8.3) in group "default"
  * jekyll-feed (newest 0.16.0, installed 0.13.0, requested ~> 0.6) in group "jekyll_plugins"
  * jekyll-sass-converter (newest 2.2.0, installed 1.5.2)
  * jekyll-seo-tag (newest 2.8.0, installed 2.6.1)
  * kramdown (newest 2.4.0, installed 1.17.0)
  * liquid (newest 5.4.0, installed 4.0.3)
  * listen (newest 3.7.1, installed 3.2.1)
  * mercenary (newest 0.4.0, installed 0.3.6)
  * public_suffix (newest 5.0.0, installed 4.0.6)
  * rb-fsevent (newest 0.11.1, installed 0.10.3)
  * rouge (newest 3.30.0, installed 3.15.0)

bundle update
Calling `DidYouMean::SPELL_CHECKERS.merge!(error_name => spell_checker)' has been deprecated. Please call `DidYouMean.correct_error(error_name, spell_checker)' instead.
The dependency tzinfo-data (>= 0) will be unused by any of the platforms Bundler is installing for. Bundler is installing for ruby but the dependency is only for x86-mingw32, x86-mswin32, x64-mingw32, java. To add those platforms to the bundle, run `bundle lock --add-platform x86-mingw32 x86-mswin32 x64-mingw32 java`.
Fetching gem metadata from https://rubygems.org/............
Fetching gem metadata from https://rubygems.org/.
Resolving dependencies...
Using public_suffix 4.0.7 (was 4.0.6)
Using addressable 2.8.0
Using bundler 2.1.4
Using colorator 1.1.0
Using concurrent-ruby 1.1.10 (was 1.1.5)
Using eventmachine 1.2.7
Using http_parser.rb 0.8.0 (was 0.6.0)
Using em-websocket 0.5.3 (was 0.5.1)
Using ffi 1.15.5 (was 1.12.2)
Using forwardable-extended 2.6.0
Fetching i18n 0.9.5
Installing i18n 0.9.5
Using rb-fsevent 0.11.1 (was 0.10.3)
Using rb-inotify 0.10.1
Fetching sass-listen 4.0.0
Installing sass-listen 4.0.0
Fetching sass 3.7.4
Installing sass 3.7.4
Fetching jekyll-sass-converter 1.5.2
Installing jekyll-sass-converter 1.5.2
Using listen 3.7.1 (was 3.2.1)
Using jekyll-watch 2.2.1
Fetching kramdown 1.17.0
Installing kramdown 1.17.0
Using liquid 4.0.3
Fetching mercenary 0.3.6
Installing mercenary 0.3.6
Using pathutil 0.16.2
Using rouge 3.30.0 (was 3.15.0)
Using safe_yaml 1.0.5
Fetching jekyll 3.8.7 (was 3.8.6)
Installing jekyll 3.8.7 (was 3.8.6)
Using jekyll-feed 0.16.0 (was 0.13.0)
Using jekyll-seo-tag 2.8.0 (was 2.6.1)
Using minima 2.5.1
Bundle updated!
Post-install message from sass:

Ruby Sass has reached end-of-life and should no longer be used.

* If you use Sass as a command-line tool, we recommend using Dart Sass, the new
  primary implementation: https://sass-lang.com/install

* If you use Sass as a plug-in for a Ruby web framework, we recommend using the
  sassc gem: https://github.com/sass/sassc-ruby#readme

* For more details, please refer to the Sass blog:
  https://sass-lang.com/blog/posts/7828841




What's involved
https://www.moncefbelyamani.com/how-to-update-gems-in-your-gemfile/

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
