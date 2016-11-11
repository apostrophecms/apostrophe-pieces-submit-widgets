# apostrophe-submit-pieces-widgets

```javascript
// In app.js
modules: {
  // For example...
  'apostrophe-events': {
  },
  'apostrophe-submit-events-widgets': {
    extend: 'apostrophe-submit-pieces-widgets',
    // Allow all
    // fields: [ 'title', 'body' ]
  }
}
```

**All page types that will display the submission form need to set the `scene` option to `user` when doing so.** You can do this when configuring `apostrophe-pieces-pages`, for instance, or in any subclass of `apostrophe-custom-pages`.
