# pastelmind.github.io

What is going on?

## Posts

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">({{ post.date | date: "%Y-%m-%d" }}) {{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
