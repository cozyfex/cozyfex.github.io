# Django Create Models From Exist Database


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



