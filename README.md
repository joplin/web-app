# Joplin Web App

This repository deploys the Joplin web client to GitHub pages.

The web app source code can be found in [the main Joplin repository](https://github.com/laurent22/joplin).


## FAQ

### What is it?

Joplin Web is Joplin Mobile, running in a web browser.

### Where is my data stored?

Like Joplin Mobile, Joplin Web is local-first. Notes and attachments are stored locally on your computer, but can optionally be synced with one of the supported sync targets. As a result, Joplin Web can be used offline.

### What browsers does it support?

The Joplin web app works best in recent versions of Chrome and Safari. It can also be used in Firefox, however, it may take a very long time to start.

Some features are available only on certain platforms:
- File system sync[^1] (as of July 2024):
	- ✅ Chrome (desktop)
	- ❌ Chrome (Android)
	- ❌ Safari
	- ❌ Firefox
- Share note content[^2] (as of July 2024):
	- ✅ Safari
	- ✅ Chrome (Android)
	- ❌ Chrome (Desktop)
	- ❌ Firefox
- Insert images from a camera (as of July 2024):
	- ✅ Safari
	- ✅ Chrome (Android)
	- ❌ Chrome (Desktop)
	- ❌ Firefox
- Drop images and files from another app (as of July 2024):
	- ✅ Chrome (Desktop), Safari, Firefox (Desktop)
	- ❌ Chrome (Android)

[^1]: Requires [support for showDirectoryPicker](https://caniuse.com/?search=showDirectoryPicker).
[^2]: Requires [Web Share API support](https://caniuse.com/?search=web%20share%20api).


