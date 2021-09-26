---
layout: articles
# articles:
#   excerpt_type: html
---

## Blog entry

{% for post in site.posts %}
*  [{{ post.title }}]({{ post.url }})
{% endfor %}