# Run Z3 regtest — DeSaint007

Upstream pinned commit: `e84ce9fd8e864ff0b2a8a62f6ce14392145db0fb`.

## What changed and why
I added `docker-compose.quest.yml`, an opt-in overlay binding Zebra's RPC
and health ports to `127.0.0.1`. The original published ports listened on all
interfaces. Local development needs no access from other machines, so this
keeps the regtest RPC confined to this laptop. It changes no mainnet settings.

## Reproduce
```sh
./scripts/regtest-init.sh
docker compose --env-file .env.regtest -f docker-compose.yml -f docker-compose.regtest.yml -f docker-compose.quest.yml up -d zebra
docker compose --env-file .env.regtest -f docker-compose.yml -f docker-compose.regtest.yml -f docker-compose.quest.yml ps zebra
curl --user zebra:zebra -H 'Content-Type: application/json' --data '{"jsonrpc":"2.0","method":"getblockchaininfo","params":[],"id":1}' http://127.0.0.1:29232
```

Validation: initialization completed; Zebra was restarted with the overlay;
its published ports became localhost-only; `generate [3]` returned three
block hashes; `getblockchaininfo` returned block height 10. The RPC reports
`chain: test` for this regtest configuration; the Compose environment and
on-demand generated blocks establish that this is local regtest.

No real funds were used. AI assisted with the overlay and documentation.
