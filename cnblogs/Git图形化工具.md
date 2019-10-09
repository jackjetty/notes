#1.  GitKranken
* 登陆
    * 集成GitHub等登录（有按钮可以直接把ssh key加入到GitHub上）
    * 使用ssh key登录时，可能要在Perferences->Authentication中专门重新生成Gitkraken的公私钥，因为用Git生成的公私钥有可能算法或秘钥长度不同，不符合Gitkraken的要求，会报错。然后选择使用新生成的公私钥，并在服务端导入ssh公钥。
* Stash
    * 相当于把本地的改动都先另外暂存起来，让当前分支文件夹恢复修改前的样子
    * 如果本地有增删改的文件，那么在左侧树双击切换分支时会失败，提醒你先stash修改，或者discard。**但是切换分支会导致本地多出莫名其妙的修改，GitKranken貌似是把文件带乱套了？**
    * 貌似点上方的Stash按钮，不会让你起名字，直接就把当前的修改给Stash了，好像都叫WIP？
    * Stash也会在右侧树上有个标记
* 细节
    * fetch和pull
        * 点fetch all会同步所有的远程分支信息，pull只会fetch+merge当前分支。
        * 点push就直接把当前分支的commit都push到了远端
        * pull时如果当前分支还没有push到远端过，会提醒你先push
    * merge
        * 从你要merge的分支节点上右击选择merge from xxx to yourbranch，如果没有冲突，会自动完成
        * merge后解决冲突时，你选择选择哪个改动后，并不会影响你已经放到stage中的文件，会在unstage中又放一份新的别人的改动？
    * 在你做commit、pull、push等动作时，右侧树可能会重新调整布局，甚至分支的位置颜色都变了。**这时候就完全重新来观察树就好了。**
    * **文件添加到stage之后，如果再有修改，不会影响stage中的文件，unstage部分会也把这个文件列出来**
    * revert
        * 在某个commit节点上右击选择revert commit
        * **生成一个新的提交来撤销某次提交，此次提交之前的commit都会被保留**
    * reset xxx to this commit
        * 在目标commit节点上右击选择reset xxx to this commit，并在子菜单选择想要的回滚方式
        * **回到某次提交，提交及之前的commit都会被保留，但是此次之后的修改都会被退回到暂存区**
        * 本意是回滚某个分支的代码到某个commit，但结合不同子选项可以有不同效果
        * 不管哪种方式，都会把这些修改放到暂存区
        * ![reset xxx to this commit](https://img2018.cnblogs.com/blog/106125/201909/106125-20190919104629202-499878143.png)
            * Soft - keep all changes：会把当前commit和想要回滚到的commit之间的改动都保留下来，即还是当前代码，即只是合并了这两个commit之间的多个commit
            * Mixed - keep working copy but reset index：保留本地代码只移动指针？？
            * Hard - discard all changes：
                * 即只保留目标commit的代码？这才是真正的回滚，但是好像不会把改动放到暂存区。
                * **成功后，所有新于此条 commit 的操作都会被抹去，包括对文件的修改。然后点击上方菜单栏的 push 推到远程仓库，但由于 head 指针滞后，选择 Force 强制把远程项目更为旧版本。**
                * **注意有时候可能由于目标commit之后有增删文件，导致用这个选项reset后并没有真的推到这个commit，而是把该文件放到了修改区，这时只要把这个修改discard就好了。**
* 左侧分支树
    * LOCAL下面是所有本地fetch过的分支，及并不是clone时就会把所有分支搞下来
    * REMOTE下面是远程仓库所有的仓库，一般分支都在origin下面
    * 所有分支都是用同一个文件目录
    * 双击分支是切换到该分支，本地目录的文件会切换过去
    * 单击分支是高亮右侧图中该分支的最新指针点
 * 右侧分支图
    * 最左侧的标签指明了该分支最新的指针点的位置，右侧的电脑图标说明是本地分支，圆圈说明是远程分支，同时画了本地和远程分支
    * 大的有图标的点都是人的commit
    * 有的实心点都是merge的点
    * 如果之前做的是rebase，那么不会形成merge的点，会直接移动到同一条线
    * 一般来说一条竖线就是一个分支，但是如果不同分支间做过rebase，那么这些分支可能在同一条线上，只是最新的指针点不同，**相当于rebase时GitKraken会帮你把俩个分支画到一条线上，也就是说两个分支有时候可以有一部分合在了一起（merge和rebase都会这样？只是合并之前保持两条多一个commit点，还是直接合并成一条线？），当其中一个又有了改动，会再次叉出来变成了两个**
        * **rebase后，GitKraken的分支图中会从上次两个分支的分歧点开始又合成了一条线，所谓的rebase就是重新定义基准点，也就是从上一次分歧点重新变成了一条线？**
        * **还有一种情况是merge时发生了fast forward，也会移动源分支的指针到目标分支，也就变成了一条线，这个时候和rebase后再merge效果类似，但需要目标分支期间没有改动**
    * **相当于每个分支都是一条线一根绳子，从最新的指针点，都可能把它们整条拎出来**
    * 如果是pull request，那么一个commit上会有两个人名，一个是commit的人，一个是操作的人
    * **每次做了merge、rebase等操作后，GitKraken会调整一下分支图，虽然看起来可能不一样了，但其实只是为了美观做了整理，逻辑未变**
    * **再次总结**
        * **从上到下就是按时间降序排的**
        * **只有某个分支还没有被删，那么即使merge或者rebase到了另一个分支，他的最上端肯定也会有一个点（可能会被其他分支的重合），并且在分支图左侧会显示分支名。如果是远程分支，那么分支名称右侧有个点图标，如果是本地分支，那么分支名称右侧会有一个电脑图标。**
        * **remote端的origin的分支图形及信息肯定是最新的，和服务端实时同步。只有本地的分支的图形及信息需要fetch来更新一下。**
        * **只有merge会导致生成一个合并的实心点**
        * **创建分支和merge会导致从一个点拉出来一个线，又在某个点合并回去，也就是会有一个分支又回来。rebase会导致最后和之前拉出来的base的分支又合成了一条线。**
        * **从某个分支的最上端接口开始往下拎（一般是直线，只有创建本分支和merge到其他分支时会拐弯），沿着先可以找到所有的commit，只不过有可能之前做过rebase，因此有些段可能会和其他分支重合。**
            * merge到其他分支，然后又继续开发的样子
                * ![merge to another branch and work again](https://img2018.cnblogs.com/blog/106125/201908/106125-20190820092813117-1655025855.png)
    * 第一次pull等操作时会要求输入显示的用户名，一般都是使用默认的**git**
    * 然后会用这个username和之前clone输入的repo的url去动态拼接pull和push的地址，**一般GitHub和GitLab的url都是username@repoaddress.git，但tfs（mercury）的比较特别是自己定义的地址如ssh://mercury.tfs.siemens.net:22/tfs/IDT/SINUMERIK%20CLOUD/_git/cloudapps.analyzemyperformance，前面多了一个ssh://，后面少了一个.git（可能是把这个.git挪到url的前面变成了/_git/），然后在GitKraken中第二次进行fetch等操作时，会使用它上次输入username后自动拼接的GitHub和GitLab风格的错误的url（如ssh://git@mercury.tfs.siemens.net:22/tfs/IDT/SINUMERIK CLOUD/_git/cloudapps.analyzemyperformance.git），即结尾有一个.git，就会导致地址错误，因此要手动直接在左侧树中的REMOTE下面的origin中edit origin，设置pull和push的url为git@mercury.tfs.siemens.net:22/tfs/IDT/SINUMERIK CLOUD/_git/cloudapps.analyzemyperformance这样，才能避免GitKraken自动生成错误的url**

#2. TortoiseGit
* 如果有disconnected no supported authentication methods available等错误，一般是要把Settings->Network下的ssh client从TortoiseGit自带的换成本机安装的Git中带的D:\Program Files\Git\usr\bin\ssh.exe
    * 如果修改不了，可以从开始菜单中找到TortoiseGit后右击以管理员权限运行，再修改保存
* Stash
    * 和GitKranken不同，可以维护一个Stash list，可以自己起名字
    * 和GitKranken一样，如果本地有增删改的文件，那么在切换分支时会失败，提醒你先stash修改。**但是切换分支不会导致本地多出莫名其妙的修改，GitKranken貌似是把文件带乱套了？**
* 细节
    * 切换分支时，默认会勾选create branch，是指相应得创建本地分支

#3. Git GUI
* 在项目目录右击菜单中选择打开
* 每个项目目录一个窗口
* 第一次clone时，要求远程仓库不能是空的，否则会出错
* rescan其实就是refresh，可以用来刷新当前文件夹的文件状态
* 没有pull功能，只能先fetch，然后merge