# Pi-hole Docker with DNS over HTTPS

This is a docker-compose file for setting up pi-hole using DNS over HTTPS.
We have two containers - `pihole` itself, and [`cloudflared`](https://github.com/crazy-max/docker-cloudflared).

There are four DNS servers involved here.  First, your upstream DNS server which
is going to serve your DNS requests.  Second, cloudflared acts as a DNS proxy
to send DNS requests over HTTPS to your upstream server.  Third, your home
router has a DNS server which is used to assign names to local machines
(e.g. "mymac.local").  Finally, pihole acts as a DNS server which forwards requests
to the router and to cloudflared, and also blocks certain requests.

The basic idea here is to setup cloudflared to send out DNS requests to our upstread
provider, to setup pihole to forward requests to cloudflared and to also conditionally
forward requests for ".local" domains to the router.  Finally we need to set up
the router to advertise the pihole as the DNS server for both ipv4 and ipv6 clients.

## Setup

* Copy `.env.sample` to `.env` and fill in appropriate variables.
* On your router, set these dnsmasq settings to advertise only the pihole server
to clients.  If you're using FreshTomato, this is under "Advanced Settings"
=&gt; "DHCP/DNS" =&gt; "Dnsmasq Custom configuration".

```
dhcp-option=option:dns-server,PIHOLE_IP
dhcp-option=option6:dns-server,PIHOLE_IPV6
```

* If you're installing on Ubuntu, you'll need to disable systemd-resolved.
  See details [here](https://github.com/pi-hole/docker-pi-hole/#installing-on-ubuntu).
* Run `start.sh` to bring up pihole.
* Run `pihole-passwd.sh` to reset the admin password.

## Scripts

* `start.sh` - Create directories and run `docker-compose up -d`.
* `pihole-passwd.sh` - Reset the admin password.
* `pihole-flush-dns.sh` - Force pihole to flush it's DNS cache.

## Troubleshooting

If you run into problems, try running:

```sh
sudo docker exec -it pihole pihole -d
```
