# SQL生成PDM文件


<!--more-->

### 基础操作设置

#### 修改name,code同步修改，操作不便

##### 版本在16.6之下

工具栏 Tools -> 常规选项 general Options -> Dialog 下 Name to Code mirroring 取消勾选

![image-20231222141450298](/common_images/image-20231222141450298.png)

##### 版本是16.6以及之上

工具栏 Tools -> 常规选项 general Options -> Respository 下 Display 取消 **Name to Code mirroring** 勾选

![在这里插入图片描述](/common_images/594c7ad4eed94cb792f2717b3b9883bb.png)

工具栏 Tools -> Modeal Options -> Naming Convention  -> Synchronize code

![在这里插入图片描述](/common_images/1ae53767bda24667bbd5898be8634f71.png)

### SQL逆向生成PDM

#### 第一步：File > Reverse Engineer > Database

![image-20231221143024791](/common_images/image-20231221143024791.png)

#### 第二步：设置DBMS参数

![image-20231221143237554](/common_images/image-20231221143237554.png)

#### 第三步：选择sql文件

![image-20231221143333237](/common_images/image-20231221143333237.png)

### 生成的PDM将coments同步至name字段

Open PDM--Tools--Execute Commands--Run Script

脚本语法参考 Microsoft VBScript 脚本语法

脚本1

```vbscript
Option   Explicit 
ValidationMode   =   True 
InteractiveMode   =   im_Batch 
Dim   mdl    
Set   mdl   =   ActiveModel 
If   (mdl   Is   Nothing)   Then 
        MsgBox   "There   is   no   current   Model" 
ElseIf   Not   mdl.IsKindOf(PdPDM.cls_Model)   Then 
        MsgBox   "The   current   model   is   not   an   Physical   Data   model." 
Else 
        ProcessFolder   mdl 
End   If 
Private   sub   ProcessFolder(folder) 
        Dim   Tab 
        for   each   Tab   in   folder.tables 
              if   not   tab.isShortcut   then
                    tab.name=tab.comment
                    Dim   col
                    for   each   col   in   tab.columns
                        col.name=col.comment 
                    next 
              end   if           
        next
end   sub
```

脚本2

```vbscript
'------------------------------------------------------------
'
'脚本功能：
'    PowerDesigner中****完成后，将数据库中comment脚本
'    赋值到PDM的name
'执行方法：
'    Open PDM--Tools--Execute Commands--Run Script
'
'------------------------------------------------------------
Option Explicit  
ValidationMode = True  
InteractiveMode = im_Batch
Dim mdl 'the current model   
'get the current active model  
Set mdl = ActiveModel  
If (mdl Is Nothing) Then  
    MsgBox "There is no current Model" 
ElseIf Not mdl.IsKindOf(PdPDM.cls_Model) Then  
    MsgBox "The current model is not an Physical Data model." 
Else  
    ProcessFolder mdl  
End If  
'------------------------------------------------------------
'This routine copy name into code for each table, each column  
'and each view of the current folder

'------------------------------------------------------------
Private sub ProcessFolder(folder)  
    Dim Tab 'running table  
    for each Tab in folder.tables  
        if not tab.isShortcut then  
            if len(tab.comment) <> 0 then  
                tab.name = tab.comment  
            end if  
            On Error Resume Next  
            Dim col 'running column
            Dim b
            for each col in tab.columns
                b = Split(col.comment,";") 
                if len(b(0)) <>0 then  
                    col.name = b(0)
                end if  
                if ubound(b) >0 then  
                    col.comment = b(1)
                else
                    col.comment = ""
                end if  
                On Error Resume Next  
            next  
        end if  
    next  
end sub 
'------------------------------------------------------------
```

kingdee脚本

```vbscript
'------------------------------------------------------------
'
'脚本功能：
'    PowerDesigner中****完成后，将数据库中comment脚本
'    赋值到PDM的name
'执行方法：
'    Open PDM--Tools--Execute Commands--Run Script
'
'------------------------------------------------------------
Option Explicit  
ValidationMode = True  
InteractiveMode = im_Batch
Dim mdl 'the current model   
'get the current active model  
Set mdl = ActiveModel  
If (mdl Is Nothing) Then  
    MsgBox "There is no current Model" 
ElseIf Not mdl.IsKindOf(PdPDM.cls_Model) Then  
    MsgBox "The current model is not an Physical Data model." 
Else  
    ProcessFolder mdl  
End If  
'------------------------------------------------------------
'This routine copy name into code for each table, each column  
'and each view of the current folder

'------------------------------------------------------------
Private sub ProcessFolder(folder)  
    Dim Tab 'running table  
    for each Tab in folder.tables  
        if not tab.isShortcut then  
            if len(tab.comment) <> 0 then  
                tab.name = tab.comment  
            end if  
            On Error Resume Next  
            Dim col 'running column
            Dim b
			Dim dataType
            for each col in tab.columns
                b = Split(col.comment,";") 
                if len(b(0)) <>0 then  
                    col.name = b(0)
                end if  
                if ubound(b) >0 then  
                    col.comment = b(1)
                else
                    col.comment = ""
                end if
				dataType = col.DataType
				if InStr(dataType,"bpchar(1)") then
					col.DataType = "char(1)"
					col.DefaultValue = "''"
					col.Mandatory = true
				elseif InStr(dataType,"timestamp") then
					col.DataType = "datetime"
					col.Mandatory = false
				elseif InStr(dataType,"int8") then
					col.DataType = "bigint"
					col.DefaultValue = 0
					col.Mandatory = true
				elseif InStr(dataType,"varchar") then
					col.DefaultValue = "''"
					col.Mandatory = true
				end if
                On Error Resume Next  
            next  
        end if  
    next  
end sub 
'------------------------------------------------------------
```


