---
title: Testing Ansible Roles with Molecule behind a proxy
date: 2018-06-13
categories:
  - technology
tags:
  - molecule
  - ansible
  - docker
  - proxy
layout: post
---

If you have ever worked with so-called devops tools (Docker, CAPS and friends)
behind a corporate proxy, you know that's not their main use case. Althought it's
getting better, usually proxy support feels like an afterthought and documentation
is lacking.
I remember reading the source of Test Kitchen Docker driver and Ansible provisioner
to understand how they were using `http_proxy` and `https_proxy` environment variables.
In the case of the Ansible provisioner, i even had to
[add proxy support myself for Ansible Galaxy][kitchen-ansible-pr].
So when I started using [Molecule][molecule] and it couldn't build the base Docker image
because `yum` couldn't reach the CentOS repo, I was like "Here we go again..."

In this case I'm using Molecule with the Docker driver.
This proxy support problem affects three different components:
- the Docker engine, when pulling images.
- the Docker image itself at build time, when using the package manager of the chosen distro.
- the Ansible tasks, when they need to reach the Internet.

Well it took me a few hours of testing but I came up with a solution. It's not perfect
but not too far from it.
in this case, for me perfect means:
- it works with and without proxy.
- it uses environment variables all the way: no hardcoded IPs.
- it works without any changes to the code under test, here the Ansible playbook.
- it can be checked in version control and work as is for my teammates.

The first part is now [well documented by Docker][docker-doc], so I won't elaborate on that.
You can also have a look at [that gist][docker-proxy-gist] that covers more OSes and init
processes.

## Building the Docker image

For the second, it's a good thing Molecule drops a Dockerfile template in the
scenario directory, it's easier to modify that than Molecule itself.
The modification is fairly simple: I just check if the platform has an `env` key
and then iterate over that array to add `ENV` command to the resulting Dockerfile.

`molecule/default/Dockerfile.j2`

{% highlight liquid %}
{% raw %}
# Molecule managed

{% if item.registry is defined %}
FROM {{ item.registry.url }}/{{ item.image }}
{% else %}
FROM {{ item.image }}
{% endif %}

{% if item.env is defined %}
{% for var, value in item.env.iteritems() %}
{% if value %}
ENV {{ var }} {{ value }}
{% endif %}
{% endfor %}
{% endif %}

RUN if [ $(command -v apt-get) ]; then apt-get update && apt-get upgrade -y && apt-get install -y python sudo bash ca-certificates && apt-get clean; \
    elif [ $(command -v dnf) ]; then dnf makecache && dnf --assumeyes install python sudo python-devel python2-dnf bash && dnf clean all; \
    elif [ $(command -v yum) ]; then yum makecache fast && yum update -y && yum install -y python sudo yum-plugin-ovl bash && sed -i 's/plugins=0/plugins=1/g' /etc/yum.conf && yum clean all; \
    elif [ $(command -v zypper) ]; then zypper refresh && zypper update -y && zypper install -y python sudo bash python-xml && zypper clean -a; \
    elif [ $(command -v apk) ]; then apk update && apk add --no-cache python sudo bash ca-certificates; \
    elif [ $(command -v xbps-install) ]; then xbps-install -Syu && xbps-install -y python sudo bash ca-certificates && xbps-remove -O; fi
{% endraw %}
{% endhighlight %}

Then in my `molecule/default/molecule.yml` file, I define the platforms like so:

{% highlight yaml %}
---
dependency:
  name: galaxy
driver:
  name: docker
lint:
  name: yamllint
platforms:
  - name: instance
    image: centos:7
    env:
      http_proxy: ${http_proxy}
      https_proxy: ${https_proxy}
      no_proxy: ${no_proxy}
provisioner:
  name: ansible
  lint:
    name: ansible-lint
scenario:
  name: default
verifier:
  name: testinfra
  lint:
    name: flake8
{% endhighlight %}

Supposing that the following variables are set in my environment:

{% highlight bash %}
http_proxy=http://proxy:3128
http_proxy=https://proxy:3128
no_proxy=localhost
{% endhighlight %}

That will produce this `Dockerfile` for the Molecule tests:

{% highlight Dockerfile %}
FROM centos:7

ENV http_proxy http://myproxy:3128
ENV https_proxy https://myproxy:3128
ENV no_proxy localhost

RUN if [ $(command -v apt-get) ]; then apt-get update && apt-get upgrade -y && apt-get install -y python sudo bash ca-certificates && apt-get clean; \
    elif [ $(command -v dnf) ]; then dnf makecache && dnf --assumeyes install python sudo python-devel python2-dnf bash && dnf clean all; \
    elif [ $(command -v yum) ]; then yum makecache fast && yum update -y && yum install -y python sudo yum-plugin-ovl bash && sed -i 's/plugins=0/plugins=1/g' /etc/yum.conf && yum clean all; \
    elif [ $(command -v zypper) ]; then zypper refresh && zypper update -y && zypper install -y python sudo bash python-xml && zypper clean -a; \
    elif [ $(command -v apk) ]; then apk update && apk add --no-cache python sudo bash ca-certificates; \
    elif [ $(command -v xbps-install) ]; then xbps-install -Syu && xbps-install -y python sudo bash ca-certificates && xbps-remove -O; fi
{% endhighlight %}

My proxy is now picked up by the package manager and the Docker image gets built properly.

## Running Ansible

Now when Molecule runs the Ansible playbook from the scenario, every task that
reaches out to the Internet also needs to be configured to use the proxy.
Fortunately, we can set environment variables at the play level and they will
be set for every task of that play.
To get the environment variable value from my dev machine, I use the `env` lookup
filter in Jinja2.
If we put all of this together, we get the following playbook:

{% highlight yaml %}
{% raw %}
---
- name: Converge
  hosts: all
  roles:
    - role: yo
  environment:
    http_proxy: "{{ lookup('env', 'http_proxy') }}"
    https_proxy: "{{ lookup('env', 'https_proxy') }}"
    no_proxy: "{{ lookup('env', 'no_proxy') }}"
{% endraw %}
{% endhighlight %}

Every call to the `yum` or `get_url` modules will have those environment
variables set and go through the proxy instead of straight out to the Internet.

## Conclusion

While I don't really like to have to commit the `Dockerfile` template and the
modified test playbook to my repo, it's a drawback I can live with.
All the other goals have been met.
I tested it with and without proxy, it works the same.
It gets all the values from my environment, no hardcoding anything.
I didn't modify the playbook under test to accomodate for my proxy settings.
So it's a good all around solution in my book. Hope it will help some of you
locked behind a corporate proxy.

[kitchen-ansible-pr]: https://github.com/neillturner/kitchen-ansible/pull/150
[molecule]: https://github.com/metacloud/molecule
[docker-doc]: https://docs.docker.com/config/daemon/systemd/#httphttps-proxy
[docker-proxy-gist]: https://gist.github.com/wenlock/1d806c9eb668ffaa2f64fa8692967359
