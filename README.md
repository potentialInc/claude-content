# claude-contents

Claude Code content creation and strategy skills - blog posts, documentation, video scripts, newsletters, content management, and editorial workflows.

## Overview

This repo contains content-focused skills for Claude Code. Add it as a submodule to projects that need content creation and management capabilities.

**Focus**: Educational, informative content that builds brand authority and educates audiences.

## Installation

```bash
cd your-project/.claude
git submodule add https://github.com/potentialInc/claude-contents.git contents
```

## Available Skills

### Content Creation (8 Skills - MVP)

| Skill | Description | Keywords |
|-------|-------------|----------|
| `blog-post-writing` | Blog post structure, hooks, SEO-friendly writing | blog post, write blog, article writing |
| `long-form-content` | In-depth articles, ultimate guides, pillar content (3000+ words) | long form, ultimate guide, pillar content |
| `technical-documentation` | API docs, user guides, integration docs, SDK documentation | technical documentation, api docs, user guide |
| `tutorial-content` | Step-by-step tutorials, how-to guides, walkthroughs | tutorial, how-to, step by step guide |
| `case-study-writing` | Customer success stories, use case documentation | case study, success story, customer story |
| `video-scripts` | YouTube scripts, explainer videos, product demos, webinars | video script, youtube script, demo script |
| `newsletter-writing` | Newsletter structure, content curation, subscriber engagement | newsletter, email newsletter, subscriber content |
| `brand-storytelling` | Company narratives, founder stories, mission-driven content | brand story, company narrative, founder story |

### Content Management & Strategy (6 Skills - MVP)

| Skill | Description | Keywords |
|-------|-------------|----------|
| `content-calendar` | Editorial calendar planning, content scheduling, seasonal planning | content calendar, editorial calendar, content schedule |
| `brand-voice` | Voice & tone guidelines, style guide development, consistency | brand voice, tone of voice, style guide |
| `content-strategy` | Content goals, audience analysis, content pillars, strategic framework | content strategy, content planning, content goals |
| `content-atomization` | Repurposing content across formats (blog → social → video → email) | content repurposing, atomization, multi-format |
| `editorial-guidelines` | Writing standards, formatting rules, editorial workflow | editorial guidelines, writing standards, style rules |
| `content-audit` | Content inventory, performance review, content pruning | content audit, content inventory, content review |

## Differentiation from claude-marketing

| Aspect | claude-marketing | claude-contents |
|--------|-----------------|-----------------|
| **Focus** | Conversion & growth | Information & brand building |
| **Goal** | Drive sales, signups, revenue | Educate, inform, build authority |
| **Content Type** | Sales copy, ad copy, CTAs | Blog posts, docs, articles, guides |
| **Tone** | Persuasive, urgent | Educational, authoritative |
| **Metrics** | Conversion rate, CPA, ROI | Engagement, shares, time on page |

**Examples**:
- **Marketing** (claude-marketing): "Sign up now and get 50% off!" → copywriting skill
- **Contents** (claude-contents): "A comprehensive guide to understanding X" → blog-post-writing skill

## Usage

Skills are auto-suggested based on keywords in your prompts. For example:

```
"Write a blog post about microservices architecture"
→ Suggests: blog-post-writing

"Create technical documentation for our API endpoints"
→ Suggests: technical-documentation

"Help me plan a content calendar for Q2"
→ Suggests: content-calendar

"Develop brand voice guidelines for our company"
→ Suggests: brand-voice

"Write a video script explaining our product"
→ Suggests: video-scripts
```

## Structure

```
claude-contents/
├── README.md                          # This file
└── skills/
    ├── skill-rules.json               # Trigger configuration
    │
    ├── creation/                      # Content Creation Skills (8)
    │   ├── blog-post-writing/
    │   │   ├── SKILL.md
    │   │   └── resources/
    │   ├── long-form-content/
    │   ├── technical-documentation/
    │   ├── tutorial-content/
    │   ├── case-study-writing/
    │   ├── video-scripts/
    │   ├── newsletter-writing/
    │   └── brand-storytelling/
    │
    └── strategy/                      # Content Strategy Skills (6)
        ├── content-calendar/
        ├── brand-voice/
        ├── content-strategy/
        ├── content-atomization/
        ├── editorial-guidelines/
        └── content-audit/
```

## When to Use

Add this submodule when your project involves:
- **Content Marketing**: Blog posts, articles, thought leadership
- **Technical Writing**: API documentation, user guides, tutorials
- **Brand Building**: Company narratives, storytelling, voice development
- **Content Operations**: Editorial calendars, content audits, workflow management
- **Multi-format Content**: Video scripts, newsletters, case studies

## Complementary Skills

These skills work well together:

### Blog Publishing Workflow
1. `content-strategy` → Define content pillars and goals
2. `brand-voice` → Establish tone and style
3. `content-calendar` → Plan publishing schedule
4. `blog-post-writing` → Create blog content
5. `content-audit` → Review and optimize existing content

### Documentation Creation
1. `content-strategy` → Define documentation goals
2. `editorial-guidelines` → Set writing standards
3. `technical-documentation` → Write API/SDK docs
4. `tutorial-content` → Create how-to guides

### Content Repurposing
1. `blog-post-writing` → Create long-form blog post
2. `content-atomization` → Repurpose into multiple formats
3. `video-scripts` → Convert to video content
4. `newsletter-writing` → Extract key points for newsletter

## Related Repos

| Repo | Purpose |
|------|---------|
| [claude-base](https://github.com/potentialInc/claude-base) | Core shared configuration |
| [claude-marketing](https://github.com/potentialInc/claude-marketing) | Marketing and conversion skills (complementary) |
| [claude-operations](https://github.com/potentialInc/claude-operations) | Operations and workflow skills |
| [claude-react](https://github.com/potentialInc/claude-react) | React frontend skills |
| [claude-nestjs](https://github.com/potentialInc/claude-nestjs) | NestJS backend skills |

## Future Expansion

Additional skills planned for future releases:

**Content Creation**:
- `podcast-scripts`: Podcast episodes, interview outlines, show notes
- `webinar-content`: Presentation scripts, slide content, interactive elements
- `whitepaper-ebook`: Long-form thought leadership, industry reports
- `interview-content`: Interview formats, Q&A articles, expert roundups
- `press-release`: News announcements, media releases
- `product-descriptions`: Feature descriptions, product documentation
- `faq-content`: FAQ structure, help center content
- `content-series`: Multi-part series planning, narrative arcs

**Content Strategy**:
- `content-gap-analysis`: Identifying missing content, competitor analysis
- `content-distribution`: Multi-channel distribution, syndication strategy
- `content-workflow`: Production workflow, approval process
- `content-brief-template`: Brief creation, writer briefing
- `content-research`: Research methodology, source credibility, fact-checking
- `content-seo`: On-page SEO for content, keyword integration
- `content-refresh`: Updating old content, evergreen maintenance
- `content-metrics`: KPI tracking, engagement analysis
- `content-taxonomy`: Content categorization, tagging strategy
- `content-quality-checklist`: Quality assurance, pre-publish checklist

## Contributing

To add new skills or improve existing ones:

1. Create skill folder in `skills/creation/` or `skills/strategy/`
2. Write comprehensive `SKILL.md` with frontmatter
3. Add trigger rules to `skill-rules.json`
4. Test with sample prompts
5. Update this README

## License

[Your License Here]

## Version

v1.0.0-mvp (14 skills)
