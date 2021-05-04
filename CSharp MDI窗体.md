---
title: CSharp MDI窗体
date: 2019-05-06 20:00:48
categories: 
 - C#
tags:
 - C#
---

记录c#重mdi窗体加载子窗体的过程

<!-- more -->


1. 将父窗体的`IsMdiContainer`属性设置为`true`
2. 在子窗体实例作为父窗体成员，并在父窗体构造函数中实例化
``` gradle
      private FrmDump dump = null;
      //构造函数
      dump = new FrmDump ();
```
3. 父窗中定义如下成员
``` stata
List<ToolStripMenuItem> tsmis = null;
List<Form> frms = null;
```
4. 在父窗体的Load事件里面添加  
``` gcode
   frms.Add(dump);
   foreach (ToolStripMenuItem menu in MenuMain.Items)
   {
       tsmis.Add(menu);
   }
   ShowChildForm(config, ConfigMenuItem);
```
5. 实现ShowChildForm
``` stata
public void ShowChildForm(Form form, ToolStripMenuItem tsm)
{

    foreach (Form childForm in frms)
    {
        if (childForm == form)
        {
            form.MdiParent = this;          //子窗口以此窗口为父窗口
            form.Dock = DockStyle.Fill; //占满父窗口 
            form.Show(); //显示窗口
        }
        else
        {
            childForm.Hide();//隐藏其余窗口
        }
    }

    //遍历所有按钮控件
    foreach (ToolStripMenuItem tsmi in tsmis)
    {
        if (tsmi == tsm)
        {
            tsmi.BackColor = Color.Blue;//将按钮颜色设置为蓝色
            tsmi.ForeColor = Color.LemonChiffon;//将字体颜色设置为淡色
        }
        else
        {
            tsmi.BackColor = Control.DefaultBackColor;//其余按钮设置为默认背景色
            tsmi.ForeColor = Control.DefaultForeColor; //将字体颜色恢复默认色
        }
    }
}
```
6. 将子窗体设置为无边框属性




