
本项目就是由GitBook搭建而成

开发之前，安装好npm，知道Git和MarkDown的知识，不知道百度学习一下。

1. `sudo npm install gitbook -g` 全局或局部安装gitbook 不加-g就是局部
2. `sudo npm install gitbook-cli -g` 同上，这个用来build和本地预览
3. `gitbook init` 初始化生成README和SUMMARY
4. 写MarkDown
5. `gitbook serve` 打开输出的url，localhost:xxxx来预览
6. `gitbook build` 自动构建，默认输出的html在_book文件夹,放到可以访问的地方