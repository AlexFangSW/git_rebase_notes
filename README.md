# git rebase notes

## 先檢視當前 commit history
```
git log --oneline --graph

# --oneline : 一個commit一行
# --graph : 字面上的意思, 畫圖 XD
```
<img src="https://user-images.githubusercontent.com/97815504/167649962-34c291ae-fb6f-4986-8ba9-936fee87b8f0.png" width=50%>

## Start rebase interactive
```
# 從某個commit到當前的要做rebase
git rebase -i 6f607a9

# 如上, 不過再往前一個commit: 注意 "^"
git rebase -i 6f607a9^

# HEAD往回數n個為rebase起點: 往回5個(從1開始)
git rebase -i HEAD~5

# 把當前的branch移到某個branch的最新然後開始做rebase(順便看一下有沒有問題)
git rebase -i master
```
> 還有很多其他用法~ 可以看官方的文件.<br>
> [Git Tools - Rewriting History](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History) <br>
> [Git - git-rebase](https://git-scm.com/docs/git-rebase) 

## 開始之後會先跳出類似下面這個檔案, 讓你決定要做甚麼
> 這是用 git rebase -i HEAD~5 的結果 <br>
> 底下都有講解指令, 這邊也有一篇把過程說的很好: 使用 [Git Rebase Interactive 模式整理 Commit](https://www.gss.com.tw/blog/%E4%BD%BF%E7%94%A8-git-rebase-interactive-%E6%A8%A1%E5%BC%8F%E6%95%B4%E7%90%86-commit) <br>
> 大部分都很好理解, 就不多說了, 後面只記錄split commit部分.
```
pick cecc7e6 更新「README.md」
pick 865d85b 更新「README.md」
break                         # break要放在這邊, 做完上面的會先暫停
pick 165ed11 Update README.md
edit 8907920 Update README.md # 使用edit到這裡會停下, 讓你重新commit東西, 也就是split
pick 35de527 yeet 

# Rebase 6768593..35de527 onto 6768593 (5 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup [-C | -c] <commit> = like "squash" but keep only the previous
#                    commit's log message, unless -C is used, in which case
#                    keep only this commit's message; -c is same as -C but
#                    opens the editor
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified); use -c <commit> to reword the commit message
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
```
## break後如果要繼續 <br>
```
# 繼續前先看一下接下來要做的, 可以作調整
git rebase --edit-todo
確定要繼續
git rebase --continue
```
## Conflict
```
# 處理一下衝突後
git add <filename>
git rebase --continue
```
## Split commit
> 要split的前面要用 edit <br>
> > edit cecc7e6 some text
```
# 到點後記得再往前一個紀錄, 這樣那些被commit的檔案才會回復成還沒被add的狀態, 從這邊開始我們的split
git reset HEAD^

# 把想要歸為一個commit的add
git add <file>

# add 完後commit
git commit -m "split a commit!!"

# 不斷重複前2個步驟直到沒有檔案還需要commit.
# 完成後在最後下 --continue結束edit
git rebase --continue

```
## 完成rebase想push上去
> 如果直接用git push 大概會出事. <br>
> 但用 --force 又不太好, 所以使用git --force-with-lease <br>
> 這邊有一篇在講解為甚麼 [–force considered harmful; understanding git’s –force-with-lease](https://blog.developer.atlassian.com/force-with-lease/)
```
git push <remote> <branch> --force-with-lease

git push origin test_branch --force-with-lease

```

