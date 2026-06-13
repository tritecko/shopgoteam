# Stage 1 Editorial Operations Redesign

## Decision

Use an editorial operations direction for ShopGo.team.

The interface keeps the Kraken-inspired floating navigation, atmospheric
porcelain canvas, large financial hierarchy, and restrained cyan action color.
It removes permanent right-side panels and outlined cards.

## Visual System

- Sora is the display face for page titles, major values, and action statements.
- Inter remains the body, label, control, table, and financial data face.
- Cards use white or softly tinted fills with broad low-opacity shadows.
- Borders are reserved for controls and subtle row separators.
- The signature element is an operational horizon: a luminous horizontal band
  representing progress, money movement, or provider health.
- Each screen has one dominant region and one dominant action.

## Layout

- Desktop: floating left navigation plus a full-width central canvas.
- Tablet: compact navigation with the action stage in normal document flow.
- Mobile: one dominant column with essential state first.
- High-stakes actions use an inline action stage directly after the governing
  summary.
- Confirmation dialogs remain modal when an action is irreversible.

## School Fulfillment Information

The school dashboard does not expose Printful implementation details.

Schools see:

- order counts and delivery outcomes;
- expected dates and tracking after shipment;
- exceptions that need school attention;
- the secondary attribution `Fulfillment powered by Printful`.

Admins retain access to mappings, sync failures, provider health, and costs.
School order and delivery detail lives on a separate screen.

## UX Laws Applied

- Hick's Law and choice overload: one dominant action per screen.
- Common region and proximity: related information is grouped with spacing and
  tint instead of borders.
- Von Restorff effect and selective attention: the inline action stage is the
  deliberate focal point.
- Fitts's Law: primary targets are large and contextual.
- Goal-gradient effect: operational horizons show progress toward launch,
  publishing, payout, and delivery.
- Working memory and Tesler's Law: technical provider complexity stays in admin
  surfaces.
- Peak-end rule: approval and irreversible transfer moments are explicit and
  carefully staged.

## References

- `design.md`
- `Design Inspiration Screenshots/Kraken Web 37.png`
- `Design Inspiration Screenshots/Kraken Web 62.png`
- https://godly.website/
- https://lawsofux.com/
