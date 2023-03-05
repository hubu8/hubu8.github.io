# Excel去除单元格保护


<!--more-->

### Excel撤销单元格保护

#### 知道密码

1、打开Excel表格，点击上方工具栏的【审阅】，再选择【保护工作表】或者【保护工作簿】

![img](/other/ae51f3deb48f8c54e2cea41d7e2aa8ffe0fe7ff4.jpeg@f_auto)

2、出现对话框后，输入想要设定的限制密码即可，也可以根据限制需要在下面勾选相应的选项。

![img](/other/a2cc7cd98d1001e92af7acaeee0dfee655e79751.png@f_auto)

#### 不知道密码

![image-20230305165055490](/other/image-20230305165055490.png)

![image-20230305165129726](/other/image-20230305165129726.png)

1、改变excel文件后缀名，将xlsx改为zip或者rar，并解压：

![image-20230305165232029](/other/image-20230305165232029.png)

2、修改xl/worksheets下的所有xml文件，逐个打开将sheetProtection标签删除

一个sheet.xml文件就代表一个工作空间，一个excel可以包含多个工作空间，所以包含有多个，如果只需要接触特定工作空间的单元格保护，工作空间顺序是和xml文件顺序一致的，可按需修改

![image-20230305165356909](/other/image-20230305165356909.png)

3、将去除sheetProtection标签的文件保存，重新压缩为一个压缩文件，修改文件后缀名为xlsx:

![image-20230305165836760](/other/image-20230305165836760.png)

#### 工作空间太多？

面对重复繁杂的程序化工作，我选择编程，面对文件操作我选择python，通过程序化方式将解除单元格保护的步骤写为一个python程序，最后编译为exe文件直接在电脑上运行

仓库地址：[removeProtection](https://gitee.com/hubu8/dongyuan_study/blob/master/python/removeExcelProtection/dist/removeProtection.exe)

程序截图：

![image-20230305170325410](/other/image-20230305170325410.png)
