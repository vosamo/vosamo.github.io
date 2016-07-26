此Git库为我的博客  [Vosamo's Homepage](http://vosamo.github.io)。
使用Jekyll进行搭建，Jekyll是一个Ruby写的程序，可以将Markdown写的文章通过模板生成最终的Html静态文件。
博客文章的评论功能使用了多说。

如果你直接拷贝或Fork本Git库作为自己的博客，请删除我写的文章，修改 `_includes / comments.md` 中的disqus_shortname，以及修改 `_layouts / default.html`中百度统计的标识  `hm.src = "//hm.baidu.com/hm.js?a76abfd6cd850b54e8baaa69b329db51"；`，这段js代码可以在百度统计网站上生成。另外，你需要添加自己的评论系统，在多说上完成注册，复制一段js代码替换`_includes/comments.md`中的代码。

最后感谢您的配合。

