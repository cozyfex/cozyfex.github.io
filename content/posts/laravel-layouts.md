---
title: "Laravel Layouts"

date: 2021-05-05T18:29:41+09:00

author: CozyFex

categories:

- laravel

tags:

- laravel
- blade
- layout
- section
- yield
- include
- stack
- push
- prepend

keywords:

- laravel
- blade
- layout
- section
- yield
- include
- stack
- push
- prepend

---

## Laravel Blade Layouts

Laravel uses Blade Templates

### extends

`@extends('file')` is loading `file.blade.php` file.

### section/show, section/endsection, yield, parent

<small>Base File</small>

```html
@section('section-name1')
<p>This is test section.</p>
@show

@yield('section-name2', 'Default Value')
```

<small>Extend File</small>

```html
@extends('base')

@section('section-name1')

@parent <!-- Load Parent Section Content -->

<p>This is replace section-name1</p>

@parent <!-- Load Parent Section Content -->

@endsection

@section('section-name2', 'This is section 2 value')
```

### stack, push/endpush, prepend/endprepend

<small>Base File</small>

```html
@stack('scripts')
```

<small>Extend File</small>

```html
@push('scripts')
<script type="text/javascript">
    function pushFunction() {
        // This is imported after prepend.
    }
</script>
@endpush

@prepend('scripts')
<script type="text/javascript">
    function prependFunction() {
        // This is imported before push.
    }
</script>
@endprepend
```

### Layout File

<small>resources/views/layouts/base.blade.php</small>

```html
<!DOCTYPE html>
<html>
<head>
    <title>@yield('title', 'Default Title')</title>
    @stack('styles')
</head>
<body>

@include('partials.menu')

@section('content')
<p>This is layout.</p>
@show

@yield('footer')

@stack('scripts')
</body>
</html>
```

### Partial File

<small>resources/views/partials/menu.blade.php</small>

```html

<ul>
    <li>Menu1</li>
    <li>Menu2</li>
    <li>Menu3</li>
    <li>Menu4</li>
</ul>
```

### Extend File

<small>resources/views/web.blade.php</small>

```html
@extends('layouts.base')

@push('styles')
<style type="text/css">
    p {
        margin: 10px;
    }
</style>
@endpush

@pretend('styles')
<style type="text/css">
    ul {
        list-style: none;
    }
</style>
@endpretend

@section('title', 'This is a extended file')

@section('content')
@parent
<p>This is web page.</p>
@parent
@endsection

@section('footer')
<p>This is footer.</p>
@endsection

@push('scripts')
<script type="text/javascript">
    function pushScript() {

    }
</script>
@endpush

@pretend('scripts')
<script type="text/javascript">
    function pretendScript() {

    }
</script>
@endpretend
```
