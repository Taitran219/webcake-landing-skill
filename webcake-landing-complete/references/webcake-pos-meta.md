# Webcake / POS / Meta implementation

This workflow was exercised on Bếp Nhà in October 2026. Inspect the current project and UI, and verify current official documentation when adapting version-sensitive behavior. Do not keep repeating a UI instruction that the user's screenshots have disproved.

## Native checkout and custom UI

Prefer a native Webcake form when it fits the required buying flow. A custom HTML UI can fill and submit a real native Webcake form, but successful receipt in POS does not alone prove every tracking association was attached.

The original custom architecture:

- A sandboxed data-URL iframe contains the storefront; a parent bridge owns access to the native Webcake form and browser Pixel.
- The child sends validated cart selections and a request UUID over a namespaced postMessage channel. Parent verifies event.source against the exact iframe window, expected origin and schema. Opaque data-URL iframes use origin `null`; that origin alone is not authentication. Use a concrete origin for hosted frames where possible.
- Parent recalculates the cart from trusted configuration, checks destination shop, maps each variant and gift to POS IDs, and fills the real form. Native form must remain mounted and submittable even when not visible to customers.
- Original observed runtime: `WebcakeScript.runtime.vm[FORM].$instance`, `_setVariations`, `_toggleAdditionalPrice`, `_getFormValueByKey`, and `form.requestSubmit()`. These are private/version-sensitive interfaces. Inspect the current runtime and fail clearly when unavailable; never blindly paste the old adapter.
- Check native subtotal, discount, shipping and grand total against the canonical cart before dispatch. Gifts must have the intended zero price. An unpriced product is inquiry-only until a real price exists.
- `form_submitted` may also fire on validation failures. Verify a fresh order ID for the expected form, acceptable response status and expected amount. Show success and emit Lead only after a verified receipt.
- Lock concurrent submission; reuse request IDs for retries of the same pending request and reject changed payloads using the same ID. Persist only the minimum request marker, not customer data. After an ambiguous dispatched request or timeout, do not automatically submit again: inspect whether an order already exists.
- Set up listeners and timeouts with cleanup. Tracking exceptions must never break checkout. Preserve chosen products/customer input appropriately on recoverable validation errors.
- In sandboxed data iframes `crypto.randomUUID` may be unavailable. Use a UUIDv4 fallback backed by `crypto.getRandomValues`; test both branches. Do not use weak random order identifiers.

## Event ownership

| Event | Default owner / trigger |
|---|---|
| Pixel init, PageView | Webcake's selected tracking configuration; one initialization path |
| ViewContent and optional time/scroll events | Webcake when already enabled; avoid manually duplicating |
| AddToCart | Custom UI bridge for actual added quantities, or native implementation when it owns cart interactions |
| InitiateCheckout | Actual opening of checkout with a valid nonempty cart |
| Lead | Verified Webcake receipt via custom bridge, OR native form event; choose one |
| Purchase | POS online **Mua hàng qua web**, triggered by **Đã xác nhận** |

For custom browser events, use the intended Pixel explicitly (`trackSingle` in the original code), stable event IDs and coherent value/currency/content fields. AddToCart must not fire from scrolling or mere button focus. Do not emit Purchase on click, receipt screen or form submission when confirmed POS status is the business definition.

If native form Lead is required for a particular integration, inspect/confirm that requirement and replace custom Lead ownership in the same change. Do not simply enable both. Event IDs for deduplication must be coordinated when intentionally sending the same event by browser and server.

## Configuration sequence

1. Resolve the correct landing, domain, shop, dataset/Pixel and native form from this project's configuration and accessible account. Check both shop and variation IDs, not display names alone.
2. Webcake Integrations: set up or reuse Global Tracking with the intended Pixel. Configure its Conversion API connection securely. In the observed UI, the Global Tracking row's `...` → **Api chuyển đổi** exposes the per-Pixel enable toggle; the ordinary Edit dialog only contains tracking IDs.
3. Landing editor Settings → **Mã chuyển đổi** → select the intended **Global Tracking**. A raw Facebook Pixel ID in a separate field is not by itself evidence that the Global Tracking/CAPI association is selected. Avoid duplicate init paths. Save and publish page changes as needed, then inspect the actual published domain.
4. Connect the native form to the intended Pancake POS shop and product mappings. Preserve required customer and request-ID fields. Validate totals and order synchronization.
5. POS → Cấu hình/Cài đặt → **Sự kiện chuyển đổi** → **Sự kiện Facebook** → enable **Đồng bộ sự kiện Facebook**.
6. In **Sự kiện online**, configure **Mua hàng qua web (Các đơn hàng từ Webcake sẽ được gửi sự kiện)** with status **Đã xác nhận**, and enable that rule. This is distinct from **Purchase (Lượt mua hàng trên Meta)** in the observed dropdown. The latter/default Meta rule alone was not the explicit website rule recommended by support.
7. The observed default value field is a fallback when the order value is zero; `0đ` there does not mean all paid orders send zero. Verify actual exported event values.
8. Do not use the **Sự kiện tại quầy → Thêm Dataset** flow for website orders merely because the online dataset list is empty. The online UI may show an automatic-connection note instead of a dataset table.

A POS setting may apply shop-wide. Inspect existing rules and account purpose; do not change unrelated stores or override a shared business trigger just to match this example.

## Publication mechanics

Maintain readable source alongside any generated base64 script or PKE artifact. Rebuild generated output after changes and check that decoded deployed content matches it. Base64 is transport, not protection for secrets.

If using the original data-iframe approach, verify UTF-8 round-trip, mobile viewport, dialog visibility, scroll behavior and parent mounting. Fixed full-viewport iframe mounting is specific to that design, not mandatory for all landings. Do not overwrite another business's settings when deriving a PKE from an export; remove inherited tracking, scripts, links and form connections that do not belong to this product.

## Documentation entry points

These are starting points, not substitutes for the current UI or runtime:

- Webcake CAPI: https://docs.pancake.biz/webcake/st-f4/st-p3/st-s2/st-ss2?lang=en
- Confirmed-order workflow: https://docs.pancake.biz/landing/st-f4/st-p3/st-s2/st-ss6?lang=es
- POS API: https://docs.pancake.biz/pos/st-f13/st-p3?lang=vi
- Meta CAPI: https://developers.facebook.com/docs/marketing-api/conversions-api/using-the-api/

## n8n alternative

Prefer the established native POS website Purchase path when working. n8n is optional for custom business logic or better sending logs, not an automatic cure for Meta report latency. A webhook starts after notification arrives; polling adds its interval and API processing time. Neither guarantees instant report display.

Before switching, implement stable per-order/event idempotency, durable sent records, bounded retries with error handling, secure credentials and the correct status transition. Coordinate disabling/replacing the old Purchase sender to avoid duplicate events. An old workflow JSON marked inactive or containing unfinished adapters is not a functioning deployment.
