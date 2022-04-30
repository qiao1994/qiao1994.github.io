# Laravel核心架构相关概念理解

# 一次请求的基本流程
* index.php
    * 加载composer生成的自动加载配置
   * 从bootstrap/app.php脚本中检索Laravel应用实例
* HTTP/Console内核
    * 初始化：配置错误处理、日志、检测环境
   * 定义中间件
   * 注册ServiceProvider
   * 请求调度：把请求传递给路由，由路由或路由中定义的控制器处理请求
如上所述，laravel通过ServiceProvider把独立插件注册到核心架构上，供后续服务调用。

# Contracts, ServiceContainer, ServiceProvider, Facades
* Contracts：接口，定义一些规则，每个实现此接口的都要实现里面的方法
* ServiceContainer：Contracts具体的逻辑实现
* ServiceProvider：ServiceContainer的服务提供者，返回ServiceContainer的实例化供其他地方使用。上文中提到的**注册ServiceProvider**就是调用方式。
* Facades：简化ServiceProvider的调用方式，而且可以静态调用ServiceContainer中的方法

## Contracts
定义接口
```
namespace App\Contracts;
interface MyFooContracts {
    public function add($a, $b)
}
```

## ServiceContainer
ServiceContainer是一个Contracts的实现，提供真正的服务
```
use App\Helper\Contracts\MyFooContracts;
namespace App\Helper;
class MyFoo implements MyFoo{
    public function add($a, $b) {
        return $a+$b;
    }
}
```

## ServiceProvider
定义一个ServiceProvider供其他地方使用ServiceContainer
本质是把ServiceContainer的服务包装起来，并提供一些接口如register()，供调用者无差别的调用不同的ServiceContainer
```
<?php
namespace App\Providers;

use App\Helper\MyFoo; //要服务的Container
use Illuminate\Support\ServiceProvider;
use App;

class MyFooServiceProvider extends ServiceProvider {
    public function boot(){}
    //注册到容器中
    public function register() {
        //可以这么绑定,这需要use App;
        App::bind("myfoo",function(){
            return new MyFoo();
        });
        //也可以这么绑定
        $this->app->bind("myfoo", function(){
            return new MyFoo();
        });
    }
}
```
如果要让laravel调用这个Service，需要在`app/config.php`中的providers数组中加入ServiceProvider,让系统自动注册
`App\Providers\MyFooServiceProvider::class,`

### 在Controller中调用
```
public function two($id=null) {
    //从系统容器中获取实例化对象
    $myfoo = App::make("myfoo");
    echo $myfoo->add(1,2);
}
```
## Facades
上述调用方法太麻烦，需要make来获取对象，为了简便，定义Facades
```
namespace App\Facades;
use Illuminate\Support\Facades\Facade;

class MyFooFacade extends Facade {
    protected static function getFacadeAccessor() {
        //这里返回的是ServiceProvider中注册时,定义的字符串
        return 'myfoo';
    }
}
```

### 使用Facades调用
```
use App\Facades\MyFooFacade;
public function two($id=null) {
    //从系统容器中获取实例化对象
    $myfoo = App::make("myfoo");
    echo $myfoo->add(1,2);
    //使用门面
    echo MyFooFacade::add(4,5);
}
```

> 综上：Contracts, ServiceContainer, ServiceProvider, Facades中，真正有用的只有ServiceContainer；Contracts是为了低耦合定义的接口；ServiceProvider是为了方便其他位置无差别的注册不同的ServiceContainer；Facades是为了更方便的调用ServiceContainer

参考&感谢：https://www.cnblogs.com/hanyouchun/p/5341264.html
