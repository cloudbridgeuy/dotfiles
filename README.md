dotfiles
===

CloudBride UY suggested dotfiles to develop a Personal Development Environment `PDE`.

# Getting Started

These are the recommended steps to install everything from scratch on a new machine.

Go to GitHub and fork this repository. The rest of these steps should be done from your forked repo.

First, start by installing `brew`. You should check their [site](https://brew.sh/) to get the
current installation method. Then install GitHub's `cli` tool using `brew`.

```bash
brew install gh
```

We are going to need to create a new `ssh` key to access this repository:

```bash
ssh-keygen -t ed25519 -C "<email>"
```

You should now have a public and a private key on `$HOME/.ssh`. Make a note of its location.

Log in to your account using `gh auth login`, and add the `ssh` key we created previously.

We can now clone this repository. Create a directory called `$REPO_DIR` and clone
this repository there.

> Consider setting these environment variables
    ```bash
    export GITHUB_USERNAME=<username>
    export REPO_DIR=<repo_dir>
    ```

```bash
mkdir -p ~/Projects/Personal
gh clone "$GITHUB_USERNAME/dotfiles" "$REPO_DIR"
```

We suggest you use `~/Projects/Personal` as the location to clone this repository.

## Install Dependencies [1/2]

We need to install all dependencies in two steps. First, we'll install all dependencies that require
`brew`, `luarocks` and `git`. This will allow us to load the `zsh` configuration so we can continue
installing the other dependencies.

```bash
ansible-playbook "$REPO_DIR/ansible/brew.yml" --extra-vars="root=$REPO_DIR"
ansible-playbook "$REPO_DIR/ansible/clone.yml" --extra-vars="root=$REPO_DIR"
ansible-playbook "$REPO_DIR/ansible/luarocks.yml" --extra-vars="root=$REPO_DIR"
```

> It's **very important** that you provide the `root` variable as an `extra-var`.

## Stowing configuration files

The next step requires `stowing` our configuration files into their appropriate folders. Most of them
will go inside the `~/.config` and `~/.local/bin` folders so start by creating them.

```bash
# Create the required directories
mkdir -p ~/.config
mkdir -p ~/.config/repos
mkdir -p ~/.local/bin

# Stow the repos folder
stow -t ~/.config/repos repos

# Stow the .config folder
stow -t ~/.config config

# Stow the dotfiles
mv ~/.zprofile ~/.zprofile.bak
mv ~/.zshrc ~/.zshrc.bak
stow -t ~ dotfiles

# Create symlinks
ansible-playbook "$REPO_DIR/ansible/symlinks.yml" --extra-vars="root=$REPO_DIR"

# Stow the scripts folder
stow -t ~/.local/bin scripts
```

## Install Dependencies [2/2]

We can continue installing additional dependencies. First, let's install `nodejs`, `rust`, and `go`.

**NodeJS:**

```bash
source ~/.config/repos/lukechilds/zsh-nvm/zsh-nvm.plugin.zsh
nvm install 16 --default
nvm install 18
```

**Go:**

Follow the instructions [here](https://go.dev/doc/install). Then add `/usr/local/go/bin` to the path.

```bash
export PATH=/usr/local/go/bin:$PATH
```

**Rust:**

Follow the instructions [here](https://www.rust-lang.org/tools/install). Then run:

```bash
export PATH=$HOME/.cargo/bin:$PATH
source "$HOME/.cargo/env"
```

And run additional tasks.

```bash
ansible-playbook "$REPO_DIR/ansible/npm.yml" --extra-vars="root=$REPO_DIR"
ansible-playbook "$REPO_DIR/ansible/go.yml" --extra-vars="root=$REPO_DIR"
ansible-playbook "$REPO_DIR/ansible/cargo.yml" --extra-vars="root=$REPO_DIR"
ansible-playbook "$REPO_DIR/ansible/tasks.yml" --extra-vars="root=$REPO_DIR"
```

At this point you should be able to open a new `zsh` session with no errors and fully configured.

## Font Configuration

We love to use [`CartographCF`](https://connary.com/cartograph.html) as my main terminal font.
Unfortunately, it doesn't come with a NERDFont variant, so I had to create my own. I won't provide
a link to it given that its not a free font.

You can buy it and follow the steps here to create your own:

https://github.com/ryanoasis/nerd-fonts#option-9-patch-your-own-font

## Alacritty Configuration

Once `zsh` is configured with all the other `dotfiles` and you've finished installing the `dependencies`
and your fonts, you can restart `alacritty` to see the new configuration taking effect.

## Nvim Configuration

The first time you open `nvim` it will show a bunch of errors. This is because we need to install all
the plugins. Open `nvim` and run `:PlugInstall` to install them. Once it finishes restart `nvim`.
Everything should work as expected after the restart.

Reset `nvim` to see the new plugins in action.

## Tinker Tool

Consider using [Tinker Tool](http://www.bresink.com/osx/TinkerToolOverview.html) to change the behavior of
the finder and dock application.

## Yabai and Skhd

Lastly, install [`yabai`](https://github.com/koekeishiya/yabai)
and [`skhd`](https://github.com/koekeishiya/skhd) by following their respective guides.
