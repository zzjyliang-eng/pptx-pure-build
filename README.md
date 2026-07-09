[SKILL.md](https://github.com/user-attachments/files/29831253/SKILL.md)
# pptx-pure-build
build ppt with closed-loop workflow to provied usable .pptx file
---
name: pptx-pure-build
description: >-
  Generate polished .pptx presentations from scratch using python-pptx with a closed-loop
  quality workflow (Plan-Build-QA-Fix-Deliver). Use when the user asks to
  create a PPT/PPTX/PowerPoint presentation, build slides from content, or generate a deck
  from a report/outline. Handles dual-template pitfalls, CJK typography, image aspect ratios,
  font size compliance, overlap detection, and content completeness verification. Triggers on:
  PPT, PPTX, PowerPoint, make slides, create presentation.
version: 1.0.0
---

# PPTX Pure Build

Generate professional .pptx presentations from scratch using python-pptx, with a closed-loop quality assurance workflow.

## When to Use

- User asks to create a PPT/PPTX/PowerPoint presentation
- User provides content (report, outline, text) and asks to build slides
- User provides a template and asks to generate a new presentation
- User asks to rebuild or fix an existing PPTX

## When NOT to Use

- Simple text extraction from PPTX -> use markitdown
- Minor edits to existing PPTX -> use python-pptx directly
- SVG-based generation -> use ppt-master skill instead

## Core Principles

### 1. Pure Build Over Template Merge

**CRITICAL**: Never merge two different .pptx templates (delete slides from one, add slides from another). This causes ZIP structure corruption - duplicate slideLayout/slideMaster/theme entries - resulting in PowerPoint repair dialogs and missing content.

| Approach | When | Risk |
|----------|------|------|
| **Pure build** (recommended) | Most cases | Lowest |
| Single template fill | Template has all needed layouts | Low |
| Dual template merge | Never | **HIGH - ZIP corruption** |

### 2. Closed-Loop Quality Workflow

Every PPTX generation MUST follow: Plan -> Build -> QA -> Fix -> Re-QA -> Deliver. Never deliver with P0 (blocking) issues.

### 3. Font Size Constraints

MIN_BODY_PT = 16 (all body text), MIN_TITLE_PT = 22 (all headings). CJK font: Microsoft YaHei.

### 4. Image Aspect Ratio

Always calculate from PIL.Image.size. Use ratio = min(max_w/iw, max_h/ih), center within bounds.

### 5. Text Box Best Practices

Always set word_wrap=True, auto_size=None, explicit font.name and font.size.

## Quick Start

See [workflow.md](workflow.md) for the full 5-phase workflow with code examples.

## Template-Based Generation

When user provides a template: analyze layouts first, then create new slides from existing layouts. NEVER delete slides from one template and add from another.

## Known Issues

| Issue | Solution |
|-------|----------|
| PowerPoint repair dialog | Use pure build, not dual template merge |
| Chinese text as boxes | font.name = Microsoft YaHei |
| UnicodeEncodeError | sys.stdout.reconfigure(encoding=utf-8) |
| Slides wrong order | Use explicit index tracking |
| Images wrong size | Calculate from PIL.Image.size |
| Text overflow | Estimate: CJK=1.0x pt width, Latin=0.55x |
| QA false overlaps | Group shape transforms - verify visually |

## Additional Resources

- [workflow.md](workflow.md) - Detailed 5-phase workflow with code patterns
- [qa-checklist.md](qa-checklist.md) - QA diagnostic checklist
- [scripts/qa_pptx.py](scripts/qa_pptx.py) - Automated QA script
