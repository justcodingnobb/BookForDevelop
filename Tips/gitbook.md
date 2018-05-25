
本项目就是由GitBook搭建而成

开发之前，安装好npm，知道Git和MarkDown的知识，不知道百度学习一下。

* 全局或局部安装gitbook 不加-g就是局部

        sudo npm install gitbook -g 
    
* 同上，这个用来build和本地预览
    
         sudo npm install gitbook-cli -g 
    
* 初始化生成README和SUMMARY

          gitbook init 
    
* 写MarkDown

    [语法参考](https://www.appinn.com/markdown/)

* 本地预览，localhost:4000来预览

        gitbook serve 
    
* 自动构建，默认输出的html在_book文件夹

        gitbook build 
