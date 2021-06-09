---
title: "PHP exec Return Error Messages"

date: 2021-05-26T19:57:38+09:00

categories:

- php
- exec
- log

tags:

- php
- exec
- log
- error
- messages

keywords:

- php
- exec
- log
- error
- messages

---

## `exec`

{{<admonition note exec true>}}  
This function is for executing OS command in PHP.  
{{</admonition>}}

## `exec` Error

```php
exec('none-exist-command', $output, $exit);
```

{{<admonition note Imporant true>}}  
Above the command `none-exist-command` occurs an error because it's not exist command.  
IMPORTANT!!  
Maybe you think you can get error messages from the `$output`.  
BUT IT'S NOT!!  
{{</admonition>}}

## Return Error Messages

```php
exec('none-exist-command 2>&1', $output, $exit);
```

{{<admonition note "2>&1" true>}}  
If you want to get error messages, you have to append `2>@1` option the command.  
{{</admonition>}}
