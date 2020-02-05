> This is not yet a rock solid solution, but will get you there.

If you want to run bitaac with a older version of tibia you often want to use the accounts.id instead of accounts.name column, here is how you do it in bitaac:

```php
// First of, we obviously start of with set the account name field to id.
Bitaac::setAccountNameField('id');

// Depending on your scheme structure, set or disable creation field.
Bitaac::setAccountCreationField(false);

// Now it is time to overwrite the default bitaac register validation rules.
Bitaac::rules('Bitaac\Auth\Http\Requests\RegisterRequest', function () {
    return [
        'name' => ['required', 'digits_between:5,11', 'integer', 'unique:accounts,id'],
        'email'    => ['required', 'email', 'unique:accounts,email'],
        'password' => ['required', 'confirmed', 'min:6', 'max:255'],
        'terms'    => ['accepted'],
    ];
});


// Now we have to re-bind the account to our custom model, since we hace to disable incrementing.
$this->app->bind(Bitaac\Contracts\Account::class, \App\Models\Account::class);
$this->app->alias(Bitaac\Contracts\Account::class, 'account');



// App\Models\Account class file
namespace App\Models;

use Bitaac\Account\Models\Account as Base;

class Account extends Base
{
    /**
     * Indicates if the IDs are auto-incrementing.
     *
     * @var bool
     */
    public $incrementing = false;
}
```
