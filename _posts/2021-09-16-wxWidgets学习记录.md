---
laout: post
title: wxWidgets学习记录
published: true
date: 2021-09-16
author: walkerwyl
tags:
  - GNU/Linux
  - wxWidgets
  - C++
---

## 安装 wxWidgets 3.0.5

1. 添加 wxWidgets 的软件库：在软件源文件 /etc/apt/sources.list 中添加 
deb http://repos.codelite.org/wx3.0.5/debian/ buster libs
2. 执行更新命令时可能出现缺少公钥编码的问题，手动从可靠的公钥服务器上下载。
```shell
$ apt-key adv --recv-keys --keyserver keyserver.ubuntu.com F42ED6FBAB17C654
```
3. 下载官方在 Linux 平台上预编译的二进制程序，避免手动编译的麻烦（后续会尝试在其他平台手动编译）。

```shell
apt-get install libwxbase3.0-0-unofficial \
                 libwxbase3.0-dev \
                 libwxgtk3.0-0-unofficial \
                 libwxgtk3.0-dev \
                 wx3.0-headers \
                 wx-common \
                 libwxbase3.0-dbg \
                 libwxgtk3.0-dbg \
                 wx3.0-i18n \
                 wx3.0-examples \
                 wx3.0-doc
```

## wxWidgets 的第一个程序

从 Codeblocks 新建的一个默认程序，了解 wxWidgets 能够进行跨平台开发的方法。



1. FirstWindowApp.h

```cpp
#ifndef FIRSTWINDOWMAIN_H
#define FIRSTWINDOWMAIN_H

#ifndef WX_PRECOMP
    #include <wx/wx.h>
#endif

#include "FirstWindowApp.h"


#include <wx/button.h>
#include <wx/statline.h>
class FirstWindowDialog: public wxDialog
{
    public:
        FirstWindowDialog(wxDialog *dlg, const wxString& title);
        ~FirstWindowDialog();

    protected:
        enum
        {
            idBtnQuit = 1000,
            idBtnAbout
        };
        wxStaticText* m_staticText1;
        wxButton* BtnAbout;
        wxStaticLine* m_staticline1;
        wxButton* BtnQuit;

    private:
        void OnClose(wxCloseEvent& event);
        void OnQuit(wxCommandEvent& event);
        void OnAbout(wxCommandEvent& event);
        DECLARE_EVENT_TABLE()
};

#endif // FIRSTWINDOWMAIN_H
```

2. FirstWindowApp.cpp

```cpp
#ifdef WX_PRECOMP
#include "wx_pch.h"
#endif

#ifdef __BORLANDC__
#pragma hdrstop
#endif //__BORLANDC__

#include "FirstWindowApp.h"
#include "FirstWindowMain.h"
// 程序入口
IMPLEMENT_APP(FirstWindowApp);

bool FirstWindowApp::OnInit()
{
    FirstWindowDialog* dlg = new FirstWindowDialog(0L, _("wxWidgets Application Template"));
    dlg->Show();
    return true;
}
```

3. FirstWindowMain.h

```cpp
#ifndef FIRSTWINDOWMAIN_H
#define FIRSTWINDOWMAIN_H

#ifndef WX_PRECOMP
    #include <wx/wx.h>
#endif

#include "FirstWindowApp.h"


#include <wx/button.h>
#include <wx/statline.h>
class FirstWindowDialog: public wxDialog
{
    public:
        FirstWindowDialog(wxDialog *dlg, const wxString& title);
        ~FirstWindowDialog();

    protected:
        enum
        {
            idBtnQuit = 1000,
            idBtnAbout
        };
        wxStaticText* m_staticText1;
        wxButton* BtnAbout;
        wxStaticLine* m_staticline1;
        wxButton* BtnQuit;

    private:
        void OnClose(wxCloseEvent& event);
        void OnQuit(wxCommandEvent& event);
        void OnAbout(wxCommandEvent& event);
        DECLARE_EVENT_TABLE()
};

#endif // FIRSTWINDOWMAIN_H
```

4. FirstWindowMain.cpp

