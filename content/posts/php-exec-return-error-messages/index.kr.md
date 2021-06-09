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
이 함수는 PHP 에서 OS 명령을 실행하기 위한 것이다.  
{{</admonition>}}

## `exec` Error

```php
exec('none-exist-command', $output, $exit);
```

{{<admonition note Imporant true>}}  
위의 `none-exist-command` 는 존재하지 않는 명령어 이므로 에러를 발생시킨다.  
IMPORTANT!!  
`$output` 으로부터 에러 메시지를 얻을 수 있다고 생각 할 수 있다. 그러나 그렇지 않다!!  
{{</admonition>}}

## Return Error Messages

```php
exec('none-exist-command 2>&1', $output, $exit);
```

{{<admonition note "2>&1" true>}}  
에러 메시지를 얻고 싶다면, `2>@1` 옵션을 명령어 끝에 추가한다.  
{{</admonition>}}
