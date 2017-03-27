# Installing golang 1.8 in CentOS 7

Step 1: Go to https://golang.org/dl/ and download binaries. Example:

```
cd /tmp
wget https://storage.googleapis.com/golang/go1.8.linux-amd64.tar.gz
```

Step 2: Uncompress in /usr/local/go 
``` 
cd /tmp
tar -C /usr/local -xzf go1.8.linux-amd64.tar.gz
```

Step 3: Create global profile 

```
cat <<'EOF' >> /etc/profile.d/golang.sh
export PATH=$PATH:/usr/local/go/bin
export GOROOT=/usr/local/go
EOF
```

Step 4: Create environment vars

```
mkdir -p ~/projects/go/{bin,pkg,src}
vi ~/.bash_profile
```

```
export GOBIN="$HOME/projects/go/bin"
export GOPATH="$HOME/projects/go"
export PATH=$PATH:$GOPATH/bin
```
Step 5: packages path

```
mkdir -p $GOPATH/src/github.com/user
mkdir $GOPATH/src/github.com/user/ola
```

Step 6: Script
```
vim $GOPATH/src/github.com/user/ola/ola.go 

```
package main

import "fmt"

func main() {
	fmt.Printf("Olá, mundo.\n")
}
```
go install github.com/user/ola
```
