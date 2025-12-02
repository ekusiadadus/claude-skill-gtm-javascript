# GTM Code Examples

Production-ready ES5 code examples for common GTM implementations.

## Basic dataLayer Operations

### Initialize dataLayer

```javascript
<script>
  window.dataLayer = window.dataLayer || [];
</script>
```

**Important:** Always place this ABOVE the GTM container snippet in `<head>`.

### Push Events

```javascript
<script>
(function() {
  'use strict';

  window.dataLayer = window.dataLayer || [];
  window.dataLayer.push({
    event: 'custom_event',
    event_category: 'engagement',
    event_action: 'click',
    event_label: 'button_cta'
  });
})();
</script>
```

### Push with Error Handling

```javascript
<script>
(function() {
  'use strict';

  try {
    window.dataLayer = window.dataLayer || [];
    window.dataLayer.push({
      event: 'tracked_action',
      action_type: 'form_submit'
    });
  } catch (e) {
    // Silent fail to prevent page errors
  }
})();
</script>
```

## Consent Mode v2 (2024 Required)

### Default Consent State

```javascript
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag() { dataLayer.push(arguments); }

  // Set default consent BEFORE GTM loads
  gtag('consent', 'default', {
    ad_storage: 'denied',
    ad_user_data: 'denied',
    ad_personalization: 'denied',
    analytics_storage: 'denied',
    functionality_storage: 'denied',
    personalization_storage: 'denied',
    security_storage: 'granted',
    wait_for_update: 500
  });
</script>
```

### Update Consent After User Choice

```javascript
<script>
(function() {
  'use strict';

  function updateConsent(preferences) {
    window.dataLayer = window.dataLayer || [];
    function gtag() { dataLayer.push(arguments); }

    gtag('consent', 'update', {
      ad_storage: preferences.marketing ? 'granted' : 'denied',
      ad_user_data: preferences.marketing ? 'granted' : 'denied',
      ad_personalization: preferences.marketing ? 'granted' : 'denied',
      analytics_storage: preferences.analytics ? 'granted' : 'denied',
      functionality_storage: preferences.functionality ? 'granted' : 'denied',
      personalization_storage: preferences.personalization ? 'granted' : 'denied'
    });
  }

  // Example: Call when user accepts all
  window.acceptAllCookies = function() {
    updateConsent({
      marketing: true,
      analytics: true,
      functionality: true,
      personalization: true
    });
  };

  // Example: Call when user accepts essential only
  window.acceptEssentialOnly = function() {
    updateConsent({
      marketing: false,
      analytics: false,
      functionality: false,
      personalization: false
    });
  };
})();
</script>
```

## GA4 Ecommerce Events

### View Item (Product Page)

```javascript
<script>
(function() {
  'use strict';

  window.dataLayer = window.dataLayer || [];

  // Clear previous ecommerce data
  window.dataLayer.push({ ecommerce: null });

  window.dataLayer.push({
    event: 'view_item',
    ecommerce: {
      currency: 'USD',
      value: 29.99,
      items: [{
        item_id: 'SKU_12345',
        item_name: 'Product Name',
        affiliation: 'Store Name',
        coupon: '',
        discount: 0,
        index: 0,
        item_brand: 'Brand Name',
        item_category: 'Category',
        item_category2: 'Subcategory',
        item_list_id: 'related_products',
        item_list_name: 'Related Products',
        item_variant: 'Blue',
        location_id: 'LOC_123',
        price: 29.99,
        quantity: 1
      }]
    }
  });
})();
</script>
```

### Add to Cart

```javascript
<script>
(function() {
  'use strict';

  function trackAddToCart(product, quantity) {
    window.dataLayer = window.dataLayer || [];
    window.dataLayer.push({ ecommerce: null });
    window.dataLayer.push({
      event: 'add_to_cart',
      ecommerce: {
        currency: product.currency || 'USD',
        value: product.price * quantity,
        items: [{
          item_id: product.id,
          item_name: product.name,
          item_brand: product.brand,
          item_category: product.category,
          item_variant: product.variant,
          price: product.price,
          quantity: quantity
        }]
      }
    });
  }

  // Expose to global scope for button onclick
  window.trackAddToCart = trackAddToCart;
})();
</script>
```

### Begin Checkout

