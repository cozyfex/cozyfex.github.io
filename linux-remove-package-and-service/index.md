# Linux Remove Package and Service


## Check Services

```shell
systemctl list-units --type service
```

## Stop Service

```shell
systemctl stop apache2
```

## Disable Service

```shell
systemctl disable apache2
```

## Check Installed Packages

```shell
apt list --installed
```

## Remove Package

```shell
apt remove apache2
```

