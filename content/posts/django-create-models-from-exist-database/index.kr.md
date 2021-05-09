---
title: "Django 기존 데이터베이스로 모델 생성"

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

## 주의사항

몇가지는 수작업으로 수정해야 합니다.\
예를들어, <span style="color: white">**CharField**</span>의 <span style="color: white">**max_length**</span>
가 <span style="color: white">**-1**</span>로 나오는데, 이부분은 수작업으로 수정해 주어야 합니다.


