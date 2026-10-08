# Bếp Nhà baseline — observed 2026-10-08

This is a historical reference for adapting a completed implementation, not a set of defaults for a new product. No account tokens or customer records belong in this skill.

## Location and identity

Original workspace: `<original-workspace>`.
Source directory: `webcake-migration/`; product app: `khuon-gio/`.

- Site: https://example.com/
- Landing: `<original-landing-id>`
- POS shop: `<original-shop-id>`
- Native form: `bnpos001`
- Pixel/dataset: `<original-pixel-id>` (Meta label DTS GIÒ)
- Global Tracking: Bếp Nhà Trọn Vị
- Iframe: `bep-nha-html-goc`

Only use these IDs when explicitly working on this existing business/page. If the old workspace is unavailable, build from the architecture references; the skill should not depend on those files being present.

## Useful source files

- `meta-child.js`, `meta-parent.js`: browser event bridge, UUID fallback, AddToCart/checkout/receipt Lead.
- `pos-child.js`, `pos-parent.js`: native form adapter source; base source was test-gated, not the final production artifact.
- `build_pos_production.py`, `build_meta_tracking.py`: inspect their inputs before rebuilding; they contain product-specific assumptions.
- `pos-production-parent.js`, `pos-meta-parent.js`, `pos-meta-combined.js`: inspect generated active behavior.
- `pos-pixel-body.html`: final combined script installed before body close, encoded for transport.
- `mount-original-viewport.js`: iframe mount logic specific to the original design.
- `test-meta-child.cjs`, `test-meta.cjs`, `test-pos-bridge.cjs`: useful behavioral test patterns, adapt IDs and rules rather than copying assertions unchanged.

Do not copy `*.private.*`, downloaded public-page dumps, Chrome profiles or PKE exports wholesale into a new skill/product. They may contain account or customer material. The old migration README described an earlier incomplete draft and is not authoritative about final status.

## Verified outcome and limits

The custom UI submitted real native Webcake forms; orders appeared in Webcake/POS. Browser AddToCart, InitiateCheckout and receipt Lead appeared in Meta. POS online rule was changed to **Mua hàng qua web** at **Đã xác nhận**, enabled.

On October 8 Meta Overview showed two Purchase events via CAPI for that day. Sample Activity showed the correct website URL. An exported Purchase CSV had four sample rows with SERVER source and website action source, VND, two rows of 282150 and two of 129000. The user stated these were different orders. The export lacked timestamps and event/order IDs; no precise per-order latency or full identity correlation was established.

Support stated Webcake sends Purchase after confirmation but could not supply per-order Meta receipt timestamps. Keep this uncertainty bounded; do not treat the pipeline as broken solely because reports appeared later.

## Product-specific rules not to inherit

The original sold cookware variants, single-item shipping 30000 VND, free shipping and 5% discount at two items, and one free heart mold at three items. One variant had no fixed price and could not submit an order. Those offers, names, limits and gift mappings must be replaced by the next product's actual business rules.

An old `khuon-gio-automation/` n8n draft existed with inactive workflows and an unfinished adapter. It was not the verified Purchase sender and is not a production fallback to deploy unchanged.

