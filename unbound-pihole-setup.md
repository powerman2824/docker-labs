Create the directory and config file:

<pre>
  mkdir -p ~/pihole-unbound/unbound
  cd ~/pihole-unbound/unbound
  nano unbound.conf
</pre>
unbound.conf
<pre>
  server:
  verbosity: 1
  interface: 127.0.0.1
  port: 5335
  do-ip4: yes
  do-udp: yes
  do-tcp: yes
  access-control: 127.0.0.0/8 allow
  root-hints: "/etc/unbound/root.hints"
  harden-glue: yes
  harden-dnssec-stripped: yes
  cache-min-ttl: 3600
  cache-max-ttl: 86400
</pre>
docker-compose.yaml
<pre>
  version: "3"

services:
  unbound:
    image: mvance/unbound:arm32v7
    container_name: unbound
    volumes:
      - './unbound:/etc/unbound'
    restart: unless-stopped
    network_mode: host

  pihole:
    image: pihole/pihole:latest
    container_name: pihole
    depends_on:
      - unbound
    environment:
      TZ: 'America/New_York'  # <- fixed the timezone format
      WEBPASSWORD: 'changeme'  # change this!
      DNS1: 127.0.0.1#5335     # <- use localhost to reach Unbound in host mode
      DNS2: 127.0.0.1#5335
    volumes:
      - './etc-pihole:/etc/pihole'
      - './etc-dnsmasq.d:/etc/dnsmasq.d'
    cap_add:
      - NET_ADMIN
    restart: unless-stopped
    network_mode: host

</pre>

You’ll also want to download the latest root hints into that same folder:

<pre>
  curl -o ./unbound/root.hints https://www.internic.net/domain/named.root 
</pre>

✅ Test Flow After Setup:

docker compose up -d

Visit http://<pi-zero-ip>/admin

Login with your WEBPASSWORD

Go to Settings → DNS → verify only 127.0.0.1#5335 is set.

From a client device, set your Pi Zero's IP as the DNS server.

Try a dig google.com and verify it works.
