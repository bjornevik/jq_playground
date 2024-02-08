# jq_playground

Repo for playing around with [jq](https://github.com/jqlang/jq).

## Basics

### From local file

[big.json](big.json) contains the last 1000 commits to the jq repo.

`jq '.' < big.json > big_copy.json`.

- `'.'` formats the json.
- `< big.json` [big.json](big.json) as input.
- `> copy.json` writes to [big_copy.json](big_copy.json).

### From pipe (cURL)

[small.json](small.json) contains the last 5 commits to the jq repo.

`curl 'https://api.github.com/repos/jqlang/jq/commits?per_page=10 | jq '.' > small.json`

- `curl X | jq` pipes the output of curl to jq
- `'.'` formats the json.
- `> small.json` writes to [small.json](small.json)

## Compact the JSON

Use the flag `-c`

`jq '.' -c < big.json > outputs/compacted.json`

See [compacted.json](outputs/compacted.json) for result.
