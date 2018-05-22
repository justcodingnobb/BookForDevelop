### Composer

1.安装 (确保已经安装PHP，php -v可以查看版本号)
  * `php -r "copy('https://install.phpcomposer.com/installer', 'composer-setup.php');"`
  
  * `php composer-setup.php`
  
  * `php -r "unlink('composer-setup.php');"`
  
  * 使用 `composer -v` 可以查看composer的版本号为安装成功
  
2.使用中国镜像（某些原因你懂的）
  * 全局使用：`composer config -g repo.packagist composer https://packagist.phpcomposer.com`
  
  * 当前项目使用: 在你要使用的项目目录下，执行 `composer config repo.packagist composer https://packagist.phpcomposer.com`
  
3.配置文档   

  [参考文档URL](http://docs.phpcomposer.com/04-schema.html)
  
  ```json
  {
   "name": "BookForDevelop", //名称 
   "version": "1.0.0", //版本号 它应该符合 "X.Y.Z" 或者 "vX.Y.Z" 的形式， -dev、-patch、-alpha、-beta 或 -RC 这些后缀是可选的   
   "type": "project", //类型 project/library/metapackage/composer-plugin
   "keywords": "string", //该包相关的关键词的数组,可用于搜索和过滤。（造轮子推广必填）
   "homepage": "https://rekkles.xyz", //轮子主页URL
   "time": "2018-05-22 14:37:34",//发布时间 必须符合 `YYYY-MM-DD` 或 `YYYY-MM-DD HH:MM:SS` 格式 
   "license": "MIT", //授权许可    
   "authors": [
           {
               "name": "Rekkles",
               "email": "yinxiangnet@qq.com",
               "homepage": "https://rekkles.xyz",
               "role": "Developer"
           }
       ],       
   "autoload": { //每次修改之后需要 composer dump-autoLoad 重新加载
           "psr-0": { //遵循PSR-0的自动加载目录
                       "Monolog\\": "src/",
                       "Vendor\\Namespace\\": "src/",
                       "Vendor_Namespace_": "src/"
                   },
           "psr-4": { //遵循PSR-4的自动加载目录
               "Monolog\\": "src/",
               "Vendor\\Namespace\\": ""
           },
           "classmap": ["src/", "lib/", "Something.php"],
           "files": ["src/MyLibrary/functions.php"]
       },     
   "autoload-dev": { //开发模式下启用
           "psr-4": { "MyLibrary\\Tests\\": "tests/" }
       }       
   "minimum-stability": "stable", //默认安装包的稳定性 可用的稳定性标识（按字母排序）：dev、alpha、beta、RC、stable。
   "prefer-stable": true, //是否优先使用稳定版本
   "support": {
           "email": "support@example.org",
           "irc": "irc://irc.freenode.org/composer"
       },
   "require": { //依赖包
           "monolog/monolog": "1.0.*@beta",
           "acme/foo": "@dev"
       },
   "suggest": { //建议安装的包
           "monolog/monolog": "Allows more advanced logging of the application flow"
       },           
  }
  ```
  
4.小知识
  * 安装新包 `composer require xxxx`
  
  * 使用dev版 -dev 
  
  * 查看安装过程日志 -vvv 
  