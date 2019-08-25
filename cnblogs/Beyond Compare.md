* 在进行文件夹比较时，默认可能即使文件、文件夹内容相同也显示红色。
    * 原因：为了速度，默认没有打开文件就比较。
    * 解决：如果想打开就开始自动比较，需设置："Session" > "Session Settings..." > "Comparison" Tab: 在"Requires opening files:" 下选中"Compare contents"并选择"Rules-based comparison"选项。