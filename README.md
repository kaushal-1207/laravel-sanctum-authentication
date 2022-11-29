# Laravel Sanctum 
What is Laravel Sanctum ?
Laravel Sanctum provides a featherweight authentication system for SPAs (single page applications), mobile applications, and simple, token based APIs. Sanctum allows each user of your application to generate multiple API tokens for their account. These tokens may be granted abilities / scopes which specify which actions the tokens are allowed to perform..

### You have to just follow a few steps to get following web services
##### Login API
##### Details API




## Getting Started
## Step 1: Setup database in .env file

```javascript
DB_DATABASE=DB_NAME
DB_USERNAME=DB_USERNAME
DB_PASSWORD=DB_PASSWORD
````

## Step 2: Install Laravel Sanctum.

```javascript 
composer require laravel/sanctum
````

## Step 3: Publish the Sanctum configuration and migration files .

```javascript
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
````

## Step 4: Run your database migrations.

```javascript
php artisan migrate
````

## Step 5: Add the Sanctum's middleware.

- Add Below Line in app/Kernel.php

```javascript
use Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful;
````

- Add Below Code in api Group Route in Kernel.php

```javascript
'api' => [
            EnsureFrontendRequestsAreStateful::class,
            'throttle:60,1',
            \Illuminate\Routing\Middleware\SubstituteBindings::class,
         ],
````

## Step 6: To use tokens for users.

- Add Below Code in Already Existing Model Named User

```javascript
use Laravel\Sanctum\HasApiTokens;
class User extends Authenticatable
{
    use HasApiTokens, Notifiable;
}
````

## Step 7: Let's create the seeder for the User model

```javascript 
php artisan make:seeder UsersTableSeeder
````

## Step 8: Now let's insert as record

```javascript 
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Hash;
...
...
DB::table('users')->insert([
    'name' => 'John Doe',
    'email' => 'john@doe.com',
    'password' => Hash::make('password')
]);
````

## Step 9: To seed users table with user

```javascript 
php artisan db:seed --class=UsersTableSeeder
````


## Step 10: Create a controller & login route in the routes/api.php file

- Add Below Lines of Code in your Controller

```javascript 
<?php
namespace App\Http\Controllers;
use Illuminate\Http\Request;
use App\User;
use Illuminate\Support\Facades\Hash;
class UserController extends Controller
{
    // 
    function index(Request $request)
    {
        $user= User::where('email', $request->email)->first();
        // print_r($data);
            if (!$user || !Hash::check($request->password, $user->password)) {
                return response([
                    'message' => ['These credentials do not match our records.']
                ], 404);
            }
        
             $token = $user->createToken('my-app-token')->plainTextToken;
        
            $response = [
                'user' => $user,
                'token' => $token
            ];
        
             return response($response, 201);
    }
}
````
## Step 11: Test with postman, Result will be below

```javascript 
{
    "user": {
        "id": 1,
        "name": "Kaushal Parekh",
        "email": "kkaushal8511@gmail.com",
        "email_verified_at": null,
        "created_at": null,
        "updated_at": null
    },
    "token": "AbQzDgXa..."
}
````
## Step 12: Make Details API or any other with secure route  
```javascript 
Route::group(['middleware' => 'auth:sanctum'], function(){
    //All secure URL's
    });
Route::post("login",[UserController::class,'index']);
````
- Secure All URL by Putting it into Sanctum Authentication Middleware Route

```javascript
Route::group(['middleware' => 'auth:sanctum'], function () {
	Route::get('devicedata', [APIController::class, 'getDeviceData']);
});
````
- Now Pass Recieved Token as a Header in Postman

![No Image](https://github.com/kaushal-1207/laravel-sanctum-authentication/blob/main/sanctum_api_auth.png)

> Login/Signup API is not be Secure
