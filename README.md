# Echo VR mod template

Start here to make a mod for Echo VR that players can get from the
[Echo VR launcher](https://github.com/marshmallow-mia/EchoVR_Launcher). A mod is a small
Windows DLL that the game's mod runtime, [nEVR](https://github.com/EchoTools/nevr-runtime),
loads when Echo VR starts. On Linux the same DLL runs under Proton.

This template gives you a mod that already loads and logs, a build that works from Linux,
macOS and Windows, and GitHub Actions that check every push and publish your releases.

> Working with an AI agent? Point it at [AGENTS.md](AGENTS.md): it has the exact rules and
> formats. This README only covers the basics.

## 1. Make it yours

1. Click **Use this template** on GitHub, then clone your new repo.
2. Open `echo-mod.json` and change:
   - `id`: a short lowercase name, unique among all mods (`my-cool-mod`)
   - `name`, `summary`, `author`
   - `file`: your DLL's name (`MyCoolMod.dll`). Rename `ExampleMod.plugin.json` to match
     (`MyCoolMod.plugin.json`) and change `settings` to that name.
   - `capabilities`: what your mod does (see below). Be honest: players see them.
   - `license`: keep MIT or pick another open-source license, and change `LICENSE` to match
     (put your name in it).
3. Write your mod in `src/mod.cpp`. The rest of `src/` is plumbing you rarely need to touch.

Everything else (the DLL's name, its version, what it tells nEVR) comes from `echo-mod.json`,
so that file is the only one to edit when you rename the mod or bump its version.

### Capabilities

| Capability | Your mod... |
|---|---|
| `observes-only` | only reads the game (stats, overlays that show data) |
| `cosmetic` | changes looks or sound only |
| `alters-gameplay` | changes physics, weapons, movement |
| `alters-rules` | changes match rules, scoring or the game mode |
| `network` | talks to the internet or opens sockets |
| `hooks-engine` | hooks the game's functions itself (needed with MinHook) |

## 2. Build it

**Easiest: let GitHub build it.** Every push runs the **build** workflow. Its result (the
**Artifacts** of the run, under **Actions**) is your DLL.

**On your PC** you need CMake 3.20+, Ninja and MinGW-w64:

- Linux: install `mingw-w64`, `cmake`, `ninja` (Ubuntu: `sudo apt install mingw-w64 cmake ninja-build`)
- macOS: `brew install mingw-w64 cmake ninja`
- Windows: [MSYS2](https://www.msys2.org/) with `mingw-w64-x86_64-toolchain`, `cmake`, `ninja`,
  or Visual Studio (use the `msvc` preset from a *x64 Native Tools* prompt)

```sh
cmake --preset mingw
cmake --build --preset mingw        # -> build/mingw/ExampleMod.dll
```

On Linux, `tools/ci-build.sh` also runs the same checks as GitHub (it needs Wine).

## 3. Try it in the game

1. In the launcher, turn on local plugins for the version you test with. The launcher's
   guide shows how:
   [Writing a plugin and loading it from your PC](https://github.com/marshmallow-mia/EchoVR_Launcher/blob/main/docs/plugins/local-plugins.md#4-loading-it-with-the-launcher).
2. **Mods → Installed mods → Add DLL**, pick your DLL, then start with **PLAY**.
3. Your mod's state (Loaded, Failed and why) is on the Mods page. **Logs** opens its log:
   `plugin_logs/<YourMod>/<YourMod>.log` in the game's `bin/win10` folder.
4. After a rebuild, add the DLL again with **Add DLL**.

## 4. Publish it

**Releases come from GitHub, built by this repo's release workflow.** You don't upload DLLs
yourself.

1. Set `version` in `echo-mod.json`. Versions are [semantic versions](https://semver.org/):
   `1.4.2`. A version with a prerelease part (`1.5.0-beta.1`) is published as a prerelease:
   players only get it if they choose to try your mod's prereleases.
2. Commit, then tag that commit with the version, prefixed with `v`, and push the tag:
   ```sh
   git tag v1.4.2 && git push origin v1.4.2
   ```
3. The **release** workflow builds, checks and publishes the GitHub release. If the tag doesn't
   match `echo-mod.json`, it stops and tells you.

A version, once published, never changes: fix things with a new version.

### Getting into the launcher

- **Your first mod:** open a submission at
  [EchoTools/echo-mods](https://github.com/EchoTools/echo-mods/issues/new/choose) once your repo
  has a release. An automatic check runs and comments on it. After the mod is approved, every new
  release of it reaches players on its own.
- **After the first approval** you also get a **dev channel**: a folder you upload test builds
  to over SFTP (with your GitHub account's SSH key), and a code testers enter in the launcher.
  Who may use your channel is decided by the launcher team, so tell us who your testers are.
- **Each new mod** of yours is submitted once the same way.

### Who sees your mod

Set `visibility` in `echo-mod.json`:

- `public` (the default): listed in the launcher for everyone
- `unlisted`: not listed, but anyone with its id or link can get it
- `private`: only you and the players you name. Put an `access.json` in your dev channel's
  SFTP folder with their **EchoVRCE usernames**:
  ```json
  { "my-cool-mod": ["SomePlayer", "AnotherPlayer"] }
  ```

## Rules

- No cheats, and nothing that gives an unfair advantage in public matches.
- No collecting players' data. Network use is declared with the `network` capability.
- Capabilities are honest.
- The source is public and under an open-source license: the launcher redistributes your
  files, so it needs the right to.
- Mods are for Echo VR on PC (Windows, and Linux through Proton).

Antivirus programs sometimes flag new, unknown DLLs. If yours is, tell us; don't ask players
to turn their antivirus off.

## Need help?

Ask in the Echo VR community Discord, or open an issue in
[EchoTools/echo-mods](https://github.com/EchoTools/echo-mods/issues).
