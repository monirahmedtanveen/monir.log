---
published: true
---
##### Introduction

This is an application which have laravel backend and jQuery frontend. In this application I solved the CORS(Cross Origin Resource Sharing) headers error while sharing data between frontend and backend. I used <a href="https://github.com/spatie/laravel-cors">spatie/laravel-cors</a> Package in backend app to solve the CORS headers issue.

##### Creating Backend Api

In this section I will create an api called "users" which will fetch all the user from the database. For creating this api at first install a freash laravel project into a folder called "laravel-cors".

```php
composer create-project --prefer-dist laravel/laravel backend
``` 

After installation, configure the .env file. Change the following lines of .env

```php
DB_DATABASE=your_database
DB_USERNAME=your_database_username
DB_PASSWORD=your_database_password
```

Change the database/migrations/2014_10_12_000000_create_users_table.php migration file

```php
<?php

use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateUsersTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            $table->string('email')->unique();
            $table->string('password');
            $table->string('mobile')->nullable();
            $table->string('address')->nullable();
            $table->rememberToken();
            $table->integer('created_at');
            $table->integer('updated_at');
            $table->integer('deleted_at')->nullable();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('users');
    }
}
```

Before migration it is need to add bellow statement on boot method of app\Providers\AppServiceProvider

```php
Schema::defaultStringLength(191);
```

Then migrate this table using following command

```php
php artisan migrate
```

It will create a "users" table in your database. Now after creating the database tables, let us create a responser trait. Create a file called 'ApiResponser.php' in 'app\Traits' folder. Bellow is the full code of ApiResponser.php file

```php
<?php

namespace App\Traits;

use Illuminate\Support\Collection;
use Illuminate\Database\Eloquent\Model;


trait ApiResponser {
    protected function showAll (Collection $collection, $code = 200) {
        return $this->successResponse(['data' => $collection], $code);
    }
}
```

After that create a controller named 'ApiController' in 'app\Http\Controllers\Api\V100' folder. This controller will extend the 'app\Http\Controllers\Controller' class and use the 'app\Traits\ApiResponser' trait. Below is the full code of ApiController class

```php
<?php

namespace App\Http\Controllers\Api\V100;

use Illuminate\Http\Request;
use App\Http\Controllers\Controller;
use App\Traits\ApiResponser;

class ApiController extends Controller
{
    use ApiResponser;
}
```

Now create a controller named 'UserController' in 'app\Http\Controllers\Api\V100\user' folder. UserController will extend the 'app\Http\Controllers\Api\V100\ApiController' class. Next create index function inside the UserController which will fetch all the user data from the users table of database. Here is the code of UserController

```php
<?php

namespace App\Http\Controllers\Api\V100\user;

use App\User;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;
use App\Http\Controllers\Api\V100\ApiController;

class UserController extends ApiController
{
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        $users = User::get();

        return $this->showAll($users);
    }
}
```