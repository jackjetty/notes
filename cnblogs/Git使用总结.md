#1. 原理
* 最大特色：**分布式**（本地有一个完整的仓库）
    * 但第一次clone只是把指定的分支（默认master）fetch下来了，以后每次第一次切换到某个分支时才会把这个分支的数据（可能是diff），svn在switch branch时会有个勾选项create branch，就是指同时创建本地分支？
* 三大特色
    * **Branch(分支)**
        * [Git三大特色之Branch(分支)](https://blog.csdn.net/qq_32452623/article/details/78355976)
        * 为什么说Branch这种每个SCM都有的功能是特色呢？因为Git的Branch成本低？？为什么地？？
        * **一个branch会在本地和远程仓库对应的各有一份**
        * Git 的分支，其实本质上仅仅是指向提交对象的可变指针，这个可变指针，指向路的终点。同时，还有一个比较特别的 HEAD 指针，用于记录当前工作的位置
        * git branch //查看本地分支
        * git branch -r //查看远程分支
        * git branch -a //查看本地和远程的所有分支
        * git branch -d <name> //删除
        * git branch -D <name> //强制删除
        * 删除远程分支需要 push 操作：git push origin :<name>
        * git checkout -b develop master：从主分支创建开发分支 
        * git checkout -b <branch name>：以当前分支为基础，创建一个新分支
        * git checkout develop：其实就是切换到develop分支
        * git marge
            * Fast-Forward 合并和策略合并
            * no-ff 参数：这个参数的意思是不要选择 Fast-Forward 合并方式，而是策略合并，策略合并会让我们多一个合并提交。这样做的好处是保证一个非常清晰的提交历史，可以看到被合并分支的存在。
        * git status 命令，查看冲突的详细信息
    * **分为三个区**：工作区（Working Directory）、暂存区（staged）、版本库（commit History）
        * [Git三大特色之Stage(暂存区)](https://blog.csdn.net/qq_32452623/article/details/78417609)
        * 好处：Stage 赋予 Git 更多灵活性。
            * 修改了4个文件，在不放弃任何修改的情况下，其中一个文件不想提交，如何操作？（没add : git add  已经add: git reset --soft ）
            * 修改到一半的文件，突然间不需要或者放弃修改了，怎么恢复未修改前文件？ (git checkout)
            * 代码写一半，被打断去做其他功能开发，未完成代码保存？(git stash)
            * 代码写一半，发现忘记切换分支了？(git stash & git checkout)
            * 代码需要回滚了？（git reset）
        * **如果使用了tortiseGit等工具/插件，那么在修改文件后会自动帮你放到暂存区**
        * Git Bash的界面上清楚的显示了这三个区域，并且表明staged中是待commmit的文件，工作区和暂存区之前的文件通过双击可以迁移到对方中。
        * 工作区（Working Directory）
            * 是我们直接编辑的地方，例如 Android Studio 打开的项目，记事本打开的文本等，肉眼可见，直接操作。
            * 也就是操作系统上物理存储的当前的文件，不管是什么编辑工具编辑的，但是git还不知道你改过这个文件了（也就是说没有成功的使用git插件，也没有用git命令或者UI把这个文件加到暂存区进行commit前的管理）
            * **不要把工作区想成一个坏东西，Git不像P4、Clearcase那样不喜欢管控之外的修改（期望你先checkout再修改文件，否则会不知道你的操作，显示特别的标记），工作区是一个正常的状态，只要在需要的时候把你待提交的文件从工作区移动到暂存区即可。**
        * 暂存区（Stage 或 Index）
            * 数据暂时存放的区域，可在工作区和版本库之间进行数据的友好交流。
            * 也就是本地git仓库已经知晓的修改，知道这些文件已经被修改过了。
            * 也就是留待准备被commit的修改
        * 版本库（commit History）
            * 存放已经提交的数据，push 的时候，就是把这个区的数据 push 到远程仓库了。
            * 也就是已经commit到本地仓库以后的情形，但是可能还没有push到远程中心仓库
    * **Workflow（工作流）**
        * [Git三大特色之WorkFlow(工作流)](https://blog.csdn.net/qq_32452623/article/details/78905181)
        * 所谓工作流，就是说协作的方式，team中的人应该如何管理分支（以及有哪些分支）、如何合并、如何code review（pull request）等等等。
        * 目前使用度最高的工作流前三名（排名不分先后哈）分别是以下三种
            * Git Flow
                * 适用于AMP这种团队开发中，通常类似这种workflow，有master、develop、release、feature、hotfix几种分支。
            * GitHub Flow
                * 适用于开源项目开发（不同团队的人，最终只是合并到master，类似AMP使用到的多语言改动），只有master和feature（参与开发的人自己的分支）两种分支，**最终通过pull request提交合并请求，当 review 或者讨论通过后，代码会合并到目标分支（通常是master）**。
                * **一般 master 会设置 protected 分支保护，只有有权限的人才能推送代码到 master 分支。**
                * **一旦合并到 master 分支，应该立即发布**
            * GitLab Flow
* Fast Forward和no-ff（no fast foward）
    * [Git – Fast Forward 和 no fast foward](https://www.cnblogs.com/xingzc/p/5975433.html)
    * 指两种不同的分支合并策略
    * **在各个图形化工具中可以选择合并方式，如果用git命令的话可以通过参数指定**
    * Fast Forward
        * 当前分支合并到另一分支时，如果没有分歧解决，就会直接移动文件指针。这个过程叫做fastforward。
        * 举例来说，开发一直在master分支进行，但忽然有一个新的想法，于是新建了一个develop的分支，并在其上进行一系列提交，完成时，回到 master分支，此时，master分支在创建develop分支之后并未产生任何新的commit。此时的合并就叫fast forward。
    * --no-ff (no fast foward)
        * --no-ff (no fast foward)，使得每一次的合并都创建一个新的commit记录。即使这个commit只是fast-foward，用来避免丢失信息
        * 使用no-ff后，会多生成一个commit 记录，并强制保留develop分支的开发记录（而fast-forward的话则是直接合并，看不出之前Branch的任何记录）。这对于以后代码进行分析特别有用，故有以下最佳实践。
    * **要求采用–no-ff的方式进行分支合并，其目的在于，希望保持原有“develop branches”整个提交链的完整性。**

#2. 小技巧
* 如果push的时候总是让你输入账号密码，那么可能是因为使用了https方式 push，[参考](https://blog.csdn.net/yywan1314520/article/details/51566924)
    * git bash中使用git remote -v，如果出现origin https://github.com/wei0long/AugmentedReality.git (fetch)  origin https://github.com/wei0long/AugmentedReality.git (push)，就说明可能是这个原因
    * 下面可以改成ssh方式的（当然要生成公钥并加到GitHub上）
        * git remote rm origin
        * git remote add origin git@github.com:wei0long/AugmentedReality.git
        * git push origin
    * ssh登陆方式下有可能出现错误：disconnected no supported authentication
        * git bash正常
        * 或者出现clone需要输入密码时，虽然默认的TortoiseGit的General下面的Git for Windows中的Git.exe Path是对的，但还要修改TortoiseGit的Network下的ssh client为Git安装目录的**C:\Program Files\Git\usr\bin\ssh.exe**，而不是用TortoiseGit自带的
    * 通常ssh方式是本地生成密钥，保证只有你可以上传，然后把公钥加到GitHub的账户中，可以避免https那样每次都要输账号密码（**但是可以通过Windows添加凭证来避免？**）
    * ssh和https方式的clone地址不同
    * ssh不需要有账号，所以虽然方便，但不如https的权限管理细致
    * 仓库上不同分支通常用的是同一个公钥？
* global .gitconfig
    * 可以通过命令修改
        * git config --global user.name "Wang,Ye Ping"
        * git config --global user.email "yeping.wang@siemens.com"
    * 也可以通过TortoiseGit的Git标签页下面的Edit global .gitconfig按钮来修改
    * GitKraken的preference下面的profile中修改的其实也是这个
* 分支操作
    * Git不像CC、SVN那样不同分支使用不同文件夹，可以在同一目录下切换分支
* 回溯历史
    * 通过查看文件或者文件夹的历史后，如果想回到某个commit当时的代码，可以使用命令+commit的SHA-1码，或者TortoiseGit的Show log窗口中选中该次commit然后右击选择“Switch/Checkout to this...”，然后点击OK（选不选择Create Branch都行）。
    * GitLab等系统上或者工具软件，通常有看分支和Commit的Graph图形化的功能
    * GitLab等系统，选中某文件后，通常有"Blame"的功能，可用于查看都有哪些人对该文件的**哪些部分**做过什么改动。

#3. 总结
* 基本操作
    * checkout（除了切换分支的功能）、reset及它们加参数可以把已经在工作区修改了但未add到stage暂存区的代码回滚，但有的命令可能不能指定文件，会把工作区的文件都回滚。
* 分支/合并操作
    * Git的所谓分支，并不像svn、clearcase那样感觉起来是完全物理隔绝独立的东西（图形化后每个分支各自是独立的一条线），更像是一个label或者说指针（图形化后是交织在一起的一些线，有可能两个分支合并后这两个分支的指针都在这个位置，只不过从这两个指针可以各自拎出来不同的线）。
    * 合并代码既可以用merge，也可以用rebase。
        * 一般的流程中都是把合并代码现在本地的分支间做一次，然后发起pull request/merge request申请在远程仓库也做一次。
        * rebase的好处
            * 保留了两个分支的所有历史，所以以后排查问题时，可以很方便得回滚到不同分支的每一种代码组合的历史点。
            * 由于rebase时新的基准点后的所有的commit都依次resolve了冲突，所以避免了合并时一次性解决冲突的复杂程度，而且保留了每次引起冲突的代码的当前的样子，可以看出来当时为什么要这么改，为什么会有冲突。
            * **GitKraken的分支图中会从上次两个分支的分歧点开始又合成了一条线，所谓的rebase就是重新定义基准点，也就是从上一次分歧点重新变成了一条线？**
        * merge
            * 就和svn、clearcase、perforce的merge基本一样
                * [Git 分支 - 分支的新建与合并](https://git-scm.com/book/zh/v1/Git-分支-分支的新建与合并)
                * **有点不同的是，如果源分支没有后续变化，也就是merge的目标分支点是源分支点的直接父节点，那么就可以使用fast-forward，即merge之后把源分支的指针也移动到目标分支的最新点**
                * **但是如果目标分支也有了改动，那么就不能直接使用fast-forward了，会生成一个新的merge点，然后源分支的最新指针还是在他自己的分支最新点上**
            * 如果做了多次改动，合并到目标分支后，目标分支只产生一个commit，并且在这次commit中一次性解决前面的commit产生的所有冲突。
            * 一个从svn、clearcase带过来的习惯是每次merge到目标分支前，先把目标分支的最新代码合并到当前分支，提前做一次冲突解决。
            * **比如在GitKraken中，如果merge之前没有做源分支onto目标分支的rebase，那么merge之后源分支的指针还是在源分支上**
        * rebase
            * 本质是重新设定多次修改历史的基准点到目标分支的最新点。
            * 如果做了多次改动，合并到目标分支后，目标分支会产生原分支上的同样多的commit，就像是把原分支剪了下来接到了目标分支，在每个新的点上都要依次解决原分支当时点的代码和目标分支最新代码（新的基准点的代码）的冲突。
            * **和merge还有一个区别是，rebase就像是指针，所谓的重新定义了基准点，就是两个分支又跑到了同一个点？**
    * pull request和用的是merge还是rebase没关系，就是要把你在本地分支间做的这个合并操作在远程仓库的分支上也做一遍，需要一些人做code review和批准什么的。
    * **pull和fetch的区别**
        * git fetch只是把远程orgin分支同步到本地仓库，但并不会影响对应的本地分支。
        * git fetch不会进行合并，执行后需要手动执行git merge合并，而git pull拉取远程分之后直接与本地分支进行合并。更准确地说，git pull是使用给定的参数运行git fetch，并调用git merge将检索到的分支头合并到当前分支中。