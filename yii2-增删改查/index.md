# YII2 增删改查

# 增

```
$customer = new Customer();
$customer->name = 'Qiang';
$customer->save();
```

# 删
```
$customer = Customer::findOne(123);
$customer->delete();
```

```
Customer::deleteAll(['status' => Customer::STATUS_INACTIVE]);
```

# 改
```
$customer = Customer::findOne(123);
$customer->email = 'james@newexample.com';
$customer->save();
```

```
Customer::updateAll(['status' => Customer::STATUS_ACTIVE], ['like', 'email', '@example.com']);
```

# 查
```
$customer = Customer::find()
    ->where(['like', 'remark', 'test'])
    ->one();
    
$customers = Customer::find()
    ->where(['status' => Customer::STATUS_ACTIVE])
    ->orderBy('id')
    ->all();
```

```
$customer = Customer::findOne(123);
$customers = Customer::findAll([100, 101, 123, 124]);
```

## 查询指定列的数组
```
User::find()->select('name,id')->column(); //column()会返回第一个列的数组,这行代码将会返回由每一行的列组成的数组['bob', 'lily']
```
