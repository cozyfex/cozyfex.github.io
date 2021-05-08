# Laravel Dump and Die


## Laravel Dump and Die

The `dd` keyword is to show object's detail in Laravel.

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
