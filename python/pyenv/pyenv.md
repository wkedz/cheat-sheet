# Installation

## Ubuntu

## Update os, and install dependencies
apt-get update -y
apt-get install make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev xz-utils tk-dev libffi-dev liblzma-dev git

## Install pyenv by script
curl https://pyenv.run | bash

## Set env
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n eval "$(pyenv init -)"\nfi' >> ~/.bashrc

## Restart shell
exec "$SHELL"

## Check available installation
pyenv install --list

## Install target python
pyenv install 3.8.4

## Check current version
pyenv versions

## Set global version
pyenv global 3.8.3

## General help
pyenv --help

# pyenv-virtualenv

## Instalation
git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv

## Add virtualenv-init (OPTIONAL)
echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.bashrc

## Restart
exec "$SHELL"

## Usage

### create virtual env based on python 2.7.10 in folder called my-virtual-env-2.7.10
pyenv virtualenv 2.7.10 my-virtual-env-2.7.10 

### create virtualenv from current version
pyenv virtualenv venv34

### list virtual env
pyenv shell venv34
pyenv virtualenvs

### Ac/Deactivate
pyenv activate <name>
pyenv deactivate

## Delete existing virtualenv
pyenv uninstall my-virtual-env
