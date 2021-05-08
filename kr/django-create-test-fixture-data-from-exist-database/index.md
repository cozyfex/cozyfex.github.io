# Django 기존 데이터베이스로 테스트 데이터 생성하기


## Command

```shell
python manage.py dumpdata --exclude contenttypes --exclude auth.permission --exclude authtoken.token -o test_data.json
```

