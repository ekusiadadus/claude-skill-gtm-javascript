# GTM Testing & Debugging Checklist

Comprehensive checklist for validating GTM implementations before deployment.

## Pre-Deployment Code Review

### ES5 Compliance Check

- [ ] **No `const` or `let`** - All variables use `var`
- [ ] **No arrow functions** - All functions use `function()` syntax
- [ ] **No template literals** - All strings use concatenation with `+`
- [ ] **No destructuring** - Properties accessed individually (`obj.prop`)
- [ ] **No spread operator** - Use `concat()` or `apply()` instead
- [ ] **No default parameters** - Use `||` or ternary in function body
- [ ] **No for-of loops** - Use traditional `for (var i = 0; ...)` loops
- [ ] **No object method shorthand** - Use explicit `: function()` syntax
- [ ] **No block-scoped functions** - Functions in if/for use `var f = function()`
- [ ] **No Promises/async/await** - Use callbacks or XHR
- [ ] **No optional chaining** (`?.`) - Use manual null checks
- [ ] **No nullish coalescing** (`??`) - Use `||` with explicit checks

### Code Quality

- [ ] **IIFE wrapper** - Code wrapped in `(function() { 'use strict'; ... })();`
- [ ] **Error handling** - Critical operations in `try-catch` blocks
- [ ] **dataLayer initialization** - `window.dataLayer = window.dataLayer || [];`
- [ ] **No global pollution** - Minimal global variable exposure
- [ ] **Clear comments** - Complex logic documented
- [ ] **No console.log** - Debug statements removed

## GTM Preview Mode Testing

### Accessing Preview Mode

1. Open GTM container in browser
2. Click **Preview** button (top right, near Submit)
3. Enter website URL in Tag Assistant
4. New tab opens with debug panel connected

### Required Events Check

Every page load MUST show these events in the Event Timeline:

1. [ ] **Consent Initialization** - First event
2. [ ] **Initialization** - Second event
3. [ ] **Container Loaded** - GTM container ready
4. [ ] **DOM Ready** - DOM fully parsed
5. [ ] **Window Loaded** - All resources loaded

### Tag Verification

For each custom tag:

- [ ] **Tag appears in "Tags Fired"** when expected
- [ ] **Tag appears in "Not Fired"** when conditions not met
- [ ] **Correct trigger** - Verify trigger name matches expectation
- [ ] **No errors** - Tag icon shows green checkmark, not red X
- [ ] **Variable values correct** - Check Variables tab for expected values

### Common Preview Mode Issues

| Issue | Solution |
|-------|----------|
| Debug window won't connect | Check GTM snippet is on page; try different URL |
| Disconnects on navigation | Disable Tag Assistant extension |
| VPN blocking | Allowlist Google domains or disable VPN |
| AdBlock interference | Disable ad blockers for testing |
| Cookie blocked | Ensure `_TAG_ASSISTANT` cookie is allowed |

## dataLayer Validation

### Browser Console Check

Open browser DevTools (F12) and run:

```javascript
// View entire dataLayer
console.log(window.dataLayer);

// View last 5 pushes
console.log(window.dataLayer.slice(-5));

// Find specific event
window.dataLayer.filter(function(item) {
  return item.event === 'your_event_name';
});
```

### dataLayer Event Validation

For each dataLayer.push:

- [ ] **Event name correct** - Matches trigger configuration
- [ ] **All parameters present** - Required fields populated
- [ ] **Data types correct** - Numbers are numbers, strings are strings
- [ ] **No undefined values** - All values have valid data
- [ ] **Ecommerce object cleared** - `push({ ecommerce: null })` before new ecommerce data

### Ecommerce Event Checklist

| Event | Required Fields | Optional Fields |
|-------|-----------------|-----------------|
| `view_item` | currency, value, items[] | item_list_id, item_list_name |
| `add_to_cart` | currency, value, items[] | - |
| `remove_from_cart` | currency, value, items[] | - |
| `begin_checkout` | currency, value, items[] | coupon |
| `add_payment_info` | currency, value, items[] | coupon, payment_type |
| `add_shipping_info` | currency, value, items[] | coupon, shipping_tier |
| `purchase` | transaction_id, currency, value, items[] | tax, shipping, coupon, affiliation |
| `refund` | transaction_id | currency, value, items[] |

### Items Array Validation

Each item in `items[]` should have:

- [ ] `item_id` - SKU or product ID
- [ ] `item_name` - Product name
- [ ] `price` - Unit price (number, not string)
- [ ] `quantity` - Quantity (number, not string)
- [ ] `item_brand` - (recommended)
- [ ] `item_category` - (recommended)
- [ ] `item_variant` - (if applicable)

## GA4 DebugView Validation

### Accessing DebugView

1. Open GA4 property in browser
2. Navigate to **Admin > DebugView** (left sidebar)
3. Select your device from the dropdown

### Enabling Debug Mode