```javascript
<script>
(function() {
  'use strict';

  function trackBeginCheckout(cartItems, cartTotal) {
    var items = [];

    for (var i = 0; i < cartItems.length; i++) {
      var item = cartItems[i];
      items.push({
        item_id: item.id,
        item_name: item.name,
        item_brand: item.brand,
        item_category: item.category,
        item_variant: item.variant,
        price: item.price,
        quantity: item.quantity
      });
    }

    window.dataLayer = window.dataLayer || [];
    window.dataLayer.push({ ecommerce: null });
    window.dataLayer.push({
      event: 'begin_checkout',
      ecommerce: {
        currency: 'USD',
        value: cartTotal,
        coupon: '',
        items: items
      }
    });
  }

  window.trackBeginCheckout = trackBeginCheckout;
})();
</script>
```

### Purchase (Transaction Complete)

```javascript
<script>
(function() {
  'use strict';

  window.dataLayer = window.dataLayer || [];
  window.dataLayer.push({ ecommerce: null });
  window.dataLayer.push({
    event: 'purchase',
    ecommerce: {
      transaction_id: 'T_12345_67890',
      affiliation: 'Online Store',
      value: 125.99,
      tax: 10.50,
      shipping: 5.99,
      currency: 'USD',
      coupon: 'SUMMER_SALE',
      items: [{
        item_id: 'SKU_001',
        item_name: 'Product One',
        item_brand: 'Brand A',
        item_category: 'Electronics',
        item_variant: 'Black',
        price: 59.99,
        quantity: 2
      }, {
        item_id: 'SKU_002',
        item_name: 'Product Two',
        item_brand: 'Brand B',
        item_category: 'Accessories',
        price: 6.01,
        quantity: 1
      }]
    }
  });
})();
</script>
```

### Refund

```javascript
<script>
(function() {
  'use strict';

  // Full refund
  window.dataLayer = window.dataLayer || [];
  window.dataLayer.push({ ecommerce: null });
  window.dataLayer.push({
    event: 'refund',
    ecommerce: {
      transaction_id: 'T_12345_67890',
      currency: 'USD',
      value: 125.99
    }
  });

  // Partial refund (specific items)
  window.dataLayer.push({ ecommerce: null });
  window.dataLayer.push({
    event: 'refund',
    ecommerce: {
      transaction_id: 'T_12345_67890',
      currency: 'USD',
      value: 59.99,
      items: [{
        item_id: 'SKU_001',
        quantity: 1
      }]
    }
  });
})();
</script>
```

## User Engagement Events

### Form Submission

```javascript
<script>
(function() {
  'use strict';

  function trackFormSubmit(formName, formId, success) {
    window.dataLayer = window.dataLayer || [];
    window.dataLayer.push({
      event: 'form_submit',
      form_name: formName,
      form_id: formId,
      form_success: success
    });
  }

  // Attach to form
  var form = document.getElementById('contact-form');
  if (form) {
    form.addEventListener('submit', function(e) {
      trackFormSubmit('Contact Form', 'contact-form', true);
    });
  }
})();
</script>
```

### Click Tracking

```javascript
<script>
(function() {
  'use strict';

  function trackClick(element, category, action, label) {
    window.dataLayer = window.dataLayer || [];
    window.dataLayer.push({
      event: 'click',
      click_category: category,
      click_action: action,
      click_label: label,
      click_element: element.tagName,
      click_text: element.innerText || element.value || '',
      click_url: element.href || ''
    });
  }

  // Track all CTA buttons
  var ctaButtons = document.querySelectorAll('.cta-button');
  for (var i = 0; i < ctaButtons.length; i++) {
    (function(button) {
      button.addEventListener('click', function() {
        trackClick(button, 'CTA', 'click', button.innerText);
      });
    })(ctaButtons[i]);
  }
})();
</script>
```

### Scroll Depth

```javascript
<script>
(function() {
  'use strict';

  var scrollMarks = [25, 50, 75, 100];
  var scrollMarksFired = {};

  function getScrollPercent() {
    var h = document.documentElement;
    var b = document.body;
    var st = 'scrollTop';
    var sh = 'scrollHeight';

    return Math.round(
      ((h[st] || b[st]) / ((h[sh] || b[sh]) - h.clientHeight)) * 100
    );
  }

  function checkScrollDepth() {
    var percent = getScrollPercent();

    for (var i = 0; i < scrollMarks.length; i++) {
      var mark = scrollMarks[i];
      if (percent >= mark && !scrollMarksFired[mark]) {
        scrollMarksFired[mark] = true;

        window.dataLayer = window.dataLayer || [];
        window.dataLayer.push({
          event: 'scroll_depth',
          scroll_threshold: mark,
          scroll_units: 'percent',
          page_path: window.location.pathname
        });
      }
    }
  }

  var scrollTimeout;
  window.addEventListener('scroll', function() {
    if (scrollTimeout) {
      clearTimeout(scrollTimeout);
    }
    scrollTimeout = setTimeout(checkScrollDepth, 100);
  }, { passive: true });
})();
</script>
```

