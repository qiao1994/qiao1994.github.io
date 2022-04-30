# Laravel备注

本文记录一些文档中描述可能不够清晰，但是开发过程中需要用到的内容
## resource方法
```
Route::resource('users', 'UsersController');
```
上述代码将等同于：（当需要生成url时，可参考下述name）
```
Route::get('/users', 'UsersController@index')->name('users.index');
Route::get('/users/{user}', 'UsersController@show')->name('users.show');
Route::get('/users/create', 'UsersController@create')->name('users.create');
Route::post('/users', 'UsersController@store')->name('users.store');
Route::get('/users/{user}/edit', 'UsersController@edit')->name('users.edit');
Route::patch('/users/{user}', 'UsersController@update')->name('users.update');
Route::delete('/users/{user}', 'UsersController@destroy')->name('users.destroy');
```

## 修改$request参数
```
$request->merge(['foo' => 'bar', ....]);
$request->replace([..])// 替换所有输入
$request['foo'] = 'bar';
unset($request['foo']) // 移除某参数
$request->offsetSet(key,value);
$request->attritube->set($key, $value)
```
上述操作方式对于普通内容都有效，但是对于file类型的参数无效

