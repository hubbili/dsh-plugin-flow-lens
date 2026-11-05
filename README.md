---
description: "Read recorded Agent execution, prompts and extension references in a separate Harness conversation tab."
kind: "package-bundle"
---
# Flow Lens

English | [中文](README.zh.md)

## Build and install

Requires Node `^22.19.0 || >=24.0.0`, pnpm `11.7.0`, and an existing DeepSeek Harness Web installation. The plugin targets Harness `0.1.6-alpha.2`; its pre-stable APIs can change between host versions.

```sh
git clone https://github.com/hubbili/dsh-plugin-flow-lens.git
cd dsh-plugin-flow-lens
pnpm install --frozen-lockfile
pnpm run typecheck
pnpm run test
pnpm run lint
pnpm run build
pnpm pack --out artifacts/flow-lens.tgz
dsh plugin --profile web add ./artifacts/flow-lens.tgz
dsh web --no-open
```

For source-based `pnpm dsh` launches, run the equivalent commands in the Harness checkout using absolute archive and overlay paths. After changes, rebuild, repack, reinstall, and restart the host.

Install this plugin independently. The companion plugin is [dsh-plugin-llm-wire-debug](https://github.com/hubbili/dsh-plugin-llm-wire-debug).

![flow-lens preview](docs/assets/flow-overview.png)
![flow-lens preview](docs/assets/flow-prompt.png)

## Summary

Follow a selected turn through input, prompts, model requests, responses, tools, and continuation. Inspect highlighted prompts, graph nodes, raw events, and a curated extension map. Copy source paths for reading a separate Harness checkout in WebStorm. This view reads loaded Session records; it does not trace every function or plugin callback.

## Table of Contents

- [Use this package](#use-this-package)
- [Understand the implementation](#understand-the-implementation)
- [Model Experience](#model-experience)
- [Known Limitations and Deferred Work](#known-limitations-and-deferred-work)

## Use this package

Follow the repository's [build and local archive installation](#build-and-install), then select a recorded Session and open Flow Lens after Trajectory. Learning guide is the default; node deep links open the graph. The package's [bundle patch](cordis.patch.yml) adds only this view. JSON inspectors can contain private prompts, arguments and results; do not share real captured data.

## Understand the implementation

<details>
<summary>Implementation internals</summary>

The [projection](src/client/model.ts) reads the host's Trajectory snapshot. The [learning stages](src/client/learning-stages.ts) combine it with loaded lifecycle records so rejected or empty turns stay visible. Prompt snapshots show effective request inputs, not individual section contributors. Turn completion uses recorded `turn/end`; missing end records remain unknown. The [extension catalog](src/client/extension-points.ts) associates related records without inferring hook invocation.

</details>

## Model Experience

The plugin does not add model tools or change model-visible input. It reads the host's existing Session projection and registers a browser view.

## Known Limitations and Deferred Work

Counts cover only the loaded history window. Source references target a separate Harness checkout and may change with its pre-stable APIs. Related events do not establish plugin activation or callback timing. Response duration spans step start to response completion, not isolated network latency. LLM Wire captures are not automatically matched to a graph step. Prompt text colors identify textual structure, not the contributing plugin.

### Dev Note

None.
