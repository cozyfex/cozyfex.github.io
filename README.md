# Hugo Blog

## Usage

```shell
hugo new posts/blog-title/index.kr.md
```

## Windows Settings

### Installing Chocolatey

1. Run administrative shell
2. Run below command

```shell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

[Chocolatey Official Install Link](https://chocolatey.org/install)

### Install Go

Go to [Go Official](https://go.dev)

### Install Dart Sass

```shell
choco install sass
```

### Install Hugo

```shell
choco install hugo-extended
```