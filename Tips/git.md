### git

不喜欢命令行推荐使用 [SourceTree](https://www.sourcetreeapp.com/)

   安装

    brew install git
  
   查看版本有输出就安装成功了
  
    git --version
  
   丢弃本地修改 （不可找回）
  
    git reset --hard
  
   查看远程地址
  
    git remote -v
  
   添加远程地址
  
    git remove add origin URL
  
   有冲突无法拉取最新代码解决办法
  
    git stash  --暂存
  
    git pull  --拉取
  
    git stash pop --恢复暂存 已成功拉取最新代码
  
   新建分支并且切换到新分支
  
    git checkout -b XXXX
  
   删除分支(软删除和硬删除)
  
    git branch -d XXXX 小写d换成大写D就是硬删除
  
   当前分支合并到其他分支
  
    git merge XXXX
  
   推送到远程分支
  
    git push origin master
  
  
  