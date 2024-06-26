# relayer-keepalive

This is a script to keep  IBC
light clients alive using the open-source IBC relayer
[Hermes](https://hermes.informal.systems).

See the IBC-Go docs on [light client
pauses](https://ibc.cosmos.network/main/ibc/proposals.html). Essentially, if a
client is not used for a period of time (its "trust period"), it will be paused,
requiring a governance proposal to restart it. This script will check the light
clients specified and use Hermes to update recently inactive clients, keeping
them alive to prevent needing to restart them via governance proposals. If it
fails to do so for any reason, it will send a notification to a Discord channel
for troubleshooting.

## Usage

1. Install [Hermes](https://hermes.informal.systems) and configure it.

2. Install the dependencies:

   ```sh
   npm install
   ```

3. Create `config.toml` from `config.toml.example` and fill in the values,
   configuring the same chains as in your Hermes config as well as the polytone
   client connections to keep alive. Chain names must match known chain names
   from the [Chain Registry](https://github.com/cosmology-tech/chain-registry)'s
   list of chains in
   [chains.ts](https://github.com/cosmology-tech/chain-registry/blob/main/packages/chain-registry/src/chains.ts).

   ```toml
   [[chains]]
   name = "<CHAIN A NAME>"
   rpc = "<CHAIN A RPC>"
   notify_balance_threshold = 1000000

   [[chains]]
   name = "<CHAIN B NAME>"
   rpc = "<CHAIN B RPC>"
   notify_balance_threshold = 1000000

   [[connections]]
   chain_a = "<CHAIN A NAME>"
   client_a = "<CHAIN A IBC CLIENT>"

   chain_b = "<CHAIN B NAME>"
   client_b = "<CHAIN B IBC CLIENT>"
   ```

4. Create a Discord webhook by following this guide:

   https://discordjs.guide/popular-topics/webhooks.html#creating-webhooks

   Then, add the webhook URL to `config.toml`.

5. Run the script:

   ```sh
   npm run keepalive
   ```

   Set up a cron job to run this script periodically. For example, to run it
   every 3 days:

   ```sh
   0 0 */3 * * cd /path/to/polytone-keepalive && npm run keepalive
   ```