# Convincing glass surfaces

Use this guidance when glass is the requested visual direction. It describes a
successful stationary treatment, not a requirement to apply glass to other UI.

Make curvature and transmitted background visible. A clear center, localized
edge distortion, directional reflections, and a small contact shadow can suggest
thickness without changing the content or animating the control.

- Keep the center mostly clear. Concentrate backdrop refraction in a narrow
  perimeter mask so the edge reads as curved while the center transmits the scene.
- Use a broad, fading reflection near one upper edge and a smaller light patch
  near the opposite lower edge. These are deliberate optical approximations;
  avoid an opaque white or gray fill that conceals the background.
- Give one rim alternating bright and subdued segments, rather than a uniform
  glowing outline. Contrast at the perimeter must still make selection readable.
- Assign the rim to one owner. Inspect library-generated borders, highlight
  spans, pseudo-elements, and host shadows; stacked outlines produce a doubled
  glass appearance. Keep a restrained contact shadow instead of another ring.
- Keep text above the decorative layer, with unchanged weight, size and position.
  Bolding or scaling text does not simulate refraction. For stationary glass,
  disable elasticity, pointer tracking and decorative transitions.

Inspect the installed library and computed styles before tuning parameters.
For example, this project's liquid-glass-react implementation adds a base 4px
blur even at blurAmount=0. Its selected-chat override uses 0.5px blur and masks
the warp to the perimeter. A library name or successful build does not establish
visual realism.

Verify selected, unselected, hovered and keyboard-focused states against the
actual backdrop, including pale areas and narrow rows. Pure transparency can
erase selection; a dark uniform tint can turn it into a conventional filled
button. Tune reflection and rim contrast while preserving a clear center.
Retain opaque reduced-transparency fallbacks and a visible forced-colors state.

The user-approved project implementation and exact settings are documented in
the repository's frontend/README.md under “Selected-chat glass recipe”. Treat
those values as a reference for its row size and wallpaper, not universal constants.
