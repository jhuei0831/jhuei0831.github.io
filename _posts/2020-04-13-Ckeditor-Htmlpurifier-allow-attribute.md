---
title: Ckeditor + Htmlpurifier allow attribute
layout: post
featured-img: kerwin
categories:
- Htmlpurifier
- Ckeditor
---

I created a page manager in the backstage, and it edit by the ckeditor. Also, I use Htmlpurifier to defense the xss injection. But Htmlpurifier will block the attribute like Bootstrap tabs plugins. 
So the way I used to allow attribute while using Htmlpurifier.

Here is the [document](http://www.poultry.org.tw/htmlpurifier/docs/enduser-customize.html).

For example, I want to purify the html below.

```
<div class="bootstrap-tabs" data-tab-set-title="Program Introduction"></div>
```

If you use the simple purifier like this,

```
function xss_filter($content){
    $purifier = new HTMLPurifier($config);
    $cleanContent = $purifier->purify($content);
    return $cleanContent;
}
```

You will get the output below,

```
<div class="bootstrap-tabs"></div>
```

The  ***data-tab-set-title***  is disabled. So, add the code below and you will get what you want.

```
$config = HTMLPurifier_Config::createDefault(); 
$def = $config->getHTMLDefinition(true);
$def->addAttribute('div', 'data-tab-set-title', 'CDATA');
```

Hope it will help !
