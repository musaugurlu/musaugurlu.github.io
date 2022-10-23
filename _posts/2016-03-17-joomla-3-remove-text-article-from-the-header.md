---
layout: post
title: Joomla 3 – Remove text Article from the Header
description: Joomla 3 – Remove text Article from the Header
image: /assets/img/posts/2016-03-17-joomla-3-remove-text-article-from-the-header/Joomla.png
category: [Web, CMS]
tags: [joomla, cms, web]
date: 2016-03-17 00:00 -0400
---

## Joomla 3 – Remove text Article from the Header

![Joomla](/assets/img/posts/2016-03-17-joomla-3-remove-text-article-from-the-header/Joomla.png)

If you manage a Joomla website, you probably require linking an article directly that is not under any menu item. In this case, you may see the “Article” text in the article header.

Here is a quick solution to remove that.

Open the file in the path below in your FTP Manager org file manager in your hosting account

> `Components/com_content/views/article/view_html.php`

find the code below and command it out

```php
// $this->params->def(‘page_heading’, JText::_(‘JGLOBAL_ARTICLES’));
```

The text "Article" should be gone from your article header now.
