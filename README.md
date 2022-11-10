# pass-osx-integration
How-To and script to integrate pass https://www.passwordstore.org/ into OS X

## Content

Description of my installation steps to integrate the password manager [pass](https://www.passwordstore.org/), it's import extension [pass-import](https://github.com/roddhjav/pass-import) and its Firefox plugin [passff](https://github.com/passff/passff) together in OS X.

## Pass

`brew install pass`


## Create a gpg identity and key, initialize the pass repro

TODO

## Python

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

## Installing pass-import via pip

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

## Installing and patching passff

PassFF has two components, the FireFox extension, which can just be installed via Firefox-AddOns.
The [passff-host](https://github.com/passff/passff-host) needs to be installed as well to integrate pass and the extension.

Steps:
1. Install via install_host_app.sh (see documentation)
2. The script prints out a installation directory: `/Library/Application Support/Mozilla/NativeMessagingHosts`
3.
```
cd "~/Library/Application Support/Mozilla/NativeMessagingHosts"
```
4. Change line 1 of the file to:
`#!/usr/bin/env python3`

5. Save and restart FF

This fixes a compatibilty issue with python3 shim and pyenv! See this [Github Issue](https://github.com/passff/passff-host/issues/57)


