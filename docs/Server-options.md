# Configuration
TabbyAPI primarily uses a config.yml file to adjust various options. This is the preferred way and has the ability to adjust all options of TabbyAPI.

CLI arguments are also included, but those serve to *override* the options set in config.yml. Therefore, they act a bit differently compared to other programs, especially with booleans.
Example: A user sets `gpu_split_auto` to True. The CLI arg will then be `--gpu_split-auto False` to override that previous config.yml setting.

In addition, some config.yml options are too complex to represent as command args, so those are not included with the argparser.

All of these options have descriptive comments above them. You should not need to reference this documentation page unless absolutely necessary.

### Networking Options
| Config Option   | Type (Default)         | Description                                                  |
|-----------------|------------------------|--------------------------------------------------------------|
| host            | String (127.0.0.1)     | Set the IP address used for hosting TabbyAPI                 |
| port            | Int (5000)             | Set the TCP Port use for TabbyAPI                            |
| disable_auth    | Bool (False)           | Disables API authentication                                  |
| send_tracebacks | Bool (False)           | Send server tracebacks to client.<br><br>Note: It's not recommended to enable this if sharing the instance with others. |
| api_servers     | List[String] (["OAI"]) | API servers to enable. Possible values `"OAI", "Kobold"`     |

### Logging Options
Note: With CLI args, all logging parameters are prefixed by `log-`. For example, `prompt` will be `--log-prompt true/false`.

| Config Option     | Type (Default) | Description                                            |
|-------------------|----------------|--------------------------------------------------------|
| prompt            | Bool (False)   | Logs prompts to the console                            |
| generation_params | Bool (False)   | Logs request generation options to the console         |
| requests          | Bool (False)   | Logs a request's URL, Body, and Headers to the console |

### Sampling Options
Note: This block is for sampling overrides, not samplers themselves.

| Config Option   | Type (Default) | Description                                                  |
|-----------------|----------------|--------------------------------------------------------------|
| override_preset | String (None)  | Startup the given sampler override preset in the sampler_overrides folder |

### Developer Options
Note: These are experimental flags that may be removed at any point.

| Config Option             | Type (Default) | Description                                                  |
|---------------------------|----------------|--------------------------------------------------------------|
| unsafe_launch             | Bool (False)   | Skips dependency checks on startup. Only recommended for debugging. |
| disable_request_streaming | Bool (False)   | Forcefully disables streaming requests                       |
| cuda_malloc_backend       | Bool (False)   | Uses pytorch's CUDA malloc backend to load models. Helps save VRAM.<br><br>Safe to enable. |
| uvloop                    | Bool (False)   | Use a faster asyncio event loop. Can increase performance.<br><br>Safe to enable. |
| realtime_process_priority | Bool (False)   | Set the process priority to "Realtime". Administrator/sudo access required, otherwise the priority is set to the highest it can go in userland. |

### Model Options
Note: Most of the options here will only apply on initial model load/startup (ephemeral). They will not persist unless you add the option name to `use_as_default`.

| Config Option         | Type (Default)    | Description                                                  |
|-----------------------|-------------------|--------------------------------------------------------------|
| model_dir             | String ("models") | Directory to look for models.<br><br>Note: Persisted across subsequent load requests |
| use_dummy_models      | Bool (False)      | Send a dummy OAI model card when calling the `/v1/models` endpoint. Used for clients which enforce specific OAI models.<br><br>Note: Persisted across subsequent load requests |
| model_name            | String (None)     | Folder name of a model to load. The below parameters will not apply unless this is filled out. |
| use_as_default        | List[String] ([]) | Keys to use by default when loading models. For example, putting `cache_mode` in this array will make every model load with that value unless specified by the API request.<br><br>Note: Also applies to the `draft` sub-block |
| max_seq_len           | Float (None)      | Maximum sequence length of the model. Uses the value from config.json if not specified here. |
| override_base_seq_len | Float (None)      | Overrides the base sequence length of a model. You probably don't want to use this. max_seq_len is better.<br><br>Note: This is only required for automatic RoPE alpha calculation AND if the model has an incorrect base sequence length (ex. Mistral 7b) |
| tensor_parallel       | Bool (False)      | Use tensor parallelism to load the model. This ignores the value of gpu_split_auto. |
| gpu_split_auto        | Bool (True)       | Automatically split the model across multiple GPUs. Manual GPU split isn't used if this is enabled. |
| autosplit_reserve     | List[Int] ([96])  | Amount of empty VRAM to reserve when loading with autosplit.<br><br>Represented as an array of MB per GPU used. |
| gpu_split             | List[Float] ([])  | Float array of GBs to split a model between GPUs.            |
| rope_scale            | Float (1.0)       | Adjustment for rope scale (or compress_pos_emb)              |
| rope_alpha            | Float (None)      | Adjustment for rope alpha. Leave blank to automatically calculate based on the max_seq_len. |
| cache_mode            | String ("FP16")   | Cache mode for the model.<br><br>Options: FP16, Q8, Q6, Q4   |
| cache_size            | Int (max_seq_len) | <br>Note: If using CFG, the cache size should be 2 * max_seq_len. |
| chunk_size            | Int (2048)        | Amount of tokens per chunk with ingestion. A lower value reduces VRAM usage at the cost of ingestion speed. |
| max_batch_size        | Int (None)        | The absolute maximum amount of prompts to process at one time. This value is automatically adjusted based on cache size. |
| prompt_template       | String (None)     | Name of a jinja2 chat template to apply for this model. Must be located in the `templates` directory. |
| num_experts_per_token | Int (None)        | Number of experts to use per-token for MoE models. Pulled from the config.json if not specified. |
| fasttensors           | Bool (False)      | Possibly increases model loading speeds.                     |

### Draft Model Options

| Config Option    | Type (Default)    | Description                                                  |
|------------------|-------------------|--------------------------------------------------------------|
| draft_model_dir  | String ("models") | Directory to look for draft models.<br><br>Note: Persisted across subsequent load requests |
| draft_model_name | String (None)     | String: Folder name of a draft model to load.                |
| draft_rope_scale | Float (1.0)       | String: RoPE scale value for the draft model.                |
| draft_rope_alpha | Float (1.0)       | RoPE alpha value for the draft model. Leave blank for auto-calculation. |
| draft_cache_mode | String ("FP16")   | Cache mode for the draft model.<br><br>Options: FP16, Q8, Q6, Q4 |

### Lora Options

| Config Option | Type (Default)   | Description                                                  |
|---------------|------------------|--------------------------------------------------------------|
| lora_dir      | String ("loras") | Directory to look for loras.<br><br>Note: Persisted across subsequent load requests |
| loras         | List[loras] ([]) | List of lora objects to apply to the model. Each object contains a name and scaling. |
| name          | String (None)    | Folder name of a lora to load.<br><br>Note: An element of the `loras` key |
| scaling       | Float (1.0)      | "Weight" to apply the lora on the parent model. For example, applying a lora with 0.9 scaling will lower the amount of application on the parent model.<br><br>Note: An element of the `loras` key |

### Embeddings Options
Note: Most of the options here will only apply on initial embedding model load/startup (ephemeral).

| Config Option        | Type (Default)    | Description                                                  |
|----------------------|-------------------|--------------------------------------------------------------|
| embedding_model_dir  | String ("models") | Directory to look for embedding models.<br><br>Note: Persisted across subsequent load requests |
| embeddings_device    | String ("cpu")    | Device to load an embedding model on.<br><br>Options: cpu, cuda, auto<br><br>Note: Persisted across subsequent load requests |
| embedding_model_name | String (None)     | Folder name of an embedding model to load using infinity-emb. |
