# Solo.io Demo Laptop macOS Setup
_forked from https://github.com/ahmetb/dotfiles_

## OS Settings

> TODO: find `defaults write` commands for these.

- Remove useless items from the Dock.
- Drag `Downloads` folder next to the Trash on the Dock.
  - Right Click &rarr; Sort by Date Added.
- Move $HOME folder to Finder sidebar.

- Set shortcuts
  - Accessibility: Invert colors: Cmd+Shift+I
  - Screenshots: Save selected area to file: Cmd+Shift+4
  - Screenshots: Save selected area to clipboard: Cmd+Shift+3
  - Screenshots: Uncheck others

Tweaks:

```sh
defaults write -g NSAutomaticSpellingCorrectionEnabled -bool false # Turn off autocorrect

defaults write com.apple.finder ShowPathbar -bool true # Show Finder Path Bar

defaults write com.apple.dock.plist wvous-tl-corner -int 10 # Hot Corner Top Left: Put Display to Sleep

defaults write NSGlobalDomain AppleShowScrollBars -string "Always" # Show scrollbar always
defaults write com.apple.finder AppleShowAllFiles true   # Show hidden files
defaults write com.apple.finder ShowStatusBar -bool true # Show Finder statusbar

# Default Finder location is the home folder
defaults write com.apple.finder NewWindowTarget -string "PfLo" && \
  defaults write com.apple.finder NewWindowTargetPath -string "file://${HOME}"

chflags nohidden ~/Library                               # Unhide ~/Library

# Disable smart quotes and dashes
defaults write 'Apple Global Domain' NSAutomaticDashSubstitutionEnabled 0
defaults write 'Apple Global Domain' NSAutomaticQuoteSubstitutionEnabled 0
defaults write 'Apple Global Domain' NSAutomaticPeriodSubstitutionEnabled 0
```

## Shell

Install oh-my-zsh: https://github.com/robbyrussell/oh-my-zsh

```sh
git clone https://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
chsh -s /bin/zsh
```

## Package manager

- Run `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`
- Run `which brew` to confirm the one in home directory is picked up.
- Run `brew analytics off`

## Installing software via Homebrew

All software installed on the system must be listed in `.Brewfile`. This is
symlinked at `~/.Brewfile` and used by `brew bundle`.

To install all the software, add it to `.Brewfile` and run:

    brew bundle --global

Some stuff will take long, in that case, identify which packages and update
`.Brewfile` to install them with `args: ['force-bottle']` or do a one-off
`brew install --force-bottle [pkg]` install.

Some things that require manual installation after Homebrew:

```sh
# if pip requires sudo, something is wrong, because the
# Homebrew bundle should install a $USER-writable over system-python.

pip install virtualenv
pip install virtualenvwrapper
```

## Post-Installation Configuration

- **Spectacle**
  - Security->Accessibility: Give access
  - Launch at Login

- **fzf** completion scripts:

      "$HOMEBREW"/opt/fzf/install

- **minikube** xhyve driver:

      # minikube uses xhyve, which requires privileged access to the hypervisor
      sudo chown root:wheel /usr/local/opt/docker-machine-driver-hyperkit/bin/docker-machine-driver-hyperkit
      sudo chmod u+s /usr/local/opt/docker-machine-driver-hyperkit/bin/docker-machine-driver-hyperkit

## Settings Sync

- Clone this repo and run `install_symlinks.sh`
    - Open a new terminal to take effect.
- iTerm2->Preferences->Load Preferences From: com.googlecode.iterm2.plist directory.
    - Restart iTerm2.

- VSCode:
  - Install "Settings Sync" extension and reload.
  - Run '> Sync: Download Settings'
  - Enter gist ID in `vscode.sync` file to prompt.
  - Once extensions are installed 'Reload' (or Restart)
  - Run '> Sync: Update/Upload Settings'
  - Create a token with 'gist' permissions and save it to the prompt
  - Wait for the Sync Summary.

## Git Setup

Run:

    ./git_setup.sh

Generate key with a password:

    ssh-keygen -f $HOME/.ssh/github_rsa

(You may want to redact hostname from the public key.)

Add key to the keychain:

    ssh-add $HOME/.ssh/github_rsa          # company-installed
    /usr/bin/ssh-add $HOME/.ssh/github_rsa # system

Upload the key to GitHub. https://github.com/settings/keys

Save this to ~/.ssh/config:

Test connection:

    ssh -T git@github.com -i ~/.ssh/github_rsa
