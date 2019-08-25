* 如果不要遮罩父容器控件，直接旁边找个地方创建一个Label控件或者其他控件，然后在逻辑代码中控制其显示和隐藏
    * 如果为了不阻塞主界面而创建了子线程，且要在子线程中控制其显示或隐藏，那么需要用BeginInvoke来包裹界面操作部分
* 如果要遮罩父容器控件，代码中创建一个PictureBox控件，并设置图片路径、填充整个父容器控件、背景色、居中，然后在逻辑代码中控制其显示和隐藏
    * 如果为了不阻塞主界面而创建了子线程，且要在子线程中控制其显示或隐藏，那么需要用BeginInvoke来包裹界面操作部分

```
    public partial class CFTreeView : Form
    {
        private PictureBox pbLoadingForTree;

        public CFTreeView()
        {
            InitializeComponent();

            pbLoadingForTree = new PictureBox();
            pbLoadingForTree.Dock = DockStyle.Fill;
            pbLoadingForTree.ImageLocation = AppDomain.CurrentDomain.BaseDirectory + "Resource\\loading.gif";
            pbLoadingForTree.SizeMode = PictureBoxSizeMode.CenterImage;
            pbLoadingForTree.BackColor = Color.FromArgb(125, Color.White);
            pbLoadingForTree.Visible = false;
            treeView1.Controls.Add(pbLoadingForTree);

            loadOrgs();
        }

        private void cbOrg_SelectedValueChanged(object sender, EventArgs e)
        {
            string selectedOrg = (string)((ComboBox)sender).SelectedItem;

            Thread t = new Thread(() =>
            {
                if (!String.IsNullOrEmpty(selectedOrg))
                {
                    this.BeginInvoke(new Action(delegate ()
                    {
                        pbLoadingForTree.Visible = true;
                        cbOrg.Enabled = false;
                    }));

                    Org currentOrg = cf.Orgs.Where(a => a.Name.Equals(selectedOrg)).First();

                    // only load data once for each org
                    if (currentOrg.Spaces.Count() == 0)
                    {
                        loadEntireTreeForOneOrg(currentOrg);
                    }

                    this.BeginInvoke(new Action(delegate ()
                    {
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

                        pbLoadingForTree.Visible = false;
                        cbOrg.Enabled = true;
                        rtbLog.Clear();
                        rtbEnv.Clear();
                        rtbService.Clear();
                    }));
                }
            });
            t.Start();
        }
    }
```