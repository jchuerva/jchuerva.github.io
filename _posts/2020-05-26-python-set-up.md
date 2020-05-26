---
title: Python set up
date: 2020-05-26 16:00:00 +01:00
tags: [python, mac, homebrew]
description: Python set up
---

## Index
- [Configure Python3 properly: `pyenv`](#configure-python3-properly---pyenv-)
- [Virtualenv](#virtualenv)
- [Manage libraries](#manage-libraries)
- [Configs files](#configs-files)

### Configure Python3 properly: `pyenv`

The easiest way to get started is with Homebrew
```bash
brew install pyenv
```

Now let's install the latest Python version (3.7.3 as of this writing)
```bash
pyenv install 3.7.3
```

Set it as our global default version for pyenv environments
```bash
pyenv global 3.7.3
# and verify it worked
pyenv version
```

The power of pyenv comes from its control over our shell's path. In order for it to work correctly, we need to add the following to our configuration file (.zshrc for me, possibly .bash_profile for you):
```
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.zshrc
```

After that command, our dotfile (.zshrc for zsh or .bash_profile for Bash) should look include these lines:

```
if command -v pyenv 1>/dev/null 2>&1; then
  eval "$(pyenv init -)"
fi
```

We can confirm pyenv is managing our Python 3 version
```bash
which python
#/Users/mbbroberg/.pyenv/shims/python
python -V
# Python 3.7.3
pip -V
# pip 19.0.3 from /Users/mbbroberg/.pyenv/versions/3.7.3/lib/python3.7/site-packages/pip (python 3.7)
```

### Virtualenv
- Install it
```bash
pip install --user pipenv
```

- Create and activate it (eg: `venv`)
```
virtualenv venv
source venv/bin/activate 
```

- Deactivate
```
deactivate
```

### Manage libraries

- List libraries
```bash
pip list
```

- Create `requirements.txt`
```
pip freeze --local > requirements.txt 
```

- Install from `requirements.txt`
```
pip install -r requirements.txt
```

### Configs files

Having the `config/auth.py` that includes the toke variable, we can import it using:
```
from config.auth import token
```

