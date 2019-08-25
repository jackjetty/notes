#1. 概述
* MFC程序由CWinApp、MainFrm（含Menu，可用CSplitterWndEx分割）、众多Dialog等组成。
* MFC既可以使用纯Dialog的形式，也可以使用Document+View的形式进行开发
    * 纯Dialog
    * Document+View
        * 类似于MVVM的模式
        * 可视化创建了FormView形式的Dialog后，可以使用Project->Class Wizard创建这个FormView相应的ModelView类
* MFC应用程序的入口函数（初始化函数）
    * **BOOL XXXApp::InitInstance()**
    * MFC对WindowsAPI进行了封装。MFC提供给编程人员的第一个裸露程序入口就是CWinApp的InitInstance()，其实最终的入口函数还是WindowsAPI的WinMain()函数
    * 所以在自定义了主界面的Init()等函数时，通常就是在InitInstance()中调用它

#2. 通过代码控制界面
* 并没有主界面的可视化设计界面？只能通过Resource View下的Menu去修改Menu？？
* **通过代码中的CSplitterWndEx等进行界面的切割**
    * 在头文件MainFrm.h，中定义分割类对象和相关初始化函数（HeaderView是生成的Class的名字）：

```
// Overrides
public:
    virtual BOOL OnCreateClient(LPCREATESTRUCT lpcs, CCreateContext* pContext);
protected:  // control bar embedded members
    CSplitterWndEx   m_wndSplitterHoriz1;
private:
    BOOL CreateSplitterWindow(CCreateContext* pContext);
```

    * 在MainFrm.cpp中给出splitter及view的设置：

```
	BOOL CMainFrame::Init()
	{
		// set default
		INT iYCur  = 300;
		INT iYMin  = 0;

		m_wndSplitterHoriz1.SetRowInfo(0, iYCur, iYMin);
		m_wndSplitterHoriz1.RecalcLayout();

		return TRUE;
	}
	
	BOOL CMainFrame::OnCreateClient(LPCREATESTRUCT /*lpcs*/,
		CCreateContext* pContext)
	{
		//SetAMMWindowTitle(); // no project

		BOOL bRet = CreateSplitterWindow(pContext);
		return bRet;
	}

	// -----------------------------------------------------------
	// function builds the splitter and the own views together
	BOOL CMainFrame::CreateSplitterWindow(CCreateContext* pContext)
	{
		//将主窗口分割为2行1列，
		if (!m_wndSplitterHoriz1.CreateStatic(this, 2, 1))
		{
			return FALSE;
		}
		//然后在主窗口的第2行第1列中绑定view
		m_wndSplitterHoriz1.CreateView(0,0,RUNTIME_CLASS(HeaderView),CSize(100,100),pContext);
		m_wndSplitterHoriz1.SetRowInfo(0,200,0);
		m_wndSplitterHoriz1.CreateView(1,0,RUNTIME_CLASS(HeaderView),CSize(100,100),pContext);
		m_wndSplitterHoriz1.SetRowInfo(1,200,0);

		return TRUE;
	}
```

    * 设置切割布局时需要制定分割线两侧是什么View（MFC的文档，是一种特殊的Dialog，在Resource View中的Dialog文件夹右击选择Add Resource时，弹出窗的Dialog节点可以展开，下面有一个FormView节点，用于创建这种View，并且可以在属性窗口修改ID）

#3. 可视化设计界面
* 可在Resource View看到所有的界面
* 主界面的菜单
    * 主界面的菜单UI是XXX.rc/Menu/IDR_MAINFRAME，可以直接可视化的增删改菜单项，设置文本（用@标识用于多语言替换？）、ID、TEXT等。
    * 在主界面MainFrm.cpp中可以用菜单的ID来进行消息映射绑定，消息绑定的事件中也可以Qt的界面
* 右键菜单
    * 可以在XXX.rc/Menu/IDR_CONTEXT_MENU中进行可视化设置，可以设置ID如IDR_CONTEXT_MENU
    * 可以包含多个右键菜单，有各自的ID如IDX_CONTEXT_MENU_LOCAL_LIST_VIEW，代码中动态获得应该显示哪一个右键菜单

