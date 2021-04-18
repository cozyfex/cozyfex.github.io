---
title: "Django Create Models From Exist Database"

date: 2018-09-21T17:23:00+0900

author: CozyFex

categories:

- django
- python

tags:

- django
- python
- modeling
- database

keywords:

- django
- python
- modeling
- database

---

## Command

```shell
python manage.py inspectdb > models.py
```

## Precautions / 주의사항

You need to change something in manually.\
For example, you have to modify
<span style="color: white">**max_length**</span> in
<span style="color: white">**CharField**</span> from
<span style="color: white">**-1**</span> to length that you want.
<br/><br/>
몇가지는 수작업으로 수정해야 합니다.\
예를들어, <span style="color: white">**CharField**</span>의 <span style="color: white">**max_length**</span>
가 <span style="color: white">**-1**</span>로 나오는데, 이부분은 수작업으로 수정해 주어야 합니다.


