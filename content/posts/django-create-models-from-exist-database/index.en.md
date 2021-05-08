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

## Precautions

You need to change something in manually.\
For example, you have to modify
<span style="color: white">**max_length**</span> in
<span style="color: white">**CharField**</span> from
<span style="color: white">**-1**</span> to length that you want.


