
1-Step :
install GNOME : 
```sh
sudo apt install kali-desktop-gnome
#then select gdm3
```
add extension : 
```sh
sudo apt install gnome-shell-extension-manager
```

search for transparent top-bar , then install it and finally adjust the value
Add an Apple look with this  : 
https://www.gnome-look.org/p/1403328/
https://www.gnome-look.org/p/1102582/
https://www.gnome-look.org/p/1148748/
https://www.youtube.com/watch?v=ymFNDSDqFDE

- More config (obsidian) : 

```
Window frame style set to --> native frame
```

- For the system : 
```sh
sudo apt install dconf-editor
```

Navigate to `org/gnome/desktop/wm/preferences` and look for the option `button-layout`. Set it : 
```sh
close,minimize,maximize:
```

2-step:

Install flatpack : 

```sh
sudo apt install flatpak
sudo apt install gnome-software-plugin-flatpak
sudo flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```
install: backbox :
```sh
sudo flatpak install flathub com.raggesilver.BlackBox
#and add sortcut to start it with this command : 
flatpak run com.raggesilver.BlackBox
```

3-step: 

OhMyZsh : 
```sh
sudo apt install zsh

sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

then add  in .zshrc.conf file  in the plugin parameter : 
```sh
git sudo history encode64 copypath zsh-autosuggestions zsh-syntax-highlighting
```
4-step:

tmux : 
```sh
sudo apt install tmux 
echo "set -g mouse on" >> ~/.tmux.conf
```

- Or: 
https://github.com/Arszilla/kali-i3-gapsqQ