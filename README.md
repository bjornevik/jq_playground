# jq_playground

Repo for playing around with [jq](https://github.com/jqlang/jq).

## Basics

> [!NOTE]
> This README.md outputs to files so that you can see the output without having to run the queries yourself.
> If no output file is specified `jq` will output to the console.

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

## Construct new file from existing JSON

### Each line = value of a field

You can use `pipe` _within_ the `jq`-filter!

`jq '.[] | .commit.message' < big.json > outputs/invalid_messages`

- `''` `jq`-filter is contained in quotes
- `.[]` selects the entire array
- `|` pipes the array to the next part of the `jq`-filter
- `.commit.message` creates a new file where each line is equal to .commit.message

### Valid JSON

> [!NOTE]
> If you'd like to output a valid JSON-array that has to be specified in the `jq`-filter:

`jq '[.[] | {message: .commit.message}]' < big.json > outputs/valid_messages.json`

See [valid_messages.json](outputs/valid_messages.json) for result.

## Handling "invalid" JSON

You can still often format and query JSON that's "invalid".

### Format

```
jq -c '.[] | {message: .commit.message}' < big.json \                                                                                                                                    ─╯
| jq '.' > outputs/prettified_invalid.json
```

1. The first jq command creates a compacted output where each line is `{"message": "example"}`
2. The second formats it and outputs to [prettified_invalid](outputs/prettified_invalid.json).

### Filter

```
jq '.[] | {message: .commit.message, sha: .sha}' < big.json | jq '.sha' > outputs/shas_from_invalid
```

1. The first jq command creates a compacted output where each line is `{"message": "example", "sha": "123afb"}`
2. The second creates a new file where each line is a value from the sha field: [shas_from_invalid](outputs/shas_from_invalid).

## List all keys

`jq '.[] | keys' < big.json > outputs/all_keys`

Will output all **top-level** keys to [outputs/all_keys](outputs/all_keys)
