# Claude Skill: GTM JavaScript

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Claude Code](https://img.shields.io/badge/Claude-Code-blueviolet)](https://claude.ai/code)
[![GTM](https://img.shields.io/badge/Google-Tag%20Manager-4285F4)](https://tagmanager.google.com/)

> A Claude Code skill for generating ES5-compliant JavaScript code for Google Tag Manager Custom HTML tags.

## Why This Skill?

**Google Tag Manager's Custom HTML tags only support ES5 JavaScript.** Using modern ES6+ syntax (like `const`, `let`, arrow functions, template literals) causes compilation errors and prevents tag publishing.

This skill ensures Claude Code generates GTM-compatible code every time, following the latest best practices for 2024-2025.

## Features

- **ES5-Only Code Generation** - Never get GTM compilation errors again
- **2024-2025 Updates** - Includes Consent Mode v2, Server-side GTM, GA4 ecommerce
- **Complete Reference** - ES6 to ES5 conversion patterns
- **Production Examples** - Ready-to-use code for common tracking scenarios
- **Testing Checklist** - Comprehensive debugging and validation guide

## Installation

### Option 1: Global Installation (All Projects)

```bash
# Clone to your home directory
git clone https://github.com/ekusiadadus/claude-skill-gtm-javascript.git ~/.claude/skills/gtm-javascript

# Or copy just the skill files
mkdir -p ~/.claude/skills/gtm-javascript
cp -r skills/gtm-javascript/* ~/.claude/skills/gtm-javascript/
```

### Option 2: Project-Specific Installation

```bash
# Clone into your project
mkdir -p .claude/skills
cp -r skills/gtm-javascript .claude/skills/
```

## Usage

Once installed, Claude Code will automatically use this skill when you ask it to:

- Write GTM Custom HTML tags
- Create dataLayer.push() code
- Implement GA4 ecommerce tracking
- Set up Consent Mode v2
- Debug GTM implementations

### Example Prompts

```
"Write a GTM Custom HTML tag to track form submissions"

"Create dataLayer code for GA4 purchase event"

"Implement Consent Mode v2 with a cookie consent banner"

"Convert this ES6 code to GTM-compatible ES5"
```

## What's Included

| File | Description |
|------|-------------|
| `SKILL.md` | Main skill definition with core rules and patterns |
| `reference.md` | Complete ES6 → ES5 conversion reference |
| `examples.md` | Production-ready code examples |
| `checklist.md` | Testing and debugging checklist |

## Key Constraints

### Prohibited ES6+ Features

| Feature | ES6+ (Prohibited) | ES5 (Required) |
|---------|-------------------|----------------|
| Variables | `const`, `let` | `var` |
| Functions | `() => {}` | `function() {}` |
| Strings | `` `${var}` `` | `'str' + var` |
| Destructuring | `{a, b} = obj` | `var a = obj.a` |
| Spread | `[...arr]` | `arr.concat()` |
| for-of | `for (x of arr)` | `for (var i...)` |

### 2024-2025 Important Updates

| Update | Date | Impact |
|--------|------|--------|
| IE11 Support Ended | July 15, 2024 | No longer need IE11 compatibility |
| Consent Mode v2 Required | March 2024 | New `ad_user_data`, `ad_personalization` parameters |
| Google Ads Auto-Tag | April 10, 2025 | Containers auto-load Google tag first |

## Code Examples

### Basic dataLayer Push

```javascript
(function() {
  'use strict';

  window.dataLayer = window.dataLayer || [];
  window.dataLayer.push({
    event: 'custom_event',
    event_category: 'engagement',
    event_label: 'button_click'
  });
})();
```

### GA4 Purchase Event

```javascript
(function() {
  'use strict';

  window.dataLayer = window.dataLayer || [];
  window.dataLayer.push({ ecommerce: null });
  window.dataLayer.push({
    event: 'purchase',
    ecommerce: {
      transaction_id: 'T_12345',
      value: 99.99,
      currency: 'USD',
      items: [{
        item_id: 'SKU_001',
        item_name: 'Product Name',
        price: 99.99,
        quantity: 1
      }]
    }
  });
})();
```

### Consent Mode v2

```javascript
window.dataLayer = window.dataLayer || [];
function gtag() { dataLayer.push(arguments); }

gtag('consent', 'default', {
  ad_storage: 'denied',
  ad_user_data: 'denied',
  ad_personalization: 'denied',
  analytics_storage: 'denied'
});
```

## Resources

- [GTM Developer Guide](https://developers.google.com/tag-manager)
- [GA4 Ecommerce](https://developers.google.com/analytics/devguides/collection/ga4/ecommerce)
- [Consent Mode](https://developers.google.com/tag-platform/security/guides/consent)
- [Sandboxed JavaScript APIs](https://developers.google.com/tag-platform/tag-manager/templates/sandboxed-javascript)

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Author

**ekusiadadus**

- GitHub: [@ekusiadadus](https://github.com/ekusiadadus)

## Acknowledgments

- Google Tag Manager documentation
- Simo Ahava's analytics blog
- The Claude Code community
