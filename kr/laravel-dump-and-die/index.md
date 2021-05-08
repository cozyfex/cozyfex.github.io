# Laravel Dump and Die


## Laravel Dump and Die

`dd` 키워드는 객체의 상세정보를 보기 위한 함수이다.

```php
namespace App\Http\Contollers\Auth;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;

class LoginController extends Controller {
    public function store(Request $request) {
        dd($request);
    }
}
```