### Video Tracking

```javascript
<script>
(function() {
  'use strict';

  function trackVideo(action, videoId, videoTitle, currentTime, duration) {
    var percent = Math.round((currentTime / duration) * 100);

    window.dataLayer = window.dataLayer || [];
    window.dataLayer.push({
      event: 'video_' + action,
      video_provider: 'html5',
      video_id: videoId,
      video_title: videoTitle,
      video_current_time: Math.round(currentTime),
      video_duration: Math.round(duration),
      video_percent: percent
    });
  }

  // Attach to HTML5 video elements
  var videos = document.querySelectorAll('video');
  for (var i = 0; i < videos.length; i++) {
    (function(video) {
      var videoId = video.id || 'video_' + i;
      var videoTitle = video.getAttribute('data-title') || videoId;

      video.addEventListener('play', function() {
        trackVideo('start', videoId, videoTitle, video.currentTime, video.duration);
      });

      video.addEventListener('pause', function() {
        trackVideo('pause', videoId, videoTitle, video.currentTime, video.duration);
      });

      video.addEventListener('ended', function() {
        trackVideo('complete', videoId, videoTitle, video.currentTime, video.duration);
      });
    })(videos[i]);
  }
})();
</script>
```

## User Properties

### Set User ID

```javascript
<script>
(function() {
  'use strict';

  window.dataLayer = window.dataLayer || [];

  // After user login
  window.dataLayer.push({
    event: 'login',
    user_id: 'USER_12345',
    method: 'email'
  });
})();
</script>
```

### User Properties

```javascript
<script>
(function() {
  'use strict';

  window.dataLayer = window.dataLayer || [];
  window.dataLayer.push({
    event: 'user_data',
    user_properties: {
      membership_tier: 'gold',
      lifetime_value: 1250.00,
      account_created: '2023-01-15',
      preferred_language: 'en'
    }
  });
})();
</script>
```

## DOM Manipulation

### Wait for Element

```javascript
<script>
(function() {
  'use strict';

  function waitForElement(selector, callback, maxWait) {
    maxWait = maxWait || 10000;
    var startTime = Date.now();

    function check() {
      var element = document.querySelector(selector);
      if (element) {
        callback(element);
      } else if (Date.now() - startTime < maxWait) {
        setTimeout(check, 100);
      }
    }

    check();
  }

  // Usage
  waitForElement('#dynamic-content', function(element) {
    window.dataLayer = window.dataLayer || [];
    window.dataLayer.push({
      event: 'content_loaded',
      content_id: element.id
    });
  });
})();
</script>
```

### Observe DOM Changes

```javascript
<script>
(function() {
  'use strict';

  if (typeof MutationObserver !== 'undefined') {
    var observer = new MutationObserver(function(mutations) {
      for (var i = 0; i < mutations.length; i++) {
        var mutation = mutations[i];
        if (mutation.type === 'childList') {
          // Check for specific element
          var target = document.querySelector('.success-message');
          if (target) {
            window.dataLayer = window.dataLayer || [];
            window.dataLayer.push({
              event: 'success_message_shown'
            });
            observer.disconnect();
            break;
          }
        }
      }
    });

    observer.observe(document.body, {
      childList: true,
      subtree: true
    });
  }
})();
</script>
```

## Performance & Timing

### Track Page Load Time

```javascript
<script>
(function() {
  'use strict';

  if (window.performance && window.performance.timing) {
    window.addEventListener('load', function() {
      setTimeout(function() {
        var timing = window.performance.timing;
        var pageLoadTime = timing.loadEventEnd - timing.navigationStart;
        var domReady = timing.domContentLoadedEventEnd - timing.navigationStart;
        var ttfb = timing.responseStart - timing.navigationStart;

        window.dataLayer = window.dataLayer || [];
        window.dataLayer.push({
          event: 'page_timing',
          page_load_time: pageLoadTime,
          dom_ready_time: domReady,
          time_to_first_byte: ttfb
        });
      }, 0);
    });
  }
})();
</script>
```

## Error Tracking

### JavaScript Error Handler

```javascript
<script>
(function() {
  'use strict';

  window.onerror = function(message, source, lineno, colno, error) {
    window.dataLayer = window.dataLayer || [];
    window.dataLayer.push({
      event: 'javascript_error',
      error_message: message,
      error_source: source,
      error_line: lineno,
      error_column: colno,
      error_stack: error && error.stack ? error.stack.substring(0, 500) : ''
    });

    // Return false to allow default error handling
    return false;
  };
})();
</script>
```
