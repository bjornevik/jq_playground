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

## Construct new JSON from existing JSON

You can use `pipe` _within_ the `jq`-query!

`jq '.[] | {message: .commit.message}' < big.json > outputs/invalid_messages.json`

- `' [..] '` `jq`-query is contained in quotes
- `.[]` selects the entire array
- `|` pipes the array to the next part of the `jq`-query
- `[{ message: .commit.message }]` creates new JSON that has 1 field `message` set to the value of the `commit.message` field in the original JSON.

This will **only** return the lines in an invalid format, see [invalid_messages.json](outputs/invalid_messages.json) for result.
If you'd like to output a valid JSON-array you can wrap the entire query (the part enclosed in `''`) with square brackets:

`jq '[.[] | {message: .commit.message}]' < big.json > outputs/valid_messages.json`

See [valid_messages.json](outputs/valid_messages.json) for result.
