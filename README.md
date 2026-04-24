# 📊 Bar Chart Race — Claude Skill

A **Claude skill** that generates fully self-contained, animated bar chart race visualizations powered by the Anthropic API. Compare how any set of entities — countries, companies, technologies, sports teams — have risen and fallen over time, all without needing an external dataset.

---

## ✨ What it does

- Animates a **racing bar chart** for any time-ranked topic
- Uses Claude (via the Anthropic API) to **generate historically-grounded data on the fly**
- Produces a **single self-contained HTML file** — no dependencies, no build step
- Works both as a **Claude.ai artifact** and as a **standalone browser app**

### Example prompts it responds to

| You say…                                        | What happens             |
|-------------------------------------------------|--------------------------|
| "bar chart race of top 10 GDP countries"        | Builds it instantly      |
| "show me how streaming services grew over time" | Builds it                |
| "animate the rise of AI companies"              | Builds it                |
| "compare programming language popularity"       | Builds it                |
| *(pastes a CSV of time-series data)*            | Builds it with your data |

---

## 📁 Repository Structure

```
/
├── LICENSE
├── README.md
└── skills/
    └── bar-chart-race/
        └── SKILL.md        ← The skill file Claude reads
```

> **How Claude skills work:** Claude reads `SKILL.md` at the start of a task to learn how to behave. Drop this directory into your Claude skills folder and Claude will automatically know how to build bar chart races.

---

## 🚀 Setup

### 1. Clone the repo

```bash
git clone https://github.com/drsreekrishnan/bar-chart-race-skill.git
```

### 2. Add the skill to Claude

Copy the `skills/` directory into your Claude skills mount point (e.g. `/mnt/skills/user/`) so Claude can find it:

```
/mnt/skills/user/bar-chart-race/SKILL.md
```

### 3. Get an Anthropic API key *(only needed outside Claude.ai)*

When running the generated HTML as a standalone file in a browser, you'll need an API key:

👉 https://console.anthropic.com/keys

When running inside **Claude.ai**, the key is injected automatically — no input needed.

---

## 🎨 Chart Features

- **Smooth 60fps animation** via rAF interpolation (no D3 transitions)
- **Pause / Play / Replay** controls + year scrubber
- **Keyboard shortcuts**: `Space` to pause, `R` to replay
- **Auto-scaled value labels** (K / M / B)
- **Dark theme** with a vibrant 16-color palette
- Handles entities that appear or disappear mid-range (e.g. countries that split or merged)

---

## 🛠 Customization

Edit `skills/bar-chart-race/SKILL.md` to change:

- **Speed** — default is 1200ms per year
- **Color palette** — 16 colors cycle through entities
- **Aesthetic** — fonts, bar radius, background, accent color
- **Data rules** — number of entities, year range, units

See the full spec inside `SKILL.md`.

---

## 📄 License

MIT — see [LICENSE](./LICENSE)
