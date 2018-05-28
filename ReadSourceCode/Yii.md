### 起步

 **安装 （这里用的是Yii2 2.0版本，以后可能会有变化，但是思路不变）**
 
     composer create-project --prefer-dist yiisoft/yii2-app-basic basic
     
 > 高级版需要进行初始化，使用 php init 初始化就好    
     
 **运行**
 
    php -S localhost:9001 -t .
    
 > php -S 是php自带的内置服务器，可以用于本地测试
 
 **查看**
 
    localhost:9001/web/index.php 
    
 **添加自定义控制器和视图**
 
   在Controller里面修改SiteController，添加以下内容
   
   ```php
   <?php
   //yii的action统一以action开头+名字
   public function actionSay(){
           //渲染view say
           return $this->render('say', ['message' => 'hello world']);
   }   
   ```   
   
   在Yii2/Views/site里面添加Say.php
   
   ```php
    <?php
    use yii\helpers\Html;
    ?>
    <?= Html::encode($message) ?>
   ```   
   
   **查看效果**
   
        http://localhost:9001/web/index.php?r=site/say&message=HelloWorld
   
   *r代表路由，选择Controller和Action，message是路由参数* 
        
   可以看到输出了HelloWorld！而且还使用了模板
   
   **解读**
   
   >tip:PHP一般解读都是从 `index.php` 开始解读，没有就找对应框架的文件，例如Yii文件
   
   Yii的生命周期如下：
   
   1.用户向入口脚本 web/index.php 发起请求。
    
   2.入口脚本加载应用配置并创建一个应用实例去处理请求。 
   
   3.应用通过请求组件解析请求的路由。 
   
   4.应用创建一个控制器实例去处理请求。
    
   5.控制器创建一个操作实例并针对操作执行过滤器。
   
   6.如果任何一个过滤器返回失败，则操作退出。 
   
   7.如果所有过滤器都通过，操作将被执行。 
   
   8.操作会加载一个数据模型，或许是来自数据库。
   
   9.操作会渲染一个视图，把数据模型提供给它。 
   
   10.渲染结果返回给响应组件。 
   
   11.响应组件发送渲染结果给用户浏览器
   
   
   **打开Yii2/web/index.php**
   
   ```php
   <?php
   
   // comment out the following two lines when deployed to production
   defined('YII_DEBUG') or define('YII_DEBUG', true); //定义常量
   defined('YII_ENV') or define('YII_ENV', 'dev');
   
   require __DIR__ . '/../vendor/autoload.php'; //Composer自动加载
   require __DIR__ . '/../vendor/yiisoft/yii2/Yii.php'; //引入Yii核心类
   
   $config = require __DIR__ . '/../config/web.php'; //引入配置
   
   (new yii\web\Application($config))->run(); //开车!!!
   ```
   
   > 从上面的源码可以看到，前面都是定义一些常量和引入关键类库，只有最后一行才是运行核心，跑过去看
   
   **打开Yii2/vendor/yiisoft/yii2/web/Application.php(方便查看我把注释删了)**
   
```php
<?php

namespace yii\web;

use Yii;
use yii\base\InvalidRouteException;
use yii\helpers\Url;

class Application extends \yii\base\Application
{
    /*
     * 乱七八糟的一堆,没有__construct
     * */
}
```

  >是不是一脸懵逼？__construct方法呢？前面不是new的这个类吗？
  别急，看看继承类
  
  **打开 `Yii2/vendor/yiisoft/yii2/base/Application.php` 文件**
   
  ```php
  
//vendor/yiisoft/yii2/base/Application.php  
<?php 
use Yii; //use了Yii核心
//继承Module
abstract class Application extends Module
{
    //真正new的方法是这个 参数是index.php里面传递过来的
    public function __construct($config = [])
    {
        Yii::$app = $this; //Yii::$app 是一个全局静态变量 在 `baseYii.php`(基类) 里面定义， 用于存储当前请求的容器App
        static::setInstance($this); //调用父类的 SetInstance 方法,参数是当前实例

        $this->state = self::STATE_BEGIN; //用于记住当前状态

        $this->preInit($config); //加载一些配置 可略过

        $this->registerErrorHandler($config); //注册异常处理

        Component::__construct($config);//初始化组件
    }
    
    //运行程序的核心
    public function run()
        {
            try {
                $this->state = self::STATE_BEFORE_REQUEST;//记录状态
                $this->trigger(self::EVENT_BEFORE_REQUEST);//处理请求之前的操作
    
                $this->state = self::STATE_HANDLING_REQUEST;
                $response = $this->handleRequest($this->getRequest());//处理请求 找路由、拼接用户参数、分发action
    
                $this->state = self::STATE_AFTER_REQUEST;
                $this->trigger(self::EVENT_AFTER_REQUEST);//处理请求完成之后的操作
    
                $this->state = self::STATE_SENDING_RESPONSE;
                $response->send(); //开始相应用户请求
    
                $this->state = self::STATE_END; //结束状态
    
                return $response->exitStatus; //返回响应完成
            } catch (ExitException $e) {
                //发生异常统一异常处理
                $this->end($e->statusCode, isset($response) ? $response : null);
                return $e->statusCode;
            }
        }
}   
?>

vendor/yiisoft/yii2/base/Module.php
<?php
//继承ServiceLocator
class Module extends ServiceLocator
{
    public $loadedModules = [];
    //接收一个实例 作用是 注入/删除 一个实例到$app->loadedModules
    public static function setInstance($instance)
    {
       if ($instance === null) {
           //空实例就清空这个Module
           unset(Yii::$app->loadedModules[get_called_class()]);
       } else {
           //不为空则将当前实例注入到loadedModules
           Yii::$app->loadedModules[get_class($instance)] = $instance;
       }
    }
}
?>
``` 

  **剩下trigger、handleRequest、send方法**
  
  *. trigger 主要是负责实现阶段性操作，例如请求前（验证登录、路由合法）、请求后（触发dispatch之类）
  *. handleRequest 主要是负责根据request的参数负责分配路由、找到Controller和action，将参数注入到action
  *. send 发送响应，没什么好说的
   
   
      
    
