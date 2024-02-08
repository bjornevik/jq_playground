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
- `> big_copy.json` writes to [big_copy.json](big_copy.json).

### From pipe (cURL)

[small.json](small.json) contains the last 5 commits to the jq repo.

`curl 'https://api.github.com/repos/jqlang/jq/commits?per_page=10 | jq '.' > small.json`

- `curl X | jq` pipes the output of curl to jq
- `'.'` formats the json.
- `> small.json` writes to [small.json](small.json)

## Compact the JSON

Use the flag `-c`

`jq '.' -c < big.json`

See [compacted.json](outputs/compacted.json) for result.

## Construct new file from existing JSON

### Each line = value of a field

You can use `pipe` _within_ the `jq`-filter!

`jq '.[] | .commit.message' < big.json`

- `''` `jq`-filter is contained in quotes
- `.[]` selects the entire array
- `|` pipes the array to the next part of the `jq`-filter
- `.commit.message` creates a new file where each line is equal to .commit.message

See [messages](outputs/messages) for result.

### Valid JSON

> [!NOTE]
> If you'd like to output a valid JSON-array that has to be specified in the `jq`-filter:

`jq '[.[] | {message: .commit.message}]' < big.json`

See [valid_messages.json](outputs/valid_messages.json) for result.

## Filter queries

You can also query the JSON through the filter.

Say we want every commit where the authors name is shorter than 7 characters

`jq '.[] | select(.commit.author.name | length < 7)' < big.json` ([short_names.json](outputs/short_names.json))

Or every commit where the authors name contains `Nico`

`jq '.[] | select(.commit.author.name | ascii_downcase | contains("nico"))' < big.json` ([nico.json](outputs/nico.json))

Or commits where the authors name is shorter than 7 characters AND contains an e

`jq '.[] | select((.commit.author.name | length < 7) and (.commit.author.name | ascii_downcase | contains("e")))' < big.json` ([short_e.json](outputs/long_nicos.json))

## Handling "invalid" JSON

You can still often format and query JSON that's "invalid".

### Format

```
jq -c '.[] | {message: .commit.message}' < big.json | jq '.'
```

1. The first jq command creates a compacted output where each line is `{"message": "example"}`
2. The second formats it. Output found in [prettified_invalid](outputs/prettified_invalid.json).

### Filter

```
jq '.[] | {message: .commit.message, sha: .sha}' < big.json | jq '.sha'
```

1. The first jq command creates a compacted output where each line is `{"message": "example", "sha": "123afb"}`
2. The second selects all values from the sha-field. Output in [shas_from_invalid](outputs/shas_from_invalid).

## List all keys

`jq '.[] | keys' < big.json`

Will output all **top-level** keys. Output in [outputs/all_keys](outputs/all_keys)
