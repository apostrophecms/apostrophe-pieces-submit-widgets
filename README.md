# apostrophe-pieces-submit-widgets

This module can be extended by `apostrophe-pieces` or a subclass to handle user submitted piece content on the front end of your site.

## Installation

    $ npm install apostrophe-pieces-submit-widgets

## Example Configuration

In this example we have a piece type `events` and we would like the user to be able to submit an event from a front end form for moderation in Apostrophe.

In `app.js` we initialize our events piece type and our widget from where we can submit a comment.

```javascript
// In app.js
bundles: [ 'apostrophe-events'],
modules: {
  'events': {
    extend: 'apostrophe-pieces'
  },
  'events-submit-widgets': {
    extend: 'apostrophe-pieces-submit-widgets',
    // Schema fields to expose on front end
    fields: [ 'eventName', 'eventDetails' ]
  }
}
```

We also need to set `scene: user` in our `apostrophe-paces` subclass `events-pages`, where we want to display our form. You can do this when configuring `apostrophe-pieces-pages`, for instance, or in any subclass of `apostrophe-custom-pages`.

```javascript
// In events-pages/index.js
module.exports = {
  name: 'events-page',
  // So that we can display the submission form
  scene: 'user'
}
```

Our widget `events-submit-widgets` can now be added to a singleton or area on our events `show` or `index` view and will expose an Apostrophe schema form with the given fields specified.
