# Banner Generation Skill

Generate polished promotional banners with a 1xm image-model backdrop and crisp local text overlays.

This is a Codex skill for creating final, usable banner assets such as ecommerce operation covers, product intro cards, social media headers, Chinese marketing graphics, and wide promotional images. It uses 1xm for the visual base, then renders exact text, logos, badges, and layout locally through HTML/CSS so Chinese and English copy stay sharp and controllable.

## What It Does

- Generates a text-free visual backdrop through 1xm, usually with `gpt-image-2`.
- Uses user-provided product facts, screenshots, logos, and brand references.
- Keeps generated text out of the image model to avoid fake letters and broken Chinese.
- Overlays final title, subtitle, logo, badges, and callouts with local HTML/CSS.
- Exports exact-size PNG files with Playwright/Chrome.
- Requires users to configure their own 1xm key; no local project key is bundled.

## Install

Clone or copy this repository into your Codex skills directory:

```bash
mkdir -p ~/.codex/skills
git clone https://github.com/howtimeschange/banner-generation.git ~/.codex/skills/banner-generation
```

If Codex does not immediately discover the skill, start a new session or restart Codex.

## Configure 1xm

Preferred:

```bash
export ONEXM_API_KEY="your-1xm-api-key"
export ONEXM_GROUP="" # optional
```

Alternative config file:

```bash
mkdir -p ~/.config/banner-generation
chmod 700 ~/.config/banner-generation
cat > ~/.config/banner-generation/1xm.json <<'JSON'
{
  "apiKey": "your-1xm-api-key",
  "group": "",
  "baseUrl": "https://api.1xm.ai/v1"
}
JSON
chmod 600 ~/.config/banner-generation/1xm.json
```

Never commit real API keys into this repository, generated HTML, prompt files, or README examples.

## Use With Codex

After installation, ask Codex naturally:

```text
用 $banner-generation 生成一张 1650x500 的产品介绍 Banner，1xm key 从环境变量 ONEXM_API_KEY 读取，中文标题用本地 HTML 合成。
```

```text
Use $banner-generation to create a 1920x640 launch banner for this app. Use the logo as a visual reference and keep all text as local overlays.
```

```text
用 $banner-generation 参考这个截图比例，做一张电商运营自动化介绍图，标题和卖点用中文叠加。
```

## Direct Script Usage

Create a prompt:

```bash
mkdir -p tmp/my-banner
cat > tmp/my-banner/prompt.txt <<'EOF'
Use case: ads-marketing
Asset type: wide promotional banner for a product introduction card
Primary request: Create a cinematic ultra-wide visual backdrop for a productivity tool.
Composition/framing: keep the left 45 percent calm and unobstructed for later typography.
Text: render no words, no numbers, and no letter-like marks; typography will be overlaid separately.
Avoid: unreadable pseudo-text, watermark, border, UI chrome, over-busy collage.
EOF
```

Generate a visual base:

```bash
node scripts/generate_1xm_image.mjs \
  --prompt tmp/my-banner/prompt.txt \
  --out tmp/my-banner/base.png \
  --model gpt-image-2 \
  --size 3840x1280 \
  --quality high
```

Render a composed HTML banner:

```bash
node scripts/render_html_banner.mjs \
  --html tmp/my-banner/banner.html \
  --out tmp/my-banner/final.png \
  --width 1650 \
  --height 500 \
  --scale 2
```

Verify the output size:

```bash
sips -g pixelWidth -g pixelHeight tmp/my-banner/final.png
```

## Workflow

1. Ground the banner in real product facts, brand assets, screenshots, and user requirements.
2. Generate a text-free base image with 1xm.
3. Compose final text, logo, badges, and visual hierarchy locally with HTML/CSS.
4. Render the final PNG with fixed dimensions.
5. Inspect the result for overflow, cropped logos, fake text in the base, and visual collisions.

## Repository Layout

```text
.
├── SKILL.md
├── README.md
├── LICENSE
├── agents/
│   └── openai.yaml
├── references/
│   └── 1xm-configuration.md
└── scripts/
    ├── generate_1xm_image.mjs
    └── render_html_banner.mjs
```

## Validate

If your local Codex installation includes the skill validator:

```bash
python3 ~/.codex/skills/.system/skill-creator/scripts/quick_validate.py .
node --check scripts/generate_1xm_image.mjs
node --check scripts/render_html_banner.mjs
```

## Notes

- This repository intentionally does not include API keys or generated output.
- `gpt-image-2` is usually a good default for wide commercial backdrops.
- For very wide final banners, generate a supported base such as `3840x1280`, then crop or fit it in HTML.
- If the base image contains fake text, regenerate it with stricter prompt constraints rather than hiding it locally.
