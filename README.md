# SGW (Simple Gateway)

![](https://a.uguu.se/KGf1Upd6kYik.png)

#### Overview

This playbook turns a Debian-based machine into a lightweight, functional
gateway to replace the crappy CPE-thingies ISPs tend to give you. Per default,
the following services are configured:

* DHCP (through `isc-dhcp-server`, configurable subnets, default is 10.9.8.0/24, the first 10 addresses being reserved)
* DNS (through `unbound`, DNSSEC-validating, configurable forward resolvers, defaults are [dns.watch](https://dns.watch) & [opennic.org](https://opennic.org))

**Important**: This role is generally ready-to-use, but not bugfree yet.
Prepare for minor glitches.

**Important**: This role does **not** pay attention to IPv6. Most ISPs still
don't give you native IPv6, and trying to accomodate DS-Lite would be
painstakingly annoying.



#### Requirements
Hardware:

* Two physical network adapters
* A modem configured as bridge, passing the IP-address assigned by your ISP through via DHCP to one of the adapters

Software:
* A debian-based machine with administrative access
* Set the IP-address of your internal interface to the first IP-address of your network segment, e.g. `10.9.8.1`
* Configure all the variables in `vars/main.yml`
* Create a `hosts`-file that contains the host that should be configured

Functionality has been tested on Debian (Jessie, Stretch) and Ubuntu (16.04,
18.04); it should work with most Debian-based distributions. If you don't have
prepared Ansible-compatibility on your remote host (or if you are unsure),
please make sure you execute the following command as root before attempting to execute
this playbook:

```
(test -e /usr/bin/python && test -d /etc/ca-certificates) || (apt -y update && apt install -y python-minimal python-apt ca-certificates unzip)
```

In case you are wondering what this does: It installs the bare minimum for Ansible to work.

#### Usage

`ansible-playbook -kK -i hosts site.yml`


#### FAQ
##### Why do you not deal with IPv6 here?
My reasons are right at the top of this document. I'm aware of things like
IPv6-tunnels, but if you know about those, chances are this isn't the right
playbook for you.
##### Why should I bother with running my own resolver?
Aside from the potential privacy / censorship implications when using Google,
Cloudflare or (much, much worse in most cases) the nameserver provided by your
ISP, running your own resolver allows you to do things like adblocking.
##### Why are the configuration files so short? There are more options!
Most of the defaults that come bundled with packaged software on Debian are
pretty sane. Relying on those to keep configurations concise and readable makes
sense.
##### Why do you enable TCP for DNS? DNS is UDP!
If you've ever dealt with DNSSEC-signed responses (which `unbound``cares
about), you know that you want TCP. If you haven't trust me here. You want TCP.
##### What about logs?
The configuration for the DHCP-logs has been left untouched, the logs for the
DNS-server have been minimized. If you want query logs (e.g. for IR-purposes)
you have to manually enable them yourselves.
