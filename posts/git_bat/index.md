# Git_bat


<!--more-->

应用场景：

搭建hugo静态博客时，只需要上传public目录下的静态资源，但是为了源文件不丢失，也想把根目录上传到另一个仓库备份。但是这样就有了两个仓库，每次推送代码需要push两次，比较麻烦

蠢人有蠢人的办法，写一个批处理脚本，在脚本中在两个目录push:

```shell
echo "Start submitting code to the local repository"
echo "The current directory is:%cd%"
git add .
echo;
 
echo "Commit the changes to the local repository"
set now=%date% %time%
echo %now%
git commit -m "%now%"
echo;
 
echo "Commit the changes to the remote git server"
git push
echo;
 
echo "Batch execution complete!"
echo;
hugo 
cd public
echo "Start submitting code to the local repository"
echo "The current directory is:%cd%"
git add .
echo;
 
echo "Commit the changes to the local repository"
set now=%date% %time%
echo %now%
git commit -m "%now%"
echo;
 
echo "Commit the changes to the remote git server"
git push
echo;
 
echo "Batch execution complete!"
echo;

echo. & pause
```

最后按任意键退出。
