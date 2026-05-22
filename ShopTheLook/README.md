# 🛍️ Shop the Look — Bee Creek Apparel

A custom Shopify feature that lets customers discover and shop curated outfits directly from the homepage. Clicking a look card takes them to a dedicated look page where they can quick-add individual pieces to their cart without leaving the page.

---

## ✨ How It Works

```
Homepage
└── "Shop the Look" section
    ├── Look Card 1 ──→ /pages/southern-belle-look  (outfit image + products + quick-add)
    ├── Look Card 2 ──→ /pages/western-look
    ├── Look Card 3 ──→ /pages/vacation-vibes-look
    └── Look Card 4 ──→ /pages/casual-chic-look
```

**Customer flow:**
1. Customer sees 4 editorial look cards on the homepage
2. Clicks a look → goes to the look's page
3. Sees a full outfit photo alongside up to 5 product cards
4. Clicks **+ Quick Add** on any product → popup appears with size/color selector pre-selected
5. Clicks **Add to Cart** → item added, cart updates instantly, no page reload

---

## 📁 File Structure

```
ShopTheLook/
├── sections/
│   ├── shop-the-look.liquid     # Homepage 4-card grid section
│   └── look-detail.liquid       # Look detail page (outfit image + products + quick-add modal)
├── templates/
│   └── page.look.json           # Page template that loads look-detail section
└── README.md
```

---

## 🚀 Installation

### Step 1 — Copy files into your live theme

In **Shopify Admin → Online Store → Themes → [Your Theme] → Edit Code**:

| File to create/replace | Source file |
|---|---|
| `sections/shop-the-look.liquid` | `ShopTheLook/sections/shop-the-look.liquid` |
| `sections/look-detail.liquid` | `ShopTheLook/sections/look-detail.liquid` |
| `templates/page.look.json` | `ShopTheLook/templates/page.look.json` |

### Step 2 — Create page metafield definitions

Run these once in **Shopify Admin → Settings → Custom data → Pages** (or via the GraphQL API):

| Name | Namespace | Key | Type |
|---|---|---|---|
| Look Image | `look` | `look_image` | File reference |
| Look Title | `look` | `look_title` | Single line text |
| Look Products | `look` | `products` | List · Product reference |

> These are already created on **beecreekapparel.com** — skip this step if reinstalling on the same store.

### Step 3 — Create your look pages

In **Shopify Admin → Pages → Add page**:

1. Give the page a name (e.g. *"Summer Southern Belle"*)
2. Under **Theme template**, select **`look`** from the dropdown
3. Save the page
4. Scroll down to the **Metafields** section and fill in:
   - **Look Image** — upload your outfit photo
   - **Look Title** — display name (optional, falls back to page title)
   - **Look Products** — search and select up to 5 products

Repeat for each look (up to 4 recommended for the homepage grid).

### Step 4 — Set up the homepage section

In the **Theme Customizer → Homepage**:

1. Click **Add section → Shop the Look**
2. Add up to **4 Look Card blocks**
3. For each block:
   - Upload a look image (portrait format, 3:4 ratio works best)
   - Set the look title
   - Paste the URL of the look page you created in Step 3
4. Save

---

## 🎨 Customization

### Homepage card grid (`shop-the-look.liquid`)

| Setting | Description |
|---|---|
| Heading | Section title (default: "Shop the Look") |
| Heading size | Small / Medium / Large |
| Color scheme | Uses your theme's color scheme tokens |
| Top / Bottom padding | 0–100px range slider |

Each **Look Card block** has:
- Image picker
- Look title (shown over the image)
- URL link to the look detail page

### Look detail page (`look-detail.liquid`)

| Setting | Description |
|---|---|
| Show page heading | Toggle on to display a heading above the layout |
| Heading override | Custom text (falls back to page title) |
| Color scheme | Matches your theme |
| Top / Bottom padding | 0–100px range slider |

Content (outfit image + products) is driven entirely by **page metafields** — not section settings — so each look page is completely independent.

---

## ⚡ Quick-Add Modal Behavior

- Opens on **+ Quick Add** click
- Product variants are fetched live from `/products/[handle].js`
- **First available variant is pre-selected automatically** — Add to Cart is enabled immediately
- Selecting a different size/color updates the price display in real time
- On **Add to Cart**:
  - Item added via `/cart/add.js`
  - Cart bubble count updates
  - Cart drawer opens (if theme supports it)
  - Modal closes after 1.4 seconds
- Closes on overlay click, ✕ button, or `Escape` key

---

## 🗂️ Metafield Reference

All metafields use namespace **`look`** on the **Page** resource:

| Key | Type | Description |
|---|---|---|
| `look_image` | `file_reference` | The outfit/model photo shown on the left |
| `look_title` | `single_line_text_field` | Optional display title (falls back to `page.title`) |
| `products` | `list.product_reference` | Products shown in the right-hand grid (up to 5) |

Access in Liquid:
```liquid
{% assign look_image    = page.metafields.look.look_image.value %}
{% assign look_title    = page.metafields.look.look_title.value | default: page.title %}
{% assign look_products = page.metafields.look.products.value %}
```

---

## 🖥️ Theme Compatibility

Built and tested on the **Shopify Heritage theme**. Compatible with any **Online Store 2.0** theme that uses:
- JSON page templates
- `color_scheme` section setting type
- Dawn-style CSS custom properties (`--color-foreground`, `--color-background`, etc.)

> **Note:** The quick-add modal uses explicit hex colors (`#ffffff`, `#1a1a1a`) rather than CSS variables to ensure it renders correctly across all Heritage color schemes.

---

## 🔧 Troubleshooting

| Problem | Fix |
|---|---|
| "look" template not in dropdown | Confirm `templates/page.look.json` was saved to the **live** theme, not a draft |
| Products showing on all look pages | Old version used section blocks — update to the metafield-based `look-detail.liquid` in this repo |
| `[object Object]` in option labels | Update to current `look-detail.liquid` — includes `optName()` fix for Shopify's AJAX API |
| Modal background is transparent | Update to current `look-detail.liquid` — uses `#ffffff` instead of `rgb(var(--color-background))` |
| Add to Cart stays disabled | Fixed in current version — first available variant is now pre-selected on modal open |
| Metafields not appearing on page | Ensure metafield definitions are created with `ownerType: PAGE` and `pin: true` |

---

## 📋 Changelog

| Version | Change |
|---|---|
| v1.0 | Initial build — section blocks approach (shared across pages) |
| v2.0 | Rebuilt with page metafields — fully isolated per-page look data |
| v2.1 | Fixed transparent modal background; fixed `[object Object]` option labels |
| v2.2 | Pre-selects first available variant on modal open; darker product card info area |

---

*Built for [Bee Creek Apparel](https://beecreekapparel.com) — Southern Belle meets Western Casual.*
