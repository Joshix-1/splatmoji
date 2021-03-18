Splatmoji
=========

Quickly look up and input emoji and/or emoticons/kaomoji on your GNU/Linux desktop via pop-up menu.

\(ノ・∀・\)ノ 😃

<img src="splatmoji.gif" width="400">

Splatmoji supports skin tone filtering, custom data sets, and includes emoji annotations in all languages supported by Unicode [CLDR](http://cldr.unicode.org/).

<!-- vim-markdown-toc GFM -->

* [Install](#install)
  * [Packages](#packages)
  * [From GitHub](#from-github)
* [Usage](#usage)
  * [Setting up keyboard shortcut](#setting-up-keyboard-shortcut)
    * [i3wm](#i3wm)
    * [KDE](#kde)
    * [Gnome](#gnome)
* [Configuration](#configuration)
  * [Recently-used selection](#recently-used-selection)
  * [Copypaste mode key combination](#copypaste-mode-key-combination)
  * [Rofi config (the pop-up menu)](#rofi-config-the-pop-up-menu)
  * [Xsel config (copying to clipboard)](#xsel-config-copying-to-clipboard)
  * [Xdotool config (auto-typing)](#xdotool-config-auto-typing)
* [Updating emoji/emoticons](#updating-emojiemoticons)
  * [Emoji](#emoji)
* [Emoticons](#emoticons)
* [Custom Configuration and Custom Emoji/Emoticons](#custom-configuration-and-custom-emojiemoticons)
* [FAQ](#faq)
  * [Why do some of the emoji come out as multiple characters?](#why-do-some-of-the-emoji-come-out-as-multiple-characters)
  * [Why are my emoticons missing characters when using `type` mode?](#why-are-my-emoticons-missing-characters-when-using-type-mode)
  * [Can I make Splatmoji automatically choose a mode for me depending on context?](#can-i-make-splatmoji-automatically-choose-a-mode-for-me-depending-on-context)
  * [When I type certain characters, extra characters are copied along with them?](#when-i-type-certain-characters-extra-characters-are-copied-along-with-them)
* [Contributing](#contributing)
* [Credits](#credits)
* [License](#license)

<!-- vim-markdown-toc -->

# Install

## Packages

You can download and install the most recent packaged version of Splatmoji for your platform from the [releases](https://github.com/cspeterson/splatmoji/releases) page.

```
# Apt-based
sudo apt install ./path/to/package.deb
# Yum-based
sudo yum install ./path/to/package.rpm
```

## From GitHub

You can also get the most recent version of the code by grabbing it straight from GitHub.

Requirements:

* Bash (>=4.4)
* [rofi](https://github.com/DaveDavenport/rofi)
* xdotool (for typing your selection in for you)
* xsel (for putting your selection into the clipboard) (xclipboard also works)
* jq (only if JSON escaping is called for with the argument `--escape json`)

```sh
# If you are using Debian based distros (e.g. Ubuntu)
sudo apt-get install rofi xdotool xsel # and maybe jq
# If your distro is using yum package manager
sudo yum install rofi xdotool xsel # and maybe jq

# After you have installed the required packages, clone the repository
git clone https://github.com/cspeterson/splatmoji.git
# Run
cd /path/to/repo
./splatmoji copy
```

# Usage

```
Usage:

  splatmoji [OPTIONS]... [copy|type|copypaste] [FILE]...

  Quickly look up and input emoji and/or emoticons/kaomoji on your GNU/Linux
  desktop via pop-up menu.

  Flags:
    -e, --escape [gfm,json,rfm]
        Escape output (this really only affects emoticons). Supports
        github-flavored markdown, json, and reddit-flavored markdown escaping.

    -h, --help
        Print this help output and exit.

    -j, --disable-emoji-db
        Disable the listing of emoji from this application's own database.

    -l, --languages LANG1,LANG2,LANG3
        With emoji from the included database, it is possible to specify
        keyword/annotation languages to include in addition to `en`. `nn`
        for Norwegain, `fr-CA` for Canadian French, etc. In theory this could
        apply to both emoji *and* emoticons, but the emoticons only come in
        English at the moment.  Default: en

    -m, --disable-emoticon-db
        Disable the display of emoticons from this application's own database.

    -p, --print-languages
        Print out available language codes (as defined in [BCP47]) and exit.
        (https://www.unicode.org/reports/tr35/tr35-17.html#BCP47)

    -s, --skin-tones [light,medium-light,medium,medium-dark,dark,none]
        Specifies specific Fitzpatrick scale skin tones to display for emoji
        that can be modified by such. `none` will not print any emoji with
        skin tone modifiers. If this options is not given at all, then all skin
        tones will be displayed.

    -v, --verbose
        Print to STDERR some important internal variables as they are created:
          * config from config file
          * list of data files from which selections are sourced
          * user selection escaping

  Positional arguments:
    [copy|type|copypaste]
        This application can place the final selection into the user's
        clipboard (copy), type it out for the user (type), or place the final
        selection into the user's clipboard and paste it out for the user with
        a configured key combo (copypaste).

    [FILE]...
        A list of files or directories of files to include in the display
        regardless of the languages. The files must be TSV in the format of:
            <thing-to-display><literal tab>keyword1, keyword2, keyword3


  Examples
    splatmoji copy
    splatmoji type
    splatmoji copypaste

  Data files
    Splatmoji will by default try to combine data files from the following
    locations, when they are available:
        * [FILE]... from the command line's positional arguments.
        * ${XDG_DATA_HOME:-${HOME}/.local/share}/splatmoji/data}
        * ${XDG_DATA_DIRS:-/usr/local/share/:/usr/share/}
        * ${PROGDIR}/data

    It is also possible to individually disable the included emoji or
    emoticon databases:
        # Use only the included emoticon database. No emoji.
        splatmoji --disable-emoji-db
        # Use only the included emoji database. No emoticons.
        splatmoji --disable-emoticon-db
        # Use only user-provided files
        splatmoji --disable-emoticon-db --disable-emoji-db copy /some/custom/files

```

## Setting up keyboard shortcut

You probably would want to bind this to some key combination in your window manager/desktop environment.

### i3wm

```sh
# This would go into your `.config/i3/config` to bind to Super+slash
bindsym $mod+slash exec "/path/to/the/script copy"
```

### KDE

[This KDE.org help page](https://docs.kde.org/trunk5/en/kde-workspace/kcontrol/khotkeys/index.html) seems to outline how to do this in the popular KDE desktop environment.

### Gnome

[This Gnome.org help page](https://help.gnome.org/users/gnome-help/stable/keyboard-shortcuts-set.html.en) seems to outline how to do this in the popular Gnome desktop environment.

# Configuration

Configuration options can be changed in `<project_dir>splatmoji.config` (if running from a git clone) or by overriding the in-project config file with `${HOME}/.config/splatmoji/splatmoji.config` (recommended).

Example config:

```ini
# history_file=~/.local/state/splatmoji/history
history_length=5
paste_command=xdotool key ctrl+v
rofi_command=rofi -dmenu -p '' -i -monitor -2
xdotool_command=xdotool sleep 0.2 type --delay 100
xsel_command=xsel -b -i
```

## Recently-used selection

By default, Splatmoji will keep a list of the `${history_length}` most-recently selected entries and show them at the top of the menu.

Via the config file, you may either specify a preferred history file location or alter the number of recent entries that is displayed.

To disable this feature entirely, just set `history_length` in the config file to `0`.

## Copypaste mode key combination

This mode copies the selection to the clipboard and presses the configured paste key combo.

You can change the shortcut keys to the one you have set on your system but in most cases, it will be `ctrl+v`. It must be the shortcut keys that triggers the paste action in your computer or it will do nothing besides copying the emoji/kaomoji/emoticons.

For the key combinations, check the xdotool manpage.

```ini
# You can also change to other combination that you have set on your system that triggers the "paste" action
# paste-shortcut-keys example
paste_command=xdotool key ctrl+v
```

Note: `copypaste` mode will not work with most terminals when configured as above, but you can change it to `ctrl+shift+v` or paste it that way manually!

## Rofi config (the pop-up menu)

Examples:

```ini
# These default arguments will pop up the menu over the currently active window
rofi_command=rofi -dmenu -p : -i -monitor -2
# Alternatively, it could pop up in the middle of the current monitor with the prompt 'Search:'
rofi_command=rofi -dmenu -p 'Search:' -i -monitor -1
# Or you could specify a theme
rofi_command=rofi -dmenu -p : -i -monitor -2 -theme /path/to/themefile
```

For *many* other options, see the Rofi manpage.

## Xsel config (copying to clipboard)

You can alter the arguments sent to xsel to change, say, which "selection" your text goes into. By default it will go to the "CLIPBOARD" selection, which is the one you would usually get when doing Ctrl+c/v.

For further options, check the xsel manpage.

```ini
# You can also use xclipboard, or (likely) any other clipping tool that you can pipe an echo into to cause selection
# xclipboard example
xsel_command=xclip -selection clipboard
```

## Xdotool config (auto-typing)

You can alter the arguments sent to xdotool for typing out your selection.

For options, check the xdotool manpage.
```ini
# Example from above
xdotool_command=xdotool type
```

Ultimately, though, recognize that this tool's `type` mode relies on xdotool and it can be finnicky on any particular setup, either generally or when typing into particular applications. Just don't forget, there's always the rock-solid `copy` mode instead.

# Updating emoji/emoticons

## Emoji

I started a separate project ([Splatmoji-emojidata](https://github.com/cspeterson/splatmoji-emojidata)) dedicated to maintaining an organized, absolutely complete, and up-to-date set of emoji. It is from there that this project gets its emoji database. There shouldn't be much to update as I'll be in sync with the latest CLDR releases from Unicode, but [the repo itself][Splatmoji-emojidata] has instructions and scripts for updating directly from the source.

# Emoticons

I'm planning on creating/maintaining a comprehensive database, and would love it if someone could point me to a well-labeled and machine-readable collection.

The ones here originally came from [w33ble/emoticon-data](https://github.com/w33ble/emoticon-data) but are no longer being updated, so this is kinda it for now.

Feel free to make pull requests for what feel to you like obvious omissions though!

# Custom Configuration and Custom Emoji/Emoticons

This repo uses emoji from [Splatmoji-emojidata](https://github.com/cspeterson/splatmoji-emojidata), and the emoticons are not currently being updated, but you can use your own files either additionally or as a replacement.

The emoji/emoticons should be stored in TSV like so:
```
emoji<tab>names keywords etc
```

And then you can call the utility with your preferred data files as per [Usage](#usage) above.

Please let me know what better source you wind up using, and maybe the command(s) you use to convert it into the above format, and I'll probably work it into the repo. 🙂

# FAQ

## Why do some of the emoji come out as multiple characters?

These are called ZWJ (zero-width joiner) Sequences. Some combinations of multiple different emoji can be combined in sequence with a special zero-width character as a joiner, and if the platform and application supports it a single meaningful symbol will be displayed. On platforms or applications that *don't* support it though, no worries; it just displays the separate emoji in sequence. 🙂

## Why are my emoticons missing characters when using `type` mode?

Solving this will be between you and how you tune the [Xdotool config](#xdotool-config-auto-typing). The best solution is probably to just use `copypaste` mode instead, but if you wind up doing anything clever to solve your problem let me know and we'll see if we can work it back into this repo! 🙂

## Can I make Splatmoji automatically choose a mode for me depending on context?

Yes you certainly can - with a wrapper script! This example to decide based on the currently focused window was [contributed](issue30) by [@jpetazzo]:

```sh
#!/bin/sh
# You can figure out which window properties you want to look for using `xprop` under X
WINDOWNAME="$(xdotool getwindowfocus getwindowname)"
case "${WINDOWNAME}" in
  *Firefox*)
  exec splatmoji copypaste ;;
  *)
  exec splatmoji type ;;
esac
```

## When I type certain characters, extra characters are copied along with them?

The "extras" should be invisible - and are actually there on purpose! 😯

The basic reason is there are some emoji from the early days that can be validly shown by either their early-days "text presentation" or their more modern "emoji presentation."

So given the possibility that these emoji can by default be (and were, for some people on some platforms under some conditions) presented as their basic old text version when encoded all by their lonesome, I made the move to explicitly include the "emoji presentation selector" character on the tail of all such emoji (in my [splatmoji-emojidata] project that produces the emoji data files). This ensures that we get the pretty version!

![Emoji smilies with text and emoji presentation sequences](presentation-sequences.jpg)

So if you are seeing "tofu" or other glyphs standing in for the specifiers, this generally suggest is some issue to be resolved with with Unicode support on your platform/application/font etc. Sorry! ¯\\_\(ツ\)\_\/¯

Note: here are the deeper technical specifics on presentation sequences: [UTS #51]

# Contributing

Taking pull requests: [https://github.com/cspeterson/splatmoji.git](https://github.com/cspeterson/splatmoji.git)

Please write/adjust tests for any new functionality. With [shunit2](https://github.com/kward/shunit2) on your path, you can run the test suite likes so in the repo directory:

```sh
./test/unit_tests
```

# Credits

By [Christopher Peterson](https://chrispeterson.info) ([@cspete](https://www.twitter.com/cspete))

# License

The MIT License (MIT). Please see [LICENSE](LICENSE) for more information.

[@jpetazzo]: https://github.com/jpetazzo
[UTS #51]: https://www.unicode.org/reports/tr51/#Emoji_Variation_Sequences
[issue30]: https://github.com/cspeterson/splatmoji/issues/30
[splatmoji-emojidata]: https://github.com/cspeterson/splatmoji-emojidata
