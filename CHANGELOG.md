# Changelog
## 2.0.4
README update referencing the new `apostrophe-forms` module.

## 2.0.3
Fixes the package URL in package.json. Adds keywords for NPM search. Improves error logging.

## 2.0.2
Assets were still being pushed `always`, which resulted in errors for any page that *didn't* contain the widgets and tried to run that javascript without `scene: 'user'`. Fixed! You should *not* need to set `scene: 'user'` for every page type on your site.

## 2.0.1
The documentation is complete and the examples are well-tested. Default styles are pushed to hide the "thank you" message until it replaces the form. Since `scene: 'user'` is set for the widget, we push the assets for the widget only for `scene: 'user'`, which saves overhead on pages that don't include it.

## 2.0.0
initial release