```cpp
#ifdef WX_PRECOMP
#include "wx_pch.h"
#endif

#ifdef __BORLANDC__
#pragma hdrstop
#endif //__BORLANDC__

#include "FirstWindowMain.h"

//helper functions
enum wxbuildinfoformat {
    short_f, long_f };

wxString wxbuildinfo(wxbuildinfoformat format)
{
    wxString wxbuild(wxVERSION_STRING);

    if (format == long_f )
    {
#if defined(__WXMSW__)
        wxbuild << _T("-Windows");
#elif defined(__WXMAC__)
        wxbuild << _T("-Mac");
#elif defined(__UNIX__)
        wxbuild << _T("-Linux");
#endif

#if wxUSE_UNICODE
        wxbuild << _T("-Unicode build");
#else
        wxbuild << _T("-ANSI build");
#endif // wxUSE_UNICODE
    }

    return wxbuild;
}


BEGIN_EVENT_TABLE(FirstWindowDialog, wxDialog)
    EVT_CLOSE(FirstWindowDialog::OnClose)
    EVT_BUTTON(idBtnQuit, FirstWindowDialog::OnQuit)
    EVT_BUTTON(idBtnAbout, FirstWindowDialog::OnAbout)
END_EVENT_TABLE()

FirstWindowDialog::FirstWindowDialog(wxDialog *dlg, const wxString &title)
    : wxDialog(dlg, -1, title)
{
    this->SetSizeHints(wxDefaultSize, wxDefaultSize);
    wxBoxSizer* bSizer1;
    bSizer1 = new wxBoxSizer(wxHORIZONTAL);
    m_staticText1 = new wxStaticText(this, wxID_ANY, wxT("Welcome To\nwxWidgets"), wxDefaultPosition, wxDefaultSize, 0);
    m_staticText1->SetFont(wxFont(20, 74, 90, 90, false, wxT("Arial")));
    bSizer1->Add(m_staticText1, 0, wxALL|wxEXPAND, 5);
    wxBoxSizer* bSizer2;
    bSizer2 = new wxBoxSizer(wxVERTICAL);
    BtnAbout = new wxButton(this, idBtnAbout, wxT("&About"), wxDefaultPosition, wxDefaultSize, 0);
    bSizer2->Add(BtnAbout, 0, wxALL, 5);
    m_staticline1 = new wxStaticLine(this, wxID_ANY, wxDefaultPosition, wxDefaultSize, wxLI_HORIZONTAL);
    bSizer2->Add(m_staticline1, 0, wxALL|wxEXPAND, 5);
    BtnQuit = new wxButton(this, idBtnQuit, wxT("&Quit"), wxDefaultPosition, wxDefaultSize, 0);
    bSizer2->Add(BtnQuit, 0, wxALL, 5);
    bSizer1->Add(bSizer2, 1, wxEXPAND, 5);
    this->SetSizer(bSizer1);
    this->Layout();
    bSizer1->Fit(this);
}


FirstWindowDialog::~FirstWindowDialog()
{
}

void FirstWindowDialog::OnClose(wxCloseEvent &event)
{
    Destroy();
}

void FirstWindowDialog::OnQuit(wxCommandEvent &event)
{
    Destroy();
}

void FirstWindowDialog::OnAbout(wxCommandEvent &event)
{
    wxString msg = wxbuildinfo(long_f);
    wxMessageBox(msg, _("Welcome to..."));
}
```

个人认为学习中存在的误区，跨平台开发**似乎**独立于每个平台，看起来类似于 Java 编译成字节码，处处运行一般，实际上的做法很简单，**编写后的程序将要在需要的平台上全部编译一次**。跨平台不是独立于各个平台进行开发，实际上是结合了各个平台的特点进行开发，所考虑、学习的知识有增无减。

## wxWidgets 程序框架分析

1. 应用程序的主窗口及其他界面元素
    该应用程序由派生自框架窗口类 wxDialog 的 FirstWindowDialog 产生。FirstWindowDialog 的构造函数实现了主窗口的界面布局，包含基本控件并设置其属性。
2. 应用程序的事件处理
    通过事件表来对事件做出响应。
    首先，在相应的类中使用宏 DECLARE_EVENT_TABLE 声明事件表。其次，实现事件处理函数。最后是通过 BEGIN_EVENT_TABLE 和 END_EVENT_TABLE 定义事件表，重点是使用事件关联宏将事件和该事件的处理函数关联起来。FirstWindowDialog 定义了事件处理函数 OnAbout 和 OnQuit，并使用事件关联宏 EVT_BUTTON 把标识为 idBtnAbout 和 idBtnQuit 的菜单事件分别和函数 OnAbout 和 OnQuit 关联起来。
3. 应用程序的抽象
    应用程序抽象成类 wxApp，一个具体的 wxWidgets 程序需要一个 wxApp 的派生类代表其自己。上述代码中代表程序的 FirstWindowApp。
4. 应用程序的初始化和退出
    应用程序抽象后，通过重载 wxApp 的虚函数 OnInit 和 OnExit 来定义初始化和退出动作。在 FirstWindowApp 实现的函数 OnInit 中，首先创建了 FirstWindowDialog 对象作为应用程序的主窗口，然后函数 Show 将主窗口显示出来并返回 true，表示初始化成功。
5. 应用程序的实现
    wxWidgets 通过宏 DECLARE_APP 和 IMPLEMENT_APP 来实现应用程序，如创建应用程序对象。

## 参考资料

1. [wxWidgets官网](https://www.wxwidgets.org/)
2. [解决软件源缺少公钥](https://blog.csdn.net/li740207611/article/details/51931333)