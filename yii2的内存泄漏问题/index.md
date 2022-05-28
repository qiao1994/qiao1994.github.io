# Yii2的内存泄漏问题


# 背景

有一段用Yii2的command导入大量数据的脚本，内存会一直上涨。（好的，我知道这个场景不应该用PHP，当时并不知道这东西能走多远，所以基于已有逻辑用PHP快速试错了）

日常导入（每天导入一天的数据）时低并发的速度可以接受，内存泄漏问题也可以忍受；但是，由于依赖方的问题（是的，一切锅都别人的），连续几天的数据异常，导入被耽搁，事后需要高并发快速补导几天的数据，这时候高并发下内存泄漏带来的频繁崩溃导致我的数据报告没法及时交差，于是不得不解决下。（重度拖延暴露了）

# 粗暴解决

迫于居家办公日会的压力， 我不得不尝试在不定位问题的情况下马上解决问题，于是使用最先进的瞎猫撞耗子方式尝试解决。

搜索得到可能能解决Yii2内存上涨的情况如下：

## 日志刷盘延迟

原因：Yii2日志在内存中积累满某个数值时，才会刷到磁盘上

解法：

* 设置刷盘间隔：`Yii::getLogger()->flushInterval = 1;`
* 主动触发刷盘：`Yii::getLogger()->flush();`

参考：

* https://www.yiichina.com/doc/api/2.0/yii-log-logger
* https://www.jb51.net/article/175488.htm

很遗憾，我的代码并不是这个问题

## 主动强制收集垃圾

`gc_collect_cycles();`

也很遗憾，不解决问题

##主动设置变量为null

`$test = null;`

还是很遗憾，不解决问题

## 放弃使用Behavior

原因：Behavior注册时会把模型本身`$this`传入，造成循环引用，导致内存无法被回收

解法：我的代码使用Behavior只用来做时间戳更新了（创建时间、更新时间），直接粗暴注释掉了

参考：https://www.yiichina.com/tutorial/1397

这个不遗憾，成功了，但没完全成功，依然会有少量泄漏，只是泄漏的幅度小了一点。

# 快速定位&临时解决

粗暴解决了一部分问题，剩下的就不得不定位一下问题了，于是我采用不那么先进的控制变量+二分定位BUG的方式快速找到了残留的表面原因。

原因：调用ActiveRecord的`save()`时，就会有内存上涨的问题，使用createCommand的方式直接insert，就没问题

解法：把ActiveRecord的`save()`替换成createCommand后insert

参考：啥都没参考

# 根因定位

周五晚上大家都不干活了，也就没人找我的麻烦了，终于可以定位下具体原因了

## 建立一个简单的测试模型

因为我们的业务模型本身有一个公用父类，有一些通用能力，且有很多方法和事件勾子，为了避免这些问题，建立一个简单的只继承ActiveRecord的简单模型并显示的关闭所有Behavior，发现不会有内存泄漏的问题。

```php
<?php
namespace app\models;

use Yii;
use yii\db\ActiveRecord;

class Test extends ActiveRecord {
    public function behaviors() {
        return [];
    }
}
```

## 使用与业务模型相同的方式生成一个测试模型

业务模型都是从Gii的扩展版工具生成出来的（我叫它GiiPlus），这里也用同样的方式生成一个模型，同样显示关闭Behavior。果然有问题了，我的心情很沉重，因为GiiPlus是我的作品，这么看大概率是它的问题了。

```php
<?php

namespace app\models;

use Yii;
use yii\gii\GiiPlusModel;

/**
 * This is the model class for table "test".
 *
 * @property int $id
 * @property string $name
 * @property int $created_at
 * @property int $updated_at
 */
class Test extends GiiPlusModel {
    public function behaviors() {
        return [
        ];
    }

    /**
     * {@inheritdoc}
     */
    public function rules() {
        return [
            [['name', 'created_at', 'updated_at'], 'required'],
            [['name'], 'string'],
            [['created_at', 'updated_at'], 'filter', 'filter' => [$this, 'trans2Timestamp']],
        ];
    }
}
```

继续用控制变量+二分定位BUG的方式定位问题，发现问题出在rules中的filter上。

好了，开始看代码

1. rules在哪儿被用到：`yii2/base/Model.php::createValidators()`，我的配置方式对应的代码内容是：

   ```php
   $validator = Validator::createValidator($rule[1], $this, (array) $rule[0], array_slice($rule, 2)); // 这里$this已经被作为第二个参数传入了
   ```

2. `Validator::createValidator()`做了啥：拿到传入的`$this(最初的Test对象)`，并构造了一个`$params`，调用了`Yii::createObject($params)`，其中`$this(最初的Test对象)`被放到了`$params['method']`中。

3. `Yii::createObject($params)`做了啥：这个场景下，调用了`static::$container->get($class, $params, $type)`，此时`$this(最初的Test对象)`在`$type['method']`中。

4. `static::$container->get($class, $params, $type)`做了啥：这个场景下，调用了`$this->build($class, $params, $config)`,此时`$this(最初的Test对象)`在`$config['method']`中。

5. `$this->build($class, $params, $config)`做了啥：这个场景下，执行`$config = $this->resolveDependencies($config);`并执行`$reflection->newInstanceArgs($dependencies)`

6. 到这已经有点想吐了，喝口水以后发发现`Yii::createObject($params)`可能就是根据`$params`创建了一个对象，我不应该继续往底层查，退回到第二步的位置，看看`Validator::createValidator()`的返回值被用来做啥了。原来它被赋值给了`$this->_validators`，把`$this->_validators`打印出来看下，果然，它长这样：

   ```php
   object(ArrayObject)#49 (1) {
     ["storage":"ArrayObject":private]=>
     array(3) {
       ...
       [2]=>
       object(yii\validators\FilterValidator)#61 (15) {
         ["filter"]=>
         array(2) {
           [0]=>
           object(app\models\Test)#31 (10) {
             ["_attributes":"yii\db\BaseActiveRecord":private]=>
             array(4) {
               ["name"]=>
               string(4) "test"
               ["created_at"]=>
               int(1653659102)
               ["updated_at"]=>
               int(1653659102)
               ["id"]=>
               int(511)
             }
           ...
   ```

   引用了他自己，所以问题发生在rules中，使用内置filter时，不能把`$this`的方法作为function传入，这会导致循环引用，进而导致垃圾无法被回收，内存上涨。

# 解决方案

这个场景下的filter其实是一个默认函数，写代码的时候为了方便就直接作为基类的一个成员函数了，转成一个全局函数即可。

