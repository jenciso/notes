## Install NodeJS in Ubuntu 18

Install Node
```
curl -sL https://deb.nodesource.com/setup_10.x -o nodesource_setup.sh
sudo bash nodesource_setup.sh
sudo apt-get install gcc g++ make build-essential
sudo apt-get install -y nodejs
```

Install YARN
```shell
curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt-get update && sudo apt-get install yarn
```

Install NVM
```shell
curl -sL https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh -o install_nvm.sh
bash install_nvm.sh
source ~/.profile
nvm ls-remote
nvm install 12.17.0
nvm use 12.17.0
node -v
nvm ls
```

Define the default
```
nvm alias default 12.17.0
nvm use default
```

Install express

```
npm install -g express
```

## Reference
https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-18-04-pt
