# Learning Docker And Kubernetes
## Docker

{% assign docker = site.pages
  | where: "docker", true
  | group_by: "category" %}

{% for docker_group in docker %}
{% if docker_group.name != "" %}
### {{ docker_group.name }}
{% else %}
### Misc
{% endif %}
{% for docker in docker_group.items %}
- [{{ docker.title }}]({{ docker.url | relative_url }})
{% endfor %}
{% endfor %}

## Docker Compose

{% assign docker-compose = site.pages
  | where: "docker-compose", true
  | group_by: "category" %}

{% for docker-compose_group in docker-compose %}
{% if docker-compose_group.name != "" %}
### {{ docker-compose_group.name }}
{% else %}
### Misc
{% endif %}
{% for docker-compose in docker-compose_group.items %}
- [{{ docker-compose.title }}]({{ docker-compose.url | relative_url }})
{% endfor %}
{% endfor %}

## Docker with Maven
{% assign docker-maven-plugin = site.pages
  | where: "docker-maven-plugin", true
  | group_by: "category" %}

{% for docker-maven-plugin_group in docker-maven-plugin %}
{% if docker-maven-plugin_group.name != "" %}
### {{ docker-maven-plugin_group.name }}
{% else %}
### Misc
{% endif %}
{% for docker-maven-plugin in docker-maven-plugin_group.items %}
- [{{ docker-maven-plugin.title }}]({{ docker-maven-plugin.url | relative_url }})
{% endfor %}
{% endfor %}

## Kubernetes

{% assign kubernetes = site.pages
  | where: "kubernetes", true
  | group_by: "category" %}

{% for kubernetes_group in kubernetes %}
{% if kubernetes_group.name != "" %}
### {{ kubernetes_group.name }}
{% else %}
### Misc
{% endif %}
{% for guide in kubernetes_group.items %}
- [{{ kubernetes.title }}]({{ kubernetes.url | relative_url }})
{% endfor %}
{% endfor %}

## Adding new guidance

Create a new Markdown file that follows this pattern, add a link to it
from this page, and make a pull request:

```markdown
---
category: The broader area this fits into
expires: yyyy-mm-dd (6 months from now)
---
# Thing you're writing about

Introduction of a couple of paragraphs to explain why the thing you're
writing about is important. The [title should probably be a verb, not a
noun][good-services-are-verbs] (e.g. “Storing source code”, not “Code
repositories”).

[good-services-are-verbs]: https://designnotes.blog.gov.uk/2015/06/22/good-services-are-verbs-2/

## User needs

Why do we do this thing? Who is it helping?

## Principles

What broad approaches do we follow when we do this thing?

## Tools

What specific bits of software (commercial or open source) do
we use to help us do this thing?
```

The service manual has some useful information on
[learning about and writing user needs](https://www.gov.uk/service-manual/user-research/start-by-learning-user-needs).
