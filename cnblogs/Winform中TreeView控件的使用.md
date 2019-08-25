* 通过一级级添加Node来构造树结构
* 注意Node的Name和Text可以不同，Text是显示的文本
* 有时为了方便后序操作，在构造与Tree对应的Model类时，可以在子节点类中包含一个父节点类的引用

```
                        // draw tree
                        treeView1.Nodes.Clear();
                        foreach (Space space in currentOrg.Spaces)
                        {
                            TreeNode spaceNode = new TreeNode();
                            spaceNode.Name = space.Name;
                            spaceNode.Text = space.Name;
                            // clear checked
                            spaceNode.Checked = false;
                            foreach (App app in space.Apps)
                            {
                                TreeNode appNode = new TreeNode();
                                appNode.Name = app.Name;
                                appNode.Text = app.Name;
                                // clear checked
                                appNode.Checked = false;
                                spaceNode.Nodes.Add(appNode);
                            }
                            treeView1.Nodes.Add(spaceNode);
                        }
```