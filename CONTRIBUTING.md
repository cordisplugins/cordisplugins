# Contributing to Cordis Plugins

Cordis Plugins is the public registry for [Cordis](https://cordisplugins.github.io/docs/getting-started/what-is-a-cordis-plugin) plugins — the smallest composable unit of a Cordis application. The registry site lives at [cordisplugins.github.io](https://cordisplugins.github.io) and is built from [cordisplugins/cordisplugins.github.io](https://github.com/cordisplugins/cordisplugins.github.io). This repository is where plugin proposals and registry-level discussion take place.

## Propose a plugin

Open an issue in this repository before you publish. A useful proposal includes:

- **Name and repository** — the package name you intend to use, plus a public repository link if there is one.
- **Capability** — what the plugin adds and which host it targets, in one or two paragraphs.
- **Boundary** — the services, tools, or UI slots it provides, and the Cordis dependencies it `inject`s (or reads optionally with `ctx.get()`).
- **Compatibility** — `Both`, `ACRYL`, or `DSH stock`, following the [compatibility tiers](https://cordisplugins.github.io/docs/compatibility/compatibility-tiers).
- **Category** — one of `services`, `tools`, `ui-client`, `providers`, `editors-canvas`, `brand-theming`, `market-registry`, `community-interop`.
- **License and documentation** — the license you will publish under and a link to the README.

A proposal is a starting point for discussion; it does not reserve a name or guarantee a listing. Expect questions about overlap with existing plugins and about lifecycle behavior.

## Publish and get listed

The registry has no submission queue. A public GitHub repository with the topic `cordis-plugin` whose latest release contains one `.tgz` is indexed within about an hour. To get there:

1. **Build to the protocol.** Export a `name` and an `apply(ctx)` function, or a class extending `Service` when other plugins need to call into you.
2. **Declare dependencies.** Use `inject` for hard requirements — a missing provider should leave the plugin `PENDING`, not broken — and `ctx.get()` for optional ones.
3. **Own your resources.** Register timers, listeners, watchers, sockets, and subprocesses through `ctx.effect()` and return a disposer, so reload and disposal leave nothing behind.
4. **Verify in a real Loader.** Exercise activation, `PENDING` and reactivation, provider replacement, disposal, and repeated mount/reload.
5. **Document and version.** Ship a README that states the capability and compatibility, declare a license, and follow semver.
6. **Publish with pnpm.** `pnpm publish --access public` — pnpm is required when the manifest uses `workspace:*` dependencies, because only pnpm rewrites them to real semver ranges.
7. **Make it discoverable.** Add the `cordis-plugin` topic to the public repository and cut a GitHub release whose assets include one `.tgz`.

The registry reads `package.json` from inside the tarball (it never runs it) and derives `provides`, `consumes`, and UI slots from the generated service contract (`acryl.requires`, `acryl.provides`, `acryl.slots`). Compatibility is computed from that contract: `ACRYL` when it needs an ACRYL-only service, `Both` when it needs only services stock DSH also has, and `DSH stock` when there is no contract.

For a curated listing, open a pull request against the registry repository adding an entry to `src/lib/registry-data.ts`. Hand-written entries take precedence over a discovered one with the same name.

## Review expectations

- **Honest compatibility.** Do not claim `Both` or `DSH stock` if the plugin injects an ACRYL-only service; it will fail silently for the users the claim attracted.
- **Lifecycle hygiene.** Every activation-owned resource has a disposer, and the plugin survives reload and provider replacement without leaks or stale registrations.
- **A small, deliberate surface.** A plugin should be replaceable and testable on its own; keep the public boundary narrow and document what consumers may rely on.
- **Protocol neutrality.** Plugins should use ordinary Cordis primitives rather than assuming a particular product, host branding, or startup order.

## Contributing to this repository

Open an issue before non-trivial changes, and keep pull requests focused: describe what changes, why, and which issue it addresses. Documentation fixes and small corrections can go straight to a pull request. By contributing you agree that your contributions are licensed under the repository's [MIT license](./LICENSE).
