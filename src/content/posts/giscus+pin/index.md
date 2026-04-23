---
title: giscus配置与置顶图标
description: giscus配置与置顶图标
published: 2025-08-24
draft: false
tags: ["giscus", "Hexo", "评论"]
category: "运维"
lang: ""
---

# 在hexo-icarus主题中，使用giscus博客评论

觉得在众多博客评论系统中，giscus这个做的不错，挺好看的，支持很多种配置，所以想采用这个方案

但是目前icarus对giscus不太支持，有bug

需要手动在博客源码中嵌入js代码片段

具体是，在`node_modules\hexo-theme-icarus\layout\comment`中，添加giscus代码

```js
const logger = require("hexo-log")();
const { Component } = require("inferno");
const view = require("hexo-component-inferno/lib/core/view");

module.exports = class extends Component {
  render() {
    const { config, page, helper } = this.props;
    const { __ } = helper;
    const { comment } = config;
    if (!comment || typeof comment.type !== "string") {
      return null;
    }

    return (
      <div class="card">
        <div class="card-content">
          <h3 class="title is-5">{__("article.comments")}</h3>
          <script
            src="https://giscus.app/client.js"
            data-repo="xxx"
            data-repo-id="xxx"
            data-category="Announcements"
            data-category-id="xxx"
            data-mapping="title"
            data-strict="0"
            data-reactions-enabled="1"
            data-emit-metadata="0"
            data-input-position="top"
            data-theme="light"
            data-lang="zh-CN"
            data-loading="lazy"
            crossorigin="anonymous"
            async
          ></script>
          {(() => {
            try {
              let Comment = view.require("comment/" + comment.type);
              Comment = Comment.Cacheable ? Comment.Cacheable : Comment;
              return (
                <Comment
                  config={config}
                  page={page}
                  helper={helper}
                  comment={comment}
                />
              );
            } catch (e) {
              logger.w(`Icarus cannot load comment "${comment.type}"`);
              return null;
            }
          })()}
        </div>
      </div>
    );
  }
};
```

然后重新hexo server或者hexo generate + hexo deploy就可以

# 文章置顶图标

在post的front-matter里面加入sticky字段可以实现文章的指定，具体排序按照sticky后的数字大小从大到小排列

此时，我们想要在被置顶的文章标题前，加一个大头针图标，具体的添加方法为：

在`node_modules\hexo-theme-icarus\layout\common\article.jsx`中的`<div class="level-left">`标签下，添加

```js
{
  /* Pin icon */
}
{
  page.sticky ? (
    <i class="fas fa-thumbtack level-item" title="Pinned"></i>
  ) : null;
}
```

同理，重启hexo即可
