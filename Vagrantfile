# -*- mode: ruby -*-
# vi: set ft=ruby :

$zsh = <<-'SCRIPT'
  ZDIR=/home/vagrant/.oh-my-zsh
  ZSHRC=/home/vagrant/.zshrc
  CURR_SHELL=$(echo $0)
  pushd /home/vagrant
  if ! [ -e $ZDIR ]; then
    git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
  else
    echo -- $ZDIR already exists... skipping
  fi
  if ! [ -e $ZSHRC ]; then
    echo -- applying oh-my-zsh config to .zshrc
    cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
  fi
  if ! [ $(echo $0 | grep -c zsh) -gt 0 ]; then
    sudo chsh -s /bin/zsh vagrant
    sudo sed -i "s!/home/vagrant:/bin/bash!/home/vagrant:/bin/zsh!" /etc/passwd
    zsh
  else
    echo -- zsh is already the default shell
  fi
  ASDF=asdf
  if ! [[ $(grep $ASDF $ZSHRC) ]]; then
    echo -- adding asdf to zshrc
    sed -i "s/plugins=(/plugins=( $ASDF /" $ZSHRC
    if ! [ -e ~/.asdf ]; then
      echo -- downloading asdf to ~/.asdf
      git clone https://github.com/asdf-vm/asdf.git ~/.asdf
      ~/.asdf/asdf.sh
    fi
    echo -- reloading zsh
    /bin/zsh -c "source ~/.zshrc"
  else
    echo "-- asdf plugin already found in zshrc"
  fi
SCRIPT
#

$vim = <<-'SCRIPT'
  apt install -y neovim
SCRIPT

$python = <<-'SCRIPT'
  sudo apt-get install -y make build-essential libssl-dev zlib1g-dev \
  libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev \
  libncursesw5-dev xz-utils tk-dev libffi-dev liblzma-dev python-openssl
  cat <<'EOF'>run.zsh
  source ~/.zshrc
  asdf>/dev/null || echo -- asdf not found
  asdf list
  if ! [[ $(asdf list | grep python) ]]; then 
      source ~/.zshrc 
      asdf plugin-add python
      asdf install python latest
  else
    echo python from asdf already detected
  fi
"
'EOF'
  zsh run.zsh
  rm run.zsh
SCRIPT

$emacs = <<-'SCRIPT'
  apt remove emacs
  apt autoremove
  add-apt-repository ppa:kelleyk/emacs
  apt install -y emacs27 ripgrep fd-find
SCRIPT

$emacs_doom = <<-'SCRIPT'
  git clone --depth 1 https://github.com/hlissner/doom-emacs ~/.emacs.d
  ~/.emacs.d/bin/doom install
SCRIPT




Vagrant.configure("2") do |config|
  # https://docs.vagrantup.com.

  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "generic/ubuntu2004"

  config.vm.network "forwarded_port", guest: 80, host: 8080
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"


  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  #config.vm.synced_folder "./src", "/home/vagrant"

  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.memory = "4096"
    vb.cpus = 6
  end
  
  config.vm.provision "shell" do |s|
    s.inline = <<-SHELL
      sudo apt update
      sudo apt install -y git expect zsh
    SHELL
  end
  config.vm.provision "install oh-my-zsh and asdf", type:"shell", 
    privileged: false, inline: $zsh
  config.vm.provision "install neovim", type:"shell", inline: $vim
  config.vm.provision "install latest python", type:"shell", 
    privileged: false, inline: $python
  config.vm.provision "install emacs", type:"shell", inline: $emacs


end

