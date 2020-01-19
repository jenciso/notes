## Install Gnome Theme 

	sudo apt-get install dconf-cli uuid-runtime
	bash -c  "$(wget -qO- https://git.io/vQgMr)"

Select option 58

Source https://github.com/Mayccoll/Gogh

## Installing GIF animated recording software

```
sudo add-apt-repository ppa:peek-developers/daily
sudo apt-get update
sudo apt-get install peek
```

## Install Icon theme

	sudo add-apt-repository ppa:papirus/papirus
	sudo apt-get update
	sudo apt-get install papirus-icon-theme

Source https://snwh.org/paper/download

## Install unity 

    sudo apt update
    sudo apt install ubuntu-unity-desktop
    sudo apt install screenfetch
    screenfetch
    unity --version
    sudo apt install unity-tweak-tool

## Install theme

	cd /usr/share/themes/ && git clone https://github.com/jenciso/Numix.git

## Fonts for tmux

	sudo apt-get install fonts-powerline

## cat highlight


	sudo apt install python-pygments
	alias ccat='pygmentize -g'

	sudo apt-get install  highlight
	alias cats='highlight -O ansi --force'

## termtosvg

Install 
```
sudo apt install python3-pip
pip3 install termtosvg
```

Using a template like gruvbox
```
mkdir ~/templates
wget https://raw.githubusercontent.com/nbedos/termtosvg/develop/termtosvg/data/templates/window_frame_js.svg -O ~/templates/window_frame_js.svg
sed -i "s/#1c1c1c;/#282828;/g" ~/templates/window_frame_js.svg

wget https://raw.githubusercontent.com/nbedos/termtosvg/develop/termtosvg/data/templates/window_frame_powershell.svg -O ~/templates/window_frame_powershell.svg

.background {fill: #282828;}
```
