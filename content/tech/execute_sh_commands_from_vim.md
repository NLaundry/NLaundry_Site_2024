
+++
title = "Execute shell commands from inside Vim"
date = 2024-02-25T20:09:26-05:00
draft = true
tags = ["CLI", "Vim"]
+++

visually seclect a set of lines

```sh
task add "REBUILD: server with Debian" project:home_lab.server_redo
task add "CONFIGURE: fresh Debian server with basic settings - user, sudo, etc" project:home_lab.server_redo
task add "SETUP: tailscale" project:home_lab.server_redo
task add "CONFIGURE: Syncthing for DB and Nvim" project:home_lab.server_redo
task add "ADJUST: pihole configurations for more logical IP addresses" project:home_lab.server_redo
task add "SETUP: pihole" project:home_lab.server_redo

task add "CONFIGURE: dynamics DNS" project:home_lab.self_hosting
task add "SETUP: dockerize Hugo site" project:home_lab.self_hosting
task add "SETUP: Docker subnet for website" project:home_lab.self_hosting
task add "SETUP: Port Forwarding" project:home_lab.self_hosting
task add "SETUP: Traefik for TLS/SSL + Lets Encrypt" project:home_lab.self_hosting
task add "CONFIGURE: UFW Firewall to only allow 443 and 80 (I think) for http and https" project:home_lab.self_hosting
task add "CONFIGURE: Domain Name with NameCheap" project:home_lab.self_hosting

task add "READ: more on Traefik's capabilities" project:home_lab.monitoring.Traefik
task add "READ: Portainer" project:home_lab.monitoring.Portainer
task add "READ: Grafana" project:home_lab.monitoring.Grafana
task add "READ: Prometheus" project:home_lab.monitoring.Prometheus

```

hitting colon to enter ex mode with a visual selection active gives you

```vimscript
:'<,'>
```

Which basically says "I've set a mark at the start of the visual selection and at the end to create a range. Whatever you do next in this ex command will act on that range."

Then we use the `!` ex command to say "hey, do this externally on the shell"  we can do `!curl https://nathanlaundry.com` for example to execute a curl, or even more fun `r! curl https://nathanlaundry.com` to pipe stdout into a temp buffer.


in this case we'll use ! to pipe the text between '< and '> (the markers for start and end of a visual selection), into sh so that it executes everything in this range as shell commands

```vimscript
:'<,'>! sh
```

This will replace the contents of this visual range with the result of the execution. If you want your commands instead of the result in the buffer, just hit `u` to undo the insertion.
