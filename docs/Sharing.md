# Remote Access
This page of the documentation serves to illustrate the in-between case of exposing a local instance without having to pay for a domain and set up a complicated reverse proxy.

### Ngrok
Ngrok is the recommended method for sharing a local instance over the internet. Throughput is faster than the alternatives, but there are limits on their free tier.

To get started:
1. Sign up and install ngrok using instructions from [their website](https://ngrok.com/docs/getting-started/)
   1. The ngrok for Windows install uses chocolatey, a package manager that isn't installed on Windows by default. Instead, use the winget package manager to install ngrok instead by running `winget install --id=Ngrok.Ngrok -e`
2. Start your TabbyAPI instance
3. In a separate terminal, open an ngrok instance using the command `ngrok http 5000` (or the port that TabbyAPI is running on)
4. Copy the public URL that shows up and send that to your users.

### Cloudflared
Cloudflared is a free local tunneling service provided by Cloudflare. Free URLs do not have restrictions, but clients will have slower throughput.

To get started:
1. Download the cloudflared binary from [their releases](https://github.com/cloudflare/cloudflared/releases)
2. Open a terminal to where you downloaded cloudflared.
3. Start your TabbyAPI instance
4. Run `./cloudflared tunnel --url 127.0.0.1:5000` to start a quick tunnel on that port
5. Copy the provided HTTPS url and send that to your users.

### Tailscale
Tailscale is a product that uses the WireGuard protocol to provide a mesh network VPN for connecting your devices anywhere you go. Think of it as a private LAN that's accessible from anywhere.

!!! note
    This is not a method for exposing your TabbyAPI instance to the world. If you want that, use the other two services or look into tailscale funnels.

To get started:
1. Set your TabbyAPI IP to `0.0.0.0` otherwise you will not be able to access your instance outside your local machine.
2. Sign up and get started on [Tailscale's website](https://tailscale.com/), then install the client.
3. Connect to your tailscale account on both your host and client machine.
4. Select the Tailscale icon (usually in the system tray) and get the name of your host device. This is usually the same as the hostname.
5. You can now access your TabbyAPI instance via `hostname:5000` instead of localhost as long as your are connected to your tailnet.