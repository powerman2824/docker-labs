You’ll also want to download the latest root hints into that same folder:

<pre>``` bash #!/bin/bash curl -o ./unbound/root.hints https://www.internic.net/domain/named.root ```</pre>

✅ Test Flow After Setup:

docker compose up -d

Visit http://<pi-zero-ip>/admin

Login with your WEBPASSWORD

Go to Settings → DNS → verify only 127.0.0.1#5335 is set.

From a client device, set your Pi Zero's IP as the DNS server.

Try a dig openai.com and verify it works.
