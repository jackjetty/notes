* 通过把DropDownStyle样式设置成DropDownList，可以让它变成不可编辑的下拉菜单
* 直接给ComboBox增加Item的话，如果构造函数的参数就是一个字符串，那么在取选中值时，直接把SelectedItem转成字符串使用即可

```
        cbOrg.Items.Add(orgLines[i]);

        private void cbOrg_SelectedValueChanged(object sender, EventArgs e)
        {
            string selectedOrg = (string)((ComboBox)sender).SelectedItem;
        }
```