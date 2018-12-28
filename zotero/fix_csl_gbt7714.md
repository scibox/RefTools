# 自定义宏批量修改csl生成参考文献书目的错误

鉴于csl还没有想好怎么支持多语言，见([csl/schema#63](https://github.com/citation-style-language/schema/issues/63))，但 GB/T 7714-{1987,2005,2015} 都有多语言的需求，导致现在 csl 没法生成完全正确的参考文献书目。生成中错误较显著的，就是多英文作者(多于4位)省略以“等”结尾，实际应为“et al.”

> 实际问题还有很多，比如图书的版本，默认还是会写作“第n版”，同样与英文书不符。
> 但是相对而言，作者比较好匹配，行首+中英文紧邻使错误尤为明显。
> 同时，GB/T 7714-{1987,2005} 的 csl，完成度还不够高(csl自述及研究过的同学反馈)，且最新版本 GB/T 7714-2015 (其实三个版本差距都不大) 还没有相匹配的 csl。真正一个完成度更高的 GB/T 7714-2015，需要正则修改的可能更多，也更难。
> GB/T 7714 里面一些规定也不合理，比如英文作者完全大写，最终效果也很丑。

## 新建自定义宏

首先，先调出“开发工具”标签页。

[windows word 开发工具](https://support.office.com/zh-cn/article/%E6%98%BE%E7%A4%BA-%E5%BC%80%E5%8F%91%E5%B7%A5%E5%85%B7-%E9%80%89%E9%A1%B9%E5%8D%A1-e1192344-5e56-4d45-931b-e5fd9bea2d45)
[mac word 开发工具](https://support.office.com/zh-cn/article/%E5%9C%A8-word-2016-for-mac-%E4%B8%AD%E6%98%BE%E7%A4%BA-%E5%BC%80%E5%8F%91%E5%B7%A5%E5%85%B7-%E9%80%89%E9%A1%B9%E5%8D%A1-0c0778a2-fa91-4b75-9164-0685ae00e9b4)

然后，新建宏。

[新建或编辑宏](https://support.office.com/zh-cn/article/%E5%88%9B%E5%BB%BA%E6%88%96%E8%BF%90%E8%A1%8C%E5%AE%8F-c6b99036-905c-49a6-818a-dfb98b7c3c9c)

宏内容见下，且同步发布于[gitgist-fix_csl_gbt7714](https://gist.github.com/specter119/ea9440c8573aa0df266ea87745226d37)。

```vb
Sub deng2etal()
'
' deng2etal macro
'
'
    With Selection.Find
        .Forward = True
        .ClearFormatting
        .Text = "([A-z]@)等"
        With .Replacement
            .ClearFormatting
            .Text = "\1, et al"
        End With
        .Wrap = wdFindStop
        .Execute Replace:=wdReplaceAll, MatchWildcards:=True
    End With
End Sub
Sub etal2deng()
'
' etal2deng macro
'
'
    With Selection.Find
        .Forward = True
        .ClearFormatting
        .Text = "([⺀-￭]@)([, ]*)et al"
        With .Replacement
            .ClearFormatting
            .Text = "\1\2等"
        End With
        .Wrap = wdFindStop
        .Execute Replace:=wdReplaceAll, MatchWildcards:=True
    End With
End Sub
```

> 默认行为全部生成“等”，使用`deng2etal`即可，反之，用`etal2geng`。
> 入前文所言，即使运行了，还是会有个别错误，暂时没有计划把这个宏做的很长很丰富的计划。

## 生成快速访问工具栏按钮

[为宏添加按钮](https://support.office.com/zh-cn/article/%E5%B0%86%E5%AE%8F%E5%88%86%E9%85%8D%E7%BB%99%E6%8C%89%E9%92%AE-728c83ec-61d0-40bd-b6ba-927f84eb5d2c#OfficeVersion=macOS)

## 用法

如果没有选择，文中自定义宏会对光标后到文末进行替换，所以，替换前应选中所有书目或者将光标放到书目最前部分。
点一下刚刚配置的快速访问工具栏按钮，即可。