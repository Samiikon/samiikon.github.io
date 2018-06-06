# Latest posts
{% for post in site.posts %}
<div>
    <h3><a href="{{ post.url }}">{{ post.title }}</a></h3>
    <p>By Sami - {{ post.date | date: "%-d %B, %Y" }}</p>
</div>
<hr/>
{% endfor %}

