---
name: knowlaborator-stage-lab
description: Build an interactive explanation, chart, calculator, diagram or simulation inside the conversation with the experimental Stage Lab MCP App.
---

# Stage Lab — prototype

You generate the interface. In ChatGPT, ChatGPT authors it; in Claude, Claude authors it.
Use `render_stage_lab` to display a self-contained presentation in an MCP Apps host.
This is an alternative to the record-oriented Knowlaborator Stage. It has its own
plugin and creates independent conversation snapshots; it requires no open browser Stage.

## Author a useful interaction

- Prefer text for simple answers. Use Stage Lab when a control, chart, animation or
  simulation helps the person understand or explore something.
- Choose the layout freely. Generate complete HTML, CSS and JavaScript, rather than
  approximating the idea with a fixed set of cards. SVG, canvas, native controls,
  CSS diagrams and local calculations are supported.
- Use the person's language. Label controls, make keyboard interactions work, adapt
  to a narrow viewport and honor `prefers-reduced-motion`. Include play/pause for animation.
- Read actual business data through the ordinary authorized Knowlaborator tools first.
  Only pass the data the presentation needs. Show sources; label invented examples,
  assumptions, estimates and units. Never embed credentials or private content not needed
  for the person's request. Rendering does not verify facts or grant write permission.

## Tool fields

- `title`: 1–160 characters.
- `description`: 1–2,000 characters of useful plain-text explanation, conclusions,
  assumptions and source attribution. This is the fallback for clients without UI.
- `html`: a complete HTML body fragment. Do not include document wrappers, scripts,
  inline event attributes, external stylesheets, links or frames.
- `css`: all presentation styles. The host provides light/dark tokens:
  `--color-background-primary`, `--color-background-secondary`, `--color-text-primary`,
  `--color-text-secondary`, `--color-text-tertiary`, `--color-border-tertiary`,
  `--color-accent`, and `--font-sans`. You can freely add local styles.
- `jsFunctions`: JavaScript declarations, calculations and `addEventListener` handlers.
  The markup already exists. Native APIs only: no imports, external libraries, fetch,
  storage, form submission, navigation or direct MCP/host calls. No top-level await.
- `jsExpressions`: at most 32 initialization expressions run in order after the functions.
- `initialHeight`: 160–1,600 pixels, default 480. The view resizes to the content.

Combined HTML, CSS and JavaScript are limited to 128 KiB of UTF-8. Supply complete output:
this prototype renders completed tool results, not token-level streaming.

## Continue the conversation

An explicit control can call `window.stageLab.sendPrompt(text)` (up to 4,000 characters).
Include the selected values, units and relevant assumptions. This prepares an editable
follow-up in the trusted shell. The person reviews and sends it to the conversation.
The UI cannot invoke arbitrary tools or silently start an assistant turn.

Example:

```js
document.getElementById('explain').addEventListener('click', () => {
  window.stageLab.sendPrompt('Explain this scenario: monthly growth is ' + rate.value + '% over 12 months.');
});
```

A follow-up can generate a new presentation. Prior presentations keep their own local
controls; there is no cross-call patch API or shared Stage channel. Frame reloads reset
local control state. Nothing is saved as a Knowledge record, Component or Document.

## Hosts and limitations

ChatGPT and Claude can display this through MCP Apps, subject to each host's sandbox
policy. Inline and fullscreen use the shared bridge; PiP appears only when offered.
Terminal-only clients receive the text explanation. If the host blocks the inner
frame, use that explanation and report the limitation rather than claiming success.
The local preview simulates both hosts; real-host acceptance is a separate check.