Add to your gtag configuration:

```javascript
gtag('config', 'G-XXXXXXXX', {
  debug_mode: true
});
```

Or add URL parameter: `?gtm_debug=1`

### DebugView Checks

- [ ] **Events appearing** - Events show in real-time stream
- [ ] **Event names correct** - Matching expected names
- [ ] **Parameters visible** - Click event to expand parameters
- [ ] **User properties set** - Check user properties section
- [ ] **No duplicate events** - Each action fires once

## Consent Mode Validation

### Before User Consent

- [ ] **Default consent set** - `consent default` command fires
- [ ] **Storage denied** - ad_storage, analytics_storage = 'denied'
- [ ] **Tags blocked** - Marketing tags not firing
- [ ] **Cookieless pings** - If advanced mode, verify pings sent

### After User Grants Consent

- [ ] **Consent update fires** - `consent update` command visible
- [ ] **Storage granted** - Appropriate consents = 'granted'
- [ ] **Tags now fire** - Previously blocked tags now active
- [ ] **Cookies set** - Check Application > Cookies in DevTools

### Consent Parameters (v2)

| Parameter | Purpose |
|-----------|---------|
| `ad_storage` | Advertising cookies (Google Ads, Floodlight) |
| `ad_user_data` | NEW in v2 - Send user data to Google for ads |
| `ad_personalization` | NEW in v2 - Personalized advertising |
| `analytics_storage` | Analytics cookies (GA4) |
| `functionality_storage` | Functionality cookies |
| `personalization_storage` | Personalization cookies |
| `security_storage` | Security-related cookies |

## Cross-Browser Testing

### Browsers to Test

- [ ] **Chrome** (latest)
- [ ] **Firefox** (latest)
- [ ] **Safari** (latest)
- [ ] **Edge** (latest)
- [ ] **Mobile Safari** (iOS)
- [ ] **Chrome Mobile** (Android)

**Note:** IE11 support ended July 15, 2024 - no longer required.

### Mobile-Specific Checks

- [ ] **Touch events tracked** - Click tracking works on touch
- [ ] **Viewport triggers** - Scroll tracking accurate
- [ ] **Performance acceptable** - No noticeable lag
- [ ] **Consent modal works** - Consent flow functional

## Performance Validation

### Page Speed Impact

- [ ] **GTM container size** - Under 200KB recommended
- [ ] **Async loading** - GTM snippet uses async
- [ ] **No blocking scripts** - Custom HTML doesn't block rendering
- [ ] **Event listeners passive** - Scroll/touch use `{ passive: true }`

### Lighthouse Check

Run Lighthouse audit and verify:

- [ ] **No significant performance drop** after GTM
- [ ] **No console errors** from GTM code
- [ ] **Third-party impact** visible in Lighthouse report

## Publishing Checklist

### Pre-Publish

- [ ] **Version name descriptive** - Describes changes
- [ ] **Version notes complete** - Documents what changed
- [ ] **Workspace clean** - No unrelated pending changes
- [ ] **Tested on staging** - Full test cycle complete

### Post-Publish Verification

- [ ] **Container published successfully** - No errors
- [ ] **Production site verified** - Tags firing correctly
- [ ] **GA4 receiving data** - Check Real-time reports
- [ ] **No console errors** - Browser console clean
- [ ] **Core Web Vitals stable** - No performance regression

## Troubleshooting Guide

### Tag Not Firing

1. Check trigger conditions in Preview Mode
2. Verify event name matches exactly (case-sensitive)
3. Check for JavaScript errors blocking execution
4. Ensure dataLayer.push happens before tag expects it

### Wrong Data in GA4

1. Verify dataLayer structure matches GA4 schema
2. Check for type mismatches (string vs number)
3. Ensure `ecommerce: null` pushed before new ecommerce data
4. Verify parameter names use correct snake_case

### Consent Mode Issues

1. Verify default consent fires BEFORE GTM container
2. Check consent banner integration is correct
3. Verify `wait_for_update` value is sufficient
4. Test with different consent scenarios

### Preview Mode Not Working

1. Clear browser cache and cookies
2. Try incognito/private browsing
3. Disable all browser extensions
4. Check if VPN is blocking
5. Verify GTM snippet is on the page
6. Try entering page URL directly in Tag Assistant

## Quick Reference

### GTM Events in Order

```
1. Consent Initialization
2. Initialization
3. Container Loaded (Pageview)
4. DOM Ready
5. Window Loaded
6. [Custom Events]
```

### Debug Console Commands

```javascript
// View dataLayer
dataLayer

// Filter by event
dataLayer.filter(e => e.event)

// Last push
dataLayer[dataLayer.length - 1]

// Watch for changes
Object.observe && console.log('Use MutationObserver instead')
```

### Emergency Rollback

If production issues detected:

1. Open GTM > Versions
2. Find last working version
3. Click three dots > Publish
4. Confirm publish
5. Verify site is working
