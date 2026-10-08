---
name: webcake-landing-complete
description: Build and complete product sales landing pages on Webcake from a product brief, including product analysis, copy, responsive design, checkout, Pancake POS integration, Meta Pixel/CAPI configuration and evidence-based verification. Use for new products following the Bếp Nhà workflow or for finishing these integrations on an existing Webcake landing.
---

# Complete a Webcake product landing

Work in Vietnamese unless the user requests otherwise. The user wants to supply product information and have the entire landing completed, including tracking, without repeatedly being asked how to configure Webcake events.

## Operating defaults

- Treat an end-to-end landing request as including analysis, implementation, checkout, POS mapping, tracking, validation and handover. Do not stop at a design mockup or a code snippet.
- Reuse the proven workflow, not the previous product's prices, identifiers, claims or promotions. Prefer Webcake + Pancake POS + native server Purchase for this user's product landings unless instructed otherwise.
- Infer routine design and technical choices. Inspect supplied materials, existing project files and accessible account configuration before asking. Do not ask whether to install normal checkout events; they are part of this workflow.
- Ask only for unresolved material inputs: actual price/offer, product evidence, destination account identity, missing access, or a consequential action outside the user's authorized scope. Bundle these questions and continue independent work. Unknown price must not silently become zero; unsupported promises must not become sales claims.
- Authorization in the current task persists. Do not repeatedly request approval for already-authorized setup. This skill does not grant access to accounts, authorize unrelated shop-wide changes, advertising spend, customer contact or unlimited live test orders.
- Use available authorized tools to configure accounts directly. If an authenticated tool is unavailable, prepare all files and provide one short, precise batch of indispensable manual actions. Do not pretend screenshots give control of the account, or promise fully unattended completion without access.
- Do not send support messages on the user's behalf unless explicitly authorized. Drafting a support message is fine.

## Workflow

1. **Recover and organize the brief.** Inspect current project and previous notes. Create a per-product configuration and a progress/evidence document. Separate facts supplied by the user, independently verified facts, and working assumptions. Identify audience, buying objections, differentiators, variants, price, promotions, shipping, gifts, returns, brand assets and desired CTA. Research claims and current platform behavior when needed; do not fabricate testimonials, reviews, scarcity or certifications.
2. **Analyze and write.** Produce a clear offer and persuasive Vietnamese copy grounded in the product. Select suitable sections: hero and CTA, benefits and demonstrations, specifications, variants/comparison, offer, delivery/returns, genuine proof, FAQ, order form and contact. Adapt structure to the product; do not force every section into every landing. Native forms are preferred when they can satisfy the UX. For custom layouts use the native-form bridge described below.
3. **Build the complete buying flow.** Implement responsive mobile/desktop layouts, optimized stable image assets, accessible inputs, product selection, quantities, live totals, shipping, discounts, gifts, order review, submission states and receipt. For COD, say order received/awaiting confirmation, not paid. Keep engineering details out of customer-facing copy. Implement page title, description, preview image and contact/policy links using real information.
4. **Connect commerce.** Read [references/webcake-pos-meta.md](references/webcake-pos-meta.md). Map this product's actual POS product and variation IDs, native form and shop. Recalculate all totals and match the native form's amounts before submitting. Preserve the existing live site's functioning order path when editing.
5. **Configure tracking by default.** Apply the event ownership table in the reference. Connect the correct page-level Global Tracking, Pixel and CAPI configuration, then POS online website Purchase on confirmed status. Avoid parallel Purchase senders. Re-publish when required by page changes within the authorized task.
6. **Verify and finish.** Read [references/verification.md](references/verification.md). Test calculations and failure paths locally, inspect mobile/desktop output, verify published artifacts and browser events, and correlate an authorized order with POS and Meta where access permits. Do not call local-only work deployed, or browser success server-CAPI success.
7. **Handover and preserve reuse.** Deliver the live URL, editable source/build commands, final configuration without secrets, event ownership and a concise evidence summary. State only material unresolved limits. Update the per-product progress record so subsequent turns resume rather than restart.

## Per-product configuration

Maintain a single source of truth for brand/domain, landing ID, shop ID, form ID, Global Tracking name, Pixel/dataset ID, currency, country-specific validation, variants and POS mappings, quantities, shipping, discounts, gifts, and event ownership. Keep secrets in the integration's secure settings or environment, never browser code, PKE exports intended for sharing, or this skill.

Do not reuse Bếp Nhà IDs for another product unless the user explicitly targets the same existing page/shop/dataset or account inspection establishes the intended connection. Reuse a shop/dataset deliberately; do not create replacements unnecessarily.

## Existing implementation to learn from

Read [references/bep-nha-baseline.md](references/bep-nha-baseline.md) only when adapting the original project or needing its implementation pointers. It records the verified outcome and the location of readable source files. This reference is historical evidence, not a promise that private runtime APIs or UI labels never change.

## Definition of done

Complete means the requested landing is delivered in the intended environment, its checkout and POS path are verified, and the agreed events have corresponding evidence. If account access prevents a step, finish all unblocked work and label that exact step pending. An unknown per-order reporting delay alone is not a missing configuration once receipt of relevant server events is demonstrated.
