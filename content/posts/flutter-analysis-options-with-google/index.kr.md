---
title: "Flutter Analysis Options With Google"

date: 2021-04-12T22:11:00+0900

author: CozyFex

categories:

- flutter
- dart

tags:

- flutter
- dart
- pedantic
- analysis_options.yaml

keywords:

- flutter
- dart
- pedantic
- analysis_options.yaml

---

## analysis_options.yaml

이 파일은 flutter project의 루트에 위치한다.\
기본 lint에 더 상세한 설정을 추가해서 더 좋은 코드를 만든다.
<br/><br/>

## [pedantic](https://pub.dev/packages/pedantic){:target="_blank"}

구글에서 제공하는 analysis_options.yaml에 추가 가능한 lint이다.
<br/><br/>

## Install [pedantic](https://pub.dev/packages/pedantic){:target="_blank"}

<sub>`pubspec.yaml`</sub>

```yaml
# `package:pedantic/pedantic.dart` 페키지를 사용하려면, dependencies 에 추가한다
dependencies:
  pedantic: ^1.11.0

# 또는 `analysis_options.yaml` 을 사용하면,  dev_dependencies 에 추가하면 된다.
dev_dependencies:
  pedantic: ^1.11.0
```

<sub>`analysis_options.yaml`</sub>

```yaml
include: package:pedantic/analysis_options.yaml
```



