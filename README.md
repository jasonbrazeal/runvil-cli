# runvil-cli

`runvil` runs any runvil prompt from the command line through the
runvil gateway. It is the generic form of `paperweight`: instead of one
embedded prompt and a paper-store lookup, it takes any prompt file and any
input file.

Every run has these tools available:

- `web/search` and `web/fetch`, through the gateway.
- One MCP Plugin per server in `mcp.json`, named after the server, so the
  tools of a server named `wg21-papers` are `wg21-papers/<tool>`.
- `runvil/subagents`.

## Build

`runvil` builds against a runvil checkout beside this repository, at
`../runvil`.

```
cargo build --release
```

## Run

`RUNVIL_GATEWAY_URL` and `RUNVIL_GATEWAY_API_KEY` must be set.

```
runvil <PROMPT> [--input <PATH>] [--args <TEXT>] [--output <PATH>] [--model <ID>] [--mcp <PATH>] [--verbose]
```

- `--input` is written to the prompt's declared `input:` file.
- `--args` is the run's argument text: a JSON object for a prompt that
  declares `args:`, or plain text.
- The prompt's declared `output:` file goes to stdout, or to `--output`. A
  prompt that declares no output writes the run's final text.
- `--model` picks the gateway model; the default is the first chat model the
  gateway lists.
- `--mcp` names the `mcp.json` file. Without it, `runvil` uses the first
  `mcp.json` beside the executable, in the working directory, or in
  `~/.runvil`. Values may use `${env:NAME}`.
- Without `--verbose` only the output and any error are printed.
  `--verbose` prints the MCP file in use and every run event, with every
  tool call, tool result, Lua log, and model reply, to stderr.

Ctrl-C cancels the run and exits with status 130.

## Examples

```
runvil prompts/hello.md
```

`prompts/paper-category-classifier.md` reads a WG21 paper by id through the
paper-mcp server and prints `process paper = true` or `process paper = false`.
Copy `mcp.example.json` to `mcp.json`, make sure `PAPER_MCP_TOKEN` is set,
and run:

```
runvil prompts/paper-category-classifier.md --args P4003R2
```

`prompts/paper-dossier.md` is a tour of the prompt language. It reads a paper
through paper-mcp, researches it in the archive and on the web, and writes a
one-page dossier:

```
runvil prompts/paper-dossier.md --args '{"paper": "P4003R2", "focus": "frame allocation"}'
```
