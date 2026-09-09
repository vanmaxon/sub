# subconverter

Convert proxy subscriptions and individual node links into client-ready configurations. The project provides a small HTTP service, a C++20 conversion core, reusable base templates, and release builds for Linux, macOS, Windows, and Docker.

[中文文档](README-cn.md) · [Docker guide](README-docker.md) · [License](LICENSE)

![subconverter conversion flow](assets/subconverter-flow.svg)

## What it does

- Fetches subscription URLs, reads local configuration files, and accepts plain-text or Base64 subscription content.
- Parses common node and configuration formats, including SS/SSR, VMess, Trojan, VLESS, Clash, Surge, Quantumult, and more.
- Generates Clash/ClashR, Surge, Quantumult/Quantumult X, Loon, Surfboard, Mellow, sing-box, and simple subscription outputs.
- Applies rules, proxy groups, renaming, emoji, filtering, templates, and profiles from `base/`.

## Quick start

The service listens on port `25500` by default. Copy an example preference file before changing local settings:

```bash
cp base/pref.example.ini base/pref.ini
./subconverter -f "$(pwd)/base/pref.ini"
```

Confirm that it is running:

```bash
curl http://127.0.0.1:25500/version
```

Create a Clash subscription by URL-encoding the source URL, then requesting `/sub`:

```text
http://127.0.0.1:25500/sub?target=clash&url=https%3A%2F%2Fexample.com%2Fsubscription
```

For VLESS and VMess outputs, `udp=true` emits `udp: true` and defaults to `packet-encoding: xudp`. Use `xudp=true` or `xudp=false` to override that encoding independently.

## Targets and inputs

| Category | Supported values |
| --- | --- |
| Main targets | `clash`, `clashr`, `surge`, `quan`, `quanx`, `loon`, `surfboard`, `mellow`, `singbox` |
| Simple targets | `ss`, `ssr`, `ssd`, `sssub`, `v2ray`, `trojan`, `mixed` |
| Input forms | HTTP(S) URLs, local files, Base64 subscriptions, and plain-text node links |

Use `|` between multiple input URLs before URL-encoding the complete value. Remote plain-text responses may contain one node link per line, including `#remarks`.

## Configuration

`base/` is the runtime data directory:

- `pref.example.ini`, `pref.example.yml`, and `pref.example.toml` document server and node preferences.
- `config/`, `profiles/`, `rules/`, `snippets/`, and `base/` provide external configurations, reusable profiles, rulesets, fragments, and output templates.
- `generate.ini` defines artifact generation for `subconverter -g` and `--artifact <name>`.

Treat subscription URLs, API tokens, and Gist credentials as secrets. Keep them in untracked preference files or environment configuration; do not commit them to `base/`.

## Build and run

The project requires CMake, a C++20 compiler, libcurl, yaml-cpp, PCRE2, RapidJSON, toml11, QuickJS, and libcron. Configure and build a local release with:

```bash
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build --parallel
```

Platform release scripts in `scripts/` provision their dependencies and package the runtime `base/` directory. Use `scripts/build.alpine.release.sh`, `scripts/build.macos.release.sh`, or `scripts/build.windows.release.sh` to reproduce CI packaging. To use the maintained container image or build a customized image, follow the [Docker guide](README-docker.md).

## HTTP endpoints

| Endpoint | Purpose |
| --- | --- |
| `GET /version` | Health and version response |
| `GET` or `HEAD /sub` | Convert subscriptions and node links |
| `GET /getruleset` | Convert a ruleset |
| `GET /getprofile` | Render a configured profile |
| `GET /refreshrules` / `GET /readconf` | Refresh runtime rules or preferences; protect these when a token is configured |

See [README-cn.md](README-cn.md) for the complete parameter reference, profile syntax, ruleset configuration, and advanced examples.
