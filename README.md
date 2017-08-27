# apostrophe-pieces-submit-widgets

This module lets your site visitors submit new pieces of any type you wish. The feature is packaged as a widget, so you can easily add it to any page template, provided you take care of a few requirements as noted below.

Here's how to set it up:

```javascript
// In app.js
modules: {
  // For example: we're using the "apostrophe-events" module
  // and we'll want submissions of events
  'apostrophe-events': {
    // Any custom configuration for the events module
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
    fields: [ 'title', 'body' ]
  },
  'default-pages': {
    extend: 'apostrophe-custom-pages',
    // Necessary to allow use of the submit widget on this page type
    scene: 'user'
  },
  // Optional: if you want to use attachment fields in widgets
  // in your submission forms, you must allow the public to upload files.
  // See below for how to define a widget that works well for this
  'apostrophe-permissions': {
    construct: function(self, options) {
      self.addPublic('edit-attachment');
    }
  },
  // Here's a simple widget that contains an image attachment field.
  // Use this in your schema for the submitted piece type,
  // rather than apostrophe-images, to solve permissions problems and
  // keep user content out of your media library
  'submitted-image-widgets': {
    extend: 'apostrophe-widgets',
    label: 'Image',
    addFields: [
      {
        name: 'image',
        type: 'attachment',
        group: 'images',
        required: true
      }
    ]
  }  
}
```

**Notice that you don't configure this module directly — you add a module that extends it, with a name based on the name of your pieces module.** If you don't like the naming convention, you can name your module whatever you like, as long as you set the `piecesModuleName` option to the appropriate module's name.

**All page types that will display the submission form widget need to set the `scene` option to `user` when doing so.** You can do this when configuring `apostrophe-pieces-pages`, for instance, or in any subclass of `apostrophe-custom-pages`. The latter is the easiest way to add this capability to a "plain old page type" like `default`.

Once you have the module set up, you can add a submission form widget to any `apos.area` or `apos.singleton` call:

```markdown
{{ apos.singleton(data.page, 'submit', 'apostrophe-events-submit') }}
```

> If you use a singleton, you will still need to click the "Add Submit [x]" button once on each page that contains the singleton. The plus side of this feature is that it means you don't have to enable the singleton on every page of the type and you can remove the submission form again.

## Moderating submissions

Newly submitted pieces are initially unpublished. This makes them easy to moderate: just click on the admin bar, choose the type of piece, click "Manage" and use the "Published" filter to view unpublished pieces. Then edit them and set them to "Published: Yes" if appropriate.

## Contact forms

It might not be obvious, but this module also works great for "contact us" forms. Just create a pieces module with appropriate schema fields and use this widget to accept the submissions. You can override the `beforeInsert` method to send email, if desired.

## Submitting images and files

You'll notice that even if your schema contains an area with `apostrophe-images` and `apostrophe-files` widgets and the field is part of your `fields` array, the public still can't submit them for permissions reasons.

This is, by and large, a good thing. You don't want the public to have unrestricted access to browse your media library, or clutter it up.

Instead, define a widget that contains its own `attachment` field, and use that widget in an `area` or `singleton` field in your piece type's schema. What this does is associate a file directly with the widget and the piece it is a part of. It won't appear in the shared media library. It is directly attached to the piece.

**A `submitted-image-widgets` module is defined in the example `app.js` above.** You'll also need to create `lib/modules/submitted-image-widgets/widget.html`, to actually display it:

```
<img src="{{ apos.attachments.url(data.widget.image, { size: 'full' }) }}" />
```

## Tip: simplicity is good

When accepting user submissions, it's usually better to minimize the complexity of what users can do, so they don't become confused by the process. Rather than areas, offer them a rich text singleton, a submitted-image singleton, etc.

## If it doesn't work

### Did you forget `scene: 'user'`?

You probably haven't set `scene: 'user'` for your page type. The page type that will feature the widget must set that option to load all of the CSS and JavaScript normally reserved for logged-in users. See the example above.

> Keep in mind that you can do this for an entire blog by setting the flag for the `apostrophe-blog-pages` module, and so on for any subclass of `apostrophe-pieces-pages`.

### Are you trying to use the `apostrophe-images` and `apostrophe-files` widgets?

That's not a good fit with submissions, but you can define your own widgets with file attachments that are better suited to submissions. See above.

