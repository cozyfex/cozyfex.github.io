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

## Remove Package

```shell
apt remove apache2
```

