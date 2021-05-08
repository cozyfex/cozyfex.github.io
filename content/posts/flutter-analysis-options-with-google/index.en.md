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
## analysis_options.yaml with Google Analysis
----

#### analysis_options.yaml

This file is flutter project root.\
It can make more good code by adding guideline to base lint.
<br/><br/>

#### [pedantic](https://pub.dev/packages/pedantic){:target="_blank"}

This is a lint that was served from Google for analysis_options.yaml.
<br/><br/>

#### Install [pedantic](https://pub.dev/packages/pedantic){:target="_blank"}

<sub>`pubspec.yaml`</sub>

```yaml
# If you use `package:pedantic/pedantic.dart`, add a normal dependency.
dependencies:
  pedantic: ^1.11.0

# Or, if you just want `analysis_options.yaml`, it can be a dev dependency.
dev_dependencies:
  pedantic: ^1.11.0
```

<sub>`analysis_options.yaml`</sub>

```yaml
include: package:pedantic/analysis_options.yaml
```