```
    UINT uiRessourceIDContextMenus = IDR_CONTEXT_MENU; 

    int iIdxContextMenu = -1; // -1 = undefined

    switch (contextMenu)
    {
    case ContextMenuLocalListView:
        iIdxContextMenu = IDX_CONTEXT_MENU_LOCAL_LIST_VIEW; 
        break;
    case ContextMenuLocalTreeView:
        iIdxContextMenu = IDX_CONTEXT_MENU_LOCAL_TREE_VIEW; 
        break;
    case ContextMenuRemoteListView:
        iIdxContextMenu = IDX_CONTEXT_MENU_REMOTE_LIST_VIEW; 
        break;
    case ContextMenuRemoteTreeView:
        iIdxContextMenu = IDX_CONTEXT_MENU_REMOTE_TREE_VIEW; 
        break;
    case ContextMenuEmpty:
        iIdxContextMenu = IDX_CONTEXT_MENU_EMPTY; 
        break;
    case ContextMenuOutputWindow:
        iIdxContextMenu = IDX_CONTEXT_MENU_OUTPUT_VIEW; 
        break;
    default:
        assert(false); // implementation error -> undefined context menu
        PRINT_TRACE(AMMCOMP_TRACE_LIB_DIALOGS, AMMCOMP_TRACE_LAYER_GUI, AMMCOMP_TRACE_LEVEL_ERROR, FUNCTION_SIGNATURE + " undefined context menu");
        break;
    }

    CMenu oContextMenus;
    if (FALSE != oContextMenus.LoadMenu(uiRessourceIDContextMenus)) // contains all context menus
    {
        CMenu* pPopup = oContextMenus.GetSubMenu(iIdxContextMenu);  // get the wanted context menu
        if(NULL != pPopup)
        {
            if (NULL != pWndParent)
            {
                while (pWndParent->GetStyle() & WS_CHILD)
                {
                    if (NULL != pWndParent)
                    {
                        pWndParent = pWndParent->GetParent();
                    }
                }
            }

            if (NULL != pWndParent)
            {
                pPopup->TrackPopupMenu(TPM_LEFTALIGN | TPM_RIGHTBUTTON, point.x, point.y, pWndParent);
            }	
        }
    }
```
* 对话框
    * 所有的Dialog都在XXX.rc/Dialog/下面
    * 在可视化设计界面中的某个控件上右击可以增加：选择"Add Variable..."
    * 添加、设计Dialog
        * 使用PROJECT->Class Wizard...按步骤设计生成MFC界面
            * 点击"Add Class..."按钮，在弹窗中输入想要创建的窗口名字（也就是Class name，.h和.cpp文件会自动同步）
        * 也可以直接在Resource View的Dialog等文件夹上直接右击添加
            * 保存后会直接在_header/resource.h文件中生成相应的宏、常量，在.rc文件中生成界面相关代码
        * 绑定Dialog弹出的消息映射
            * 菜单点击触发：
            * 页面上的按钮点击触发：
        * 可以使用Toolbox进行界面设计

#4. 数据及事件绑定（通过Variable）
* 数据绑定（MFC中叫数据交换和检验/校验，winform中直接通过控件获取text什么的，相当于已经帮你绑好了）
    * 代码中通过DoDataExchange进行数据双向绑定：
```
void CDlgTabMaintenance::DoDataExchange(CDataExchange* pDX)
{
    CRCSBasicProjectDialogTab::DoDataExchange(pDX);
    DDX_Control(pDX, IDC_TREE_FILES_MAINTENANCE, m_ctrlTreeFiles);
}
```
    * 可视化操作中，右键控件选择"Add Variable..."，同样会在cpp和头文件中生成DoDataExchange部分代码，将控件和变量绑定在一起，也会在.rc和Resource.h文件中生成对应的常量等
        * 如果勾选"Control Variable"，那么添加变量为控件变量，可以操作控件，变量类型将固定为控件类型；**如果不勾选，则让控件关联了一个变量，不可以修改控件**。
        * 将在头文件中声明相应的变量。
        * 同时如果是控件变量，则会在cpp文件中生成DoDataExchange代码段；如果不是控件变量，那么将在.cpp文件的类定义的继承后面多一段, edit_binded_data(0)。
        * 完成变量绑定后，就不需要管它了，在文本变化等事件的处理函数中，会自动把变量值变成控件中用户填的新值
* 在Class中通过消息映射表进行事件绑定
    * 可以手动写事件处理函数和向消息映射表添加记录
    * 可以在设计窗口右击控件选择"Add Event Handler..."
    * 可以在控件的属性窗口中选择事件tab，然后在想要加的事件右侧填函数名
* **打开一个新的MFC Dialog可以用CreateProcess**，比如按钮点击触发或菜单按钮点击触发

#5. 其他
* MainFrm.cpp中的Init()等函数在定义前，都需要现在.h头文件中声明函数原型，否则无法识别报错
* 可以在函数中任意位置临时#include？？ -- 不行，即使vs中可以找到，编译也可能不通过，可能要写到函数中的话，需要特殊定义方法或者当成了内部类？
* **如果Resource.h或者.rc文件被打开了，那么Resource View不能展开，同时这两个文件和设计窗口不能被同时打开**
* **有时Resource View下面节点无法展开，可以先把编辑窗口所有打开的文件关掉再试，可能是rc文件被打开等问题？**
* **给主界面的菜单项绑定的事件，必须声明、定义和绑定分别要放在MainFrm.h和MainFrm.cpp中，否则运行后菜单会一直是灰色，并不是enable的原因**
    * Resource View中给菜单项添加Event Handler时COMMAND的Class List中要选CMainFrame？
* 报错"unexpected \#endif"或者"unexpected end of file while looking for precompiled header. Did you forget to add '#include "stdafx.h"' to your source?"
    * 通常是因为微软忽略掉实现文件中stdafx.h包含头文件之前的一切指令
    * 所谓头文件预编译，就是把一个工程(Project)中使用的一些MFC标准头文件(如Windows.H、Afxwin.H)预先编译，以后该工程编译时，不再编译这部分头文件，仅仅使用预编译的结果。这样可以加快编译速度，节省时间。
    * 编译器通过一个头文件stdafx.h来使用预编译头文件。stdafx.h这个头文件名是可以在project的编译设置里指定的。编译器认为，所有在指令\#include   "stdafx.h"前的代码都是预编译的，它跳过\#include   "stdafx.   h"指令，使用projectname.pch编译这条指令之后的所有代码。
    * 解决
        * \#include "stdafx.h"要放在最前面
        * 不使用预编译头文件（stdafx.h）
        * 在stdafx.h文件中增加预编译处理
        * 还有一种说法是：project_name[right click] -> C/C++ -> Precompiled Header -> Precompiled Header (Choose Not Using Precompiled Headers)
    * [参考](https://blog.csdn.net/ziren235/article/details/1428869)，[参考2](https://blog.csdn.net/xbrain2014/article/details/81318396)