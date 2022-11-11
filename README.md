# pass-osx-integration
How-To and script to integrate pass https://www.passwordstore.org/ into OS X

## Content

Description of my installation steps to integrate the password manager [pass](https://www.passwordstore.org/), it's import extension [pass-import](https://github.com/roddhjav/pass-import) and its Firefox plugin [passff](https://github.com/passff/passff) together in OS X.

### Pass

`brew install pass`


### Create a gpg identity and key, initialize the pass repro

TODO

### Python

I removed all python3 version I had installed via homebrew and installed pyenv

  `brew install pyenv`
  
Change .zshrc and .zprofile as described in [PyEnv Documentation](https://github.com/pyenv/pyenv#set-up-your-shell-environment-for-pyenv)

```
export PYENV_ROOT="$HOME/.pyenv"
command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
```

```
pyenv install 3.11.0
pyenv global 3.11.0
```

Starting a new shell, python should now have the correct version

```
python --version
> Python 3.11.0
```

### pass-import

pass-import is an extension for pass that allows easy import and export of passwords to and from pass.

`pip install pass-import --user`

This will install the site-packages into a `$HOME/.local/lib/python3.9/site-packages/usr/lib/password-store/extensions

Make those extension available to pass vi `.zshrc`

```
export PASSWORD_STORE_ENABLE_EXTENSIONS=true
export PASSWORD_STORE_EXTENSIONS_DIR="$HOME/.local/lib/python3.11/site-packages/usr/lib/password-store/extensions
```

`pimport` executable ends up in ~/.local/bin, so add that to path as well in .zshrc

```
export PATH=$PATH:"$HOME/.local/bin"
```

Verify with:
```
pass import
[x] Error: The source password manager or the path to import is empty
```

(if the error message is *Error: import is not in the password store.*, the installation has failed)

### passff

[PassFF](https://github.com/passff/passff) is a FireFox-Addon for pass which can be installed via the [Mozilla Addon page](https://addons.mozilla.org/firefox/addon/passff). 

However, to integrate with pass and gpg, it requires to setup two dependecies.

#### passff-host

The [passff-host](https://github.com/passff/passff-host) needs to be installed to integrate pass and the addon. As the time of writing, there is a compatibilty issue with python3, when installed via pyenv. See this [Github Issue](https://github.com/passff/passff-host/issues/57)

Steps:
1. Install via install_host_app.sh (see documentation)
2. The script prints out a installation directory: `/Library/Application Support/Mozilla/NativeMessagingHosts`
3.
```
cd ~/Library/Application\ Support/Mozilla/NativeMessagingHosts
```
4. Change line 1 of the file to:
`#!/usr/bin/env python3`

5. Save and restart FF

#### pinentry-mac

When pass is installed via brew, as described above, it comes with a terminal based pin-entry dialog that can not be used from outside of the terminal, so passff failes.

The error messages given from passff are misleading: "gpg: command not found", but the troubleshooting section on the passff README points in the right direction.

To install a proper pinentry

```
brew install pinentry-mac
```

Edit or create a .gnupg/gpg-agent.conf as descibed in the caveat text:

```
You can now set this as your pinentry program like
~/.gnupg/gpg-agent.conf
          pinentry-program #{HOMEBREW_PREFIX}/bin/pinentry-mac
```

```
default-cache-ttl 18000
max-cache-ttl 86400
ignore-cache-for-signing
pinentry-program /opt/homebrew/bin/pinentry-mac
```

The cache settings here control how long the keys will be available before the passphrase is requested again.


Restart gpg-agent on your system:

```
gpgconf --kill gpg-agent && gpgconf --launch gpg-agent
```

The gpg-agent manpage also mentions that 

```
export GPG_TTY=$(tty)
```

should be added to the correct exports during shell startup (e.g. .zshrc)


## Migration of existing password database to pass

tbd
