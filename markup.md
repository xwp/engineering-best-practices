---
page: markup
title: Markup
nav: Markup
group: navigation
weight: 2
layout: default
updated: 2015-12-18 17:40:16Z
subnav:
  - title: Philosophy
    tag: philosophy
  - title: HTML5 Structural Elements
    tag: html5-structural-elements
  - title: Classes & IDs
    tag: classes-amp-ids
  - title: Accessibility
    tag: accessibility
  - title: Progressive Enhancement
    tag: progressive-enhancement
---

<div class="docs-section">
		{% capture markup %}{% include markdown/Markup.md %}{% endcapture %}
		{{ markup | markdownify }}
</div>