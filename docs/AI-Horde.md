# Kobold AI Horde
The [AI Horde](https://aihorde.net/) is a FOSS crowdsourced compute pool to serve models to users who are not able to access them. TabbyAPI is currently the only horde compatible LLM server that runs on Windows with parallel batching.

To get started, Horde requires an API key, which you can acquire [here](https://stablehorde.net/register)

The LLM branch of AI Horde does not use the OpenAI standard, but uses KoboldAI's API. Here are the steps to configure your TabbyAPI instance for hosting:

1. In `config.yml`, set the `api_servers` value to include `"Kobold"` which will enable the KoboldAI API.
2. Horde doesn't support API key authentication. Therefore, you need to enable `disable_auth` in `config.yml`
3. After those config changes, launch TabbyAPI as normal and your server should be Horde-ready.

Now, the horde needs a "worker" to interface between its servers and your TabbyAPI backend. To accomplish that, here are the steps:

1. Clone the AI horde worker repository
   1. `git clone https://github.com/Haidra-Org/AI-Horde-Worker`
2. Open the repository in your favorite editor and copy `bridgeData_template.yaml` to `bridgeData.yaml`. This will serve as your configuration file.

Afterwards, you'd need to adjust the options based on your preferences. Here are the recommended values to adjust:

- `api_key`: The Horde API key you registered for above.
- `max_threads`: How many requests should be run at once? A higher value should also use a higher batch size and cache size in Tabby.
- `scribe_name`: The name of your worker that's displayed in Horde.
- `kai_url`: The URL to your TabbyAPI backend. This will usually be `http://localhost:5000`
- `max_length`: The maximum number of tokens for every request. 512 is recommended.
- `max_context_length`: The maximum context length of the worker itself. It's recommended to set this to the model's `max_seq_len`.

Finally, launch the worker by running `.\horde-scribe-bridge.bat` or `./horde-scribe-bridge.sh` depending on your system.