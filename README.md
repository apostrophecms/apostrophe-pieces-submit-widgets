# apostrophe-pieces-submit-widgets

This module extends the [Apostrophe CMS](http://apostrophecms.org), allowing sie visitors to submit new [pieces](http://apostrophecms.org/docs/tutorials/getting-started/reusable-content-with-pieces.html) of any type you wish. The feature is packaged as a widget, so you can easily add it to any page template.

Here's how to set it up. Our example is based on the [apostrophe-events](https://www.npmjs.com/package/apostrophe-events) module, which extends pieces.

```javascript
// In app.js
modules: {
  // For example: we're using the "apostrophe-events" module
  // and we'll want submissions of events. "apostrophe-events"
  // extends apostrophe-pieces
  'apostrophe-events': {
    // Let's add an attachment field so the user can upload an image
    addFields: [
      {
        name: 'image',
        type: 'attachment',
        group: 'images',
        required: true
      }
    ]
  },
  // ** The name you give this module is significant. **
  // It should begin with the name of the pieces module you want
  // to add the submissions feature to, and end with -submit-widgets
  'apostrophe-events-submit-widgets': {
    // Your module extends this one, and adds capabilities
    // to your pieces module
    extend: 'apostrophe-pieces-submit-widgets',
    // Always spell out the schema field names the user is allowed to edit.
    // You almost certainly don't want them to have control
    // of the "published" field, for instance
    fields: [ 'title', 'body', 'image', 'startDate', 'endDate' ]
  },
  // Optional: if you want to let the public attach files.
  // See the `addFields` call above
  'apostrophe-permissions': {
    construct: function(self, options) {
      self.addPublic('edit-attachment');
    }
  }
}
```

**Notice that you don't configure this module directly — you add a module that extends it, with a name based on the name of your pieces module.** If you don't like the naming convention, you can name your module whatever you like, as long as you set the `piecesModuleName` option to the appropriate module's name.

Once you have the module set up, you can add a submission form widget to any `apos.area` or `apos.singleton` call:

```markdown
{{ apos.singleton(data.page, 'submit', 'apostrophe-events-submit') }}
```

> If you use a singleton, you will still need to click the "Add Submit [x]" button once on each page that contains the singleton. The plus side of this feature is that it means you don't have to enable the singleton on every page of the type and you can remove the submission form again.

## Moderating submissions

Newly submitted pieces are initially unpublished. This makes them easy to moderate: just click on the admin bar, choose the type of piece, click "Manage" and use the "Published" filter to view unpublished pieces. Then edit them and set them to "Published: Yes" if appropriate.

> This module also adds a `submitted: true` property. If you wish, you can add that to your pieces module's schema as a boolean field and configure a filter for it so you can always distinguish between visitor submissions and your own unpublished content.

## Contact forms

It might not be obvious, but this module also works great for "contact us" forms. Just create a pieces module with appropriate schema fields and use this widget to accept the submissions. You can override the `beforeInsert` method to send email, if desired.

**Note:** If forms may need to change periodically or website users need to be able to create and edit form without help from a developer, [Apostrophe Forms](https://www.npmjs.com/package/apostrophe-forms) is likely a better choice.

## Submitting images and files

You'll notice that even if your schema contains an area with `apostrophe-images` and `apostrophe-files` widgets and the field is part of your `fields` array, the public still can't submit them for permissions reasons.

This is, by and large, a good thing. You don't want the public to have unrestricted access to browse your media library, or clutter it up.

Instead, define a widget that contains its own `attachment` field, and use that widget in an `area` or `singleton` field in your piece type's schema. What this does is associate a file directly with the widget and the piece it is a part of. It won't appear in the shared media library. It is directly attached to the piece.

**A `submitted-image-widgets` module is defined in the example `app.js` above.**

In `lib/modules/apostrophe-events-pages/views/show.html`, or anywhere else you have access to the piece, you can display the image like this:

```
<img src="{{ apos.attachments.url(data.piece.image, { size: 'full' }) }}" />
```

## Tip: simplicity is good

When accepting user submissions, it's usually better to minimize the complexity of what users can do, so they don't become confused by the process. Rather than areas, offer them a rich text singleton, a clearly labeled attachment field, etc.

## If it doesn't work: some tricky edge cases

> Keep in mind that you can do this for an entire blog by setting the flag for the `apostrophe-blog-pages` module, and so on for any subclass of `apostrophe-pieces-pages`.

### Are you trying to use the `apostrophe-images` and `apostrophe-files` widgets?

That's not a good fit with submissions, but you can define your own widgets with file attachments that are better suited to submissions. See above.

### AJAX and assets: when the form is dynamically loaded

This widget sets its `scene: 'user'` option, which automatically loads the extra user-oriented CSS and JavaScript it needs at the time the page containing the widget is loaded. 99% of the time, that's perfectly sufficient.

However, if you are loading the widget into the page later via an "infinite scroll" plugin like [jQuery Bottomless](https://github.com/punkave/jquery-bottomless), Apostrophe won't know the extra assets are needed until too late.

To address that scenario, set the `scene: 'user'` option for any page type that might load the widget via AJAX.

This is pretty uncommon, but the most likely example is an `apostrophe-pieces-page` like our event index page. It's easy to configure that page to do it because there is already a module managing it:

```javascript
'apostrophe-events-pages': {
  scene: 'user'
}
```

*If the page type doing the extra AJAX loading is an "ordinary" page type like `home` or `default` with no module managing it so far, you'll need to create a module that extends `apostrophe-custom-pages` and sets its `name` option to `home` or `default` as appropriate.*

## Changelog
2.0.4: README update referencing the new `apostrophe-forms` module.
2.0.3: Fixes the package URL in package.json. Adds keywords for NPM search. Improves error logging.
2.0.2: assets were still being pushed `always`, which resulted in errors for any page that *didn't* contain the widgets and tried to run that javascript without `scene: 'user'`. Fixed! You should *not* need to set `scene: 'user'` for every page type on your site.
2.0.1: the documentation is complete and the examples are well-tested. Default styles are pushed to hide the "thank you" message until it replaces the form. Since `scene: 'user'` is set for the widget, we push the assets for the widget only for `scene: 'user'`, which saves overhead on pages that don't include it.

2.0.0: initial release.
