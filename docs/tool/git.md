# Git


## 基本用法

### 提交
- `git add` 文件内容添加到 Stage 
- `git commit`  Stage 添加到 Commit history
- `git push` Commit history 添加到 Remote


## 更多用法

- `git add -p` 修改片段（hunk）**选择性**添加到 Stage

    ??? note "关于 git add -p file 进入交互式 Stage 模式"
        ``` 
        例如： (1/3) Stage this hunk [y,n,q,a,d,j,J,g,/,e,?]?
        y 表示暂存当前的修改
        n 表示不暂存当前的修改
        q 表示退出暂存模式
        a 表示暂存当前以及其后面的所有修改
        d 表示不暂存当前以及其后面的所有修改
        j 和 J 表示移动到下一个修改
        k 和 K 表示移动到上一个修改
        g 表示跳转到指定行号
        / 表示搜索匹配的内容
        e 表示手动编辑暂存的内容
        ? 表示显示帮助信息
        ```

- `git rebase -i HEAD~num` 重新整理 Commit history

    ??? note "关于 git rebase -i HEAD~2 进入交互式 rebase 模式"
        ```
        1. 列出一个 commit 列表，每个 commit 前面有一个操作指令，默认是 pick
        2. 按你的想法修改操作指令，保存并关闭文件
        3. 紧接着打开一个新的界面，用于编辑合并后的 commit 信息，修改保存并关闭
        4. 如果你要覆盖远程仓库 `git push origin branch-name --force`
        ```



- `git blame` 查看代码是谁写的
- `git show` 查看 commits tags 内容


## gitflow 工作流
