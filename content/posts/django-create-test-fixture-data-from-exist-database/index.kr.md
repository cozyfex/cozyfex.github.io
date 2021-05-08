---
title: "Django 기존 데이터베이스로 테스트 데이터 생성하기"

date: 2021-04-08T21:57:23+0900

author: CozyFex

categories:

- django
- python
- tdd

tags:

- django
- python
- database
- fixture
- test
- tdd

keywords:

- django
- python
- database
- fixture
- test
- tdd

---

## Command

```shell
python manage.py dumpdata --exclude contenttypes --exclude auth.permission --exclude authtoken.token -o test_data.json
```
