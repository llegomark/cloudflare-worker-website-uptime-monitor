# Cloudflare Worker - Website Uptime Monitor

This Cloudflare Worker monitors the uptime of a list of websites and sends email and Discord notifications when a website is down or encounters an error. It utilizes Cloudflare Email Workers to send email notifications and integrates with Discord to send messages to a specified channel and user. The Worker sends an immediate notification when a website is detected as down and also sends regular status reports at configurable intervals.

## Features

- Monitors the uptime of multiple websites at configurable intervals
- Retries failed requests with exponential backoff
- Sends an immediate email notification when a website is detected as down
- Sends a regular status report email at configurable intervals, regardless of the website status
- Sends an emergency message to a Discord channel when a website is down
- Sends a direct message to a specified Discord user when a website is down
- Logs website status and errors to Cloudflare KV storage
- Utilizes Cloudflare Email Workers for sending email notifications
- Integrates with Discord API to send notifications
- Configurable test mode for testing the Discord functionality without actual website downtime

## Prerequisites

Before deploying and using this Cloudflare Worker, ensure that you have the following:

- A Cloudflare account with the Workers feature enabled
- Cloudflare Email Workers enabled on your account (see [Enabling Email Workers](#enabling-email-workers) for more information)
- A configured email provider for sending emails through Cloudflare Email Workers
- A Discord bot token and the necessary permissions to send messages to a channel and user

## Configuration

1. Clone this repository or create a new Cloudflare Worker project.
2. Update the `config` object in the `index.js` file with your desired configuration:
   ```javascript
   const config = {
   	websites: [
   		'https://example1.com',
   		'https://example2.com',
   		// Add more website URLs to monitor
   	],
   	emailInterval: 60 * 60 * 1000, // 60 minutes in milliseconds
   	discordReportInterval: 5 * 60 * 1000, // 5 minutes in milliseconds
   	maxRetries: 3,
   	retryDelayBase: 5000, // 5 seconds in milliseconds
   	retryDelayExponent: 2,
   	senderName: 'Website Uptime Monitor',
   	senderEmail: 'your-email@example.com',
   	recipientEmail: 'recipient@example.com',
   	emailSubjectPrefix: '[Uptime Monitor]',
   	testMode: false,
   };
   ```
   - `websites`: An array of website URLs to monitor.
   - `emailInterval`: The interval at which to send email notifications (in milliseconds).
   - `discordReportInterval`: The interval at which to send status reports to Discord (in milliseconds).
   - `maxRetries`: The maximum number of retries for failed requests.
   - `retryDelayBase`: The base delay between retries (in milliseconds).
   - `retryDelayExponent`: The exponent for exponential backoff delay calculation.
   - `senderName`: The name of the email sender.
   - `senderEmail`: The email address of the sender.
   - `recipientEmail`: The email address of the recipient.
   - `emailSubjectPrefix`: The prefix to add to the email subject.
   - `testMode`: Set to `true` to enable test mode, which sends Discord notifications regardless of actual website downtime.
3. Configure the `wrangler.toml` file with your Cloudflare account details and the necessary bindings:

   ```toml
   name = "worker-meme-llego-website-uptime-monitor"
   main = "src/index.js"
   compatibility_date = "2024-04-07"
   workers_dev = true
   node_compat = true

   kv_namespaces = [
     { binding = "UPTIME_LOGS", id = "your-kv-namespace-id" }
   ]

   send_email = [
     { type = "send_email", name = "EMAIL_BINDING", destination_address = "recipient@example.com" }
   ]

   [triggers]
   crons = [ "*/1 * * * *" ]

   [vars]
   DISCORD_BOT_TOKEN = "YOUR_DISCORD_BOT_TOKEN"
   DISCORD_WEBHOOK_URL = "YOUR_DISCORD_WEBHOOK_URL"
   DISCORD_USER_ID = "YOUR_DISCORD_USER_ID"
   ```

   - Replace `your-kv-namespace-id` with the ID of your Cloudflare KV namespace.
   - Update the `destination_address` in the `send_email` binding with the recipient email address.
   - Adjust the `crons` trigger to set the desired frequency for running the uptime checks.
   - Replace `YOUR_DISCORD_BOT_TOKEN`, `YOUR_DISCORD_WEBHOOK_URL`, and `YOUR_DISCORD_USER_ID` with your actual Discord bot token, webhook URL, and user ID, respectively.

## Enabling Email Workers

To use Cloudflare Email Workers for sending email notifications, you need to enable Email Workers on your Cloudflare account. Follow these steps:

1. Log in to the Cloudflare dashboard.
2. Navigate to the "Workers" section.
3. Click on the "Email Workers" tab.
4. Follow the instructions provided in the Cloudflare documentation to enable Email Workers: [Enable Email Workers](https://developers.cloudflare.com/email-routing/email-workers/enable-email-workers/)

Once Email Workers is enabled, you can configure your email provider and use the Email Workers functionality in this Cloudflare Worker.

## Deployment

1. Install the Cloudflare Workers CLI (wrangler) if you haven't already:
   ```bash
   npm install -g @cloudflare/wrangler
   ```
2. Authenticate wrangler with your Cloudflare account:
   ```bash
   wrangler login
   ```
3. Deploy the Cloudflare Worker:
   ```bash
   wrangler deploy
   ```
   The Worker will be deployed to your Cloudflare account, and the uptime monitoring will start according to the configured schedule.

## Logging and Monitoring

The Cloudflare Worker logs the status of each website and any encountered errors to the Cloudflare KV storage. You can access the logs using the Cloudflare dashboard or the Cloudflare API.

The Worker sends email notifications to the configured recipient email address when a website is down or encounters an error. The email includes the website URL, status code, and any error messages.

Additionally, the Worker sends notifications to a Discord channel and a specified Discord user when a website is down. It uses the Discord API to send messages and create a direct message channel with the user.

## Contributing

Contributions are welcome! If you find any issues or have suggestions for improvements, please open an issue or submit a pull request.

## License

This project is licensed under the [MIT License](LICENSE).

## Acknowledgments

- [Cloudflare Workers](https://developers.cloudflare.com/workers/)
- [Cloudflare Email Workers](https://developers.cloudflare.com/email-routing/email-workers/)
- [Cloudflare KV](https://developers.cloudflare.com/kv/)
- [Discord API](https://discord.com/developers/docs/intro)
