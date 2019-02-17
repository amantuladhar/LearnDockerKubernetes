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
{% for kubernetes in kubernetes_group.items %}
- [{{ kubernetes.title }}]({{ kubernetes.url | relative_url }})
{% endfor %}
{% endfor %}
