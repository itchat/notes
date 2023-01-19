# Java Full Stack Notes

> 通往全栈工程师与自由之路.

## Docsify

首先初始化项目

```bash
npm i docsify-cli -g
docsify init SDE
docsify start SDE
```

修改原始 css 文件为

```html
<link rel="stylesheet" media="(prefers-color-scheme: light)" href="https://cdn.jsdelivr.net/npm/docsify-themeable@0/dist/css/theme-simple.css">
<link rel="stylesheet" media="(prefers-color-scheme: dark)" href="https://cdn.jsdelivr.net/npm/docsify-themeable@0/dist/css/theme-simple-dark.css">
<link rel="stylesheet" href="theme-custom.css">
```

根目录创建 theme-custom.css

```css
@import url('https://fonts.googleapis.com/css2?family=JetBrains+Mono&display=swap');

:root {
  --base-font-family: 'JetBrains Mono', monospace;
  --base-line-height: 1.7;
  --base-font-size: 16px;
}
```

加入搜索以及其他插件与功能

```html
<body>
    <div id="app"></div>
    <script src="//cdn.jsdelivr.net/npm/docsify-plugin-carbon@1/index.min.js"></script>
    <script>
        window.$docsify = {
            name: "",
            repo: "",
            loadSidebar: true,
            subMaxLevel: 6,
            search: "auto",
            coverpage: true,
            plugins: [
                // Change to your Carbon property ID
                // cSpell:disable
                // DocsifyCarbon.create("CEAIP237", "docx"),
                // cSpell:enable
            ],
        };
    </script>
    <script src="https://cdn.jsdelivr.net/gh/forthespada/forthespada.github.io@master/plugin/jquery.js"></script>
    <script src="https://cdn.jsdelivr.net/gh/forthespada/forthespada.github.io@master/plugin/jquery.goup.js"></script>
    <script type="text/javascript">
        $(document).ready(function () {
            $.goup({
                trigger: 700,
                bottomOffset: 20,
                locationOffset: 8,
                title: '',
                titleAsText: true
            });
        });
    </script>
    <script src="//cdn.jsdelivr.net/npm/docsify/lib/plugins/zoom-image.min.js"></script>
    <script src="//unpkg.com/docsify/lib/docsify.min.js"></script>
    <script src="//unpkg.com/docsify/lib/plugins/emoji.min.js"></script>
    <script src="https://unpkg.com/docsify-copy-code@2"></script>
    <script src="//unpkg.com/docsify/lib/plugins/search.min.js"></script>
    <script src="//unpkg.com/prismjs/components/prism-bash.min.js"></script>
    <script src="//unpkg.com/prismjs/components/prism-java.min.js"></script>
</body>
```

最后在 _sidebar.md 文件中用以下格式整理目录

```markdown
- [SDE Roadmap.](README.md)
- **JavaSE**
  - [Day01](./JavaDev/JavaSE/day01.md)
```

## Typora Forest

修改 typora 主题为 forest, [下载](https://theme.typora.io/theme/Forest/)拖动到主题目录后对 code-mirror.css 文件稍作代码块修改

```css
.cm-s-inner.CodeMirror {
    background-color: #003444;
    color: #EEFFFF;
    padding: 1.75rem 1.15rem 1.75rem 1.15rem;
    border-radius: 1px;
}
```

## Reference

[Typora Theme](https://theme.typora.io/)

[LearningNotes](https://github.com/moxi624/LearningNotes)