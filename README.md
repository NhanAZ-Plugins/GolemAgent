# GolemAgent

GolemAgent is a PocketMine-MP and Axolotl-PM companion plugin for driving
server-side simulated players in a disposable development server.

This repository contains only the PHP companion plugin. It is not a Bedrock
network client and it does not join public servers. It does not provide Xbox
authentication, RakNet transport, resource-pack transfer validation, retail
client rendering, or public-server release evidence.

Use it when a plugin test needs the real server-side `Player` API, PocketMine
events, forms, inventories, permissions, worlds, scheduler ticks, or custom
plugin services without opening a Minecraft client. Keep the server, world,
plugin data directory, and trusted test scripts disposable and private.

## Runtime

The companion requires the pinned Golem 0.7.0 runtime. The runtime source is
not bundled in this repository. Build it from the reviewed upstream revision,
apply `golem-0.7.0.patch`, and build both PHAR files:

```powershell
git clone https://github.com/achedon12/golem.git .local/golem-build/runtime
git -C .local/golem-build/runtime checkout --detach 18c86ea25b1d67fa9ceb6079c46ade2f8f99fc31
$patch = (Resolve-Path golem-0.7.0.patch).Path
git -C .local/golem-build/runtime apply --check $patch
git -C .local/golem-build/runtime apply $patch
php -d phar.readonly=0 .local/golem-build/runtime/.github/scripts/build-phar.php .local/golem-build/Golem.phar
php -d phar.readonly=0 build.php .local/golem-build/GolemAgent.phar
```

Install `Golem.phar` and `GolemAgent.phar` into the disposable server's
`plugins` directory. GolemAgent verifies the runtime source against
`runtime-lock.json` before enabling.

## Data contract

On startup, the plugin creates a private data directory containing
`ready.json`, `requests`, `responses`, `scripts`, and an append-only simulation
`journal-BOOT.jsonl`. A compatible CLI can publish one JSON request at a time,
observe the structured response, and retain the journal as server-side
simulation evidence.

The mailbox grants administrative authority to anyone who can write the data
directory. Never expose it through a web server or place credentials in
trusted scripts. Trusted PHP scripts run with plugin-level server authority
and are not a sandbox.

The request and response schema is `bedrockdebugproxy.golem.v1`. The original
Go bridge and its scenario documentation remain in the BedrockDebugProxy
history while the network-client Golem replacement is developed there.

## License and provenance

GolemAgent source is licensed under the GNU GPL version 3 or later. The
`GOLEM-LICENSE.txt` file preserves the MIT license for the separate upstream
Golem runtime. The patch and runtime lock document the exact dependency
revision and expected source hashes.

This project was split from the `dev/golem-agent` subtree of
[BedrockDebugProxy](https://github.com/NhanAZ/BedrockDebugProxy) so its PHP
server companion can evolve independently from the Go Bedrock protocol client.
