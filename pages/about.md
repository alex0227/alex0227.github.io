---
layout: page
title: About
description: 不是没有风景，只是高度不够
keywords: feng chen, 晨枫
comments: true
menu: 关于
permalink: /about/
---

才与诚合，然后事可成，恃才而败


## 联系

{% for website in site.data.social %}
* {{ website.sitename }}：[@{{ website.name }}]({{ website.url }})
{% endfor %}

## Skill Keywords

{% for category in site.data.skills %}
### {{ category.name }}
<div class="btn-inline">
{% for keyword in category.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}
