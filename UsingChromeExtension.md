# Introduction #

Au-to-do's source includes a Chrome extension that provides a:
  * Link to Au-to-do.
  * Counter indicating how many incidents have been assigned to you.
  * List of incidents assigned to you, and links directly to those incidents within Au-to-do.

Keep reading to see how to configure the Chrome extension to point to your installation of Au-to-do, and install it in your copy of Chrome.

# Configuring the extension #

All of the source code for the Chrome extension is included in the `extension` directory of the Au-to-do source. To configure your copy of the extension to point to your instance of Au-to-do, you will need to edit strings in two files.

First, open `extension.js`. Replace the value of `google.devrel.samples.autodo.Extension.APP_URL` with the complete link to your installation of Au-to-do. If you want the extension to point to an instance running locally, use the following:

```
google.devrel.samples.autodo.Extension.APP_URL =
    'http://localhost:9999';
```

Second, open `manifest.json`. Replace the first line of the `permissions` array with the same URL you used above, followed by `/*`. For example:

```
"permissions": [
    "http://localhost:9999/*",
    "background",
    "tabs"
  ],
```

This enables the Chrome extension to make cross-domain calls to any page in Au-to-do. Specifically, this allows the Chrome extension to make requests to the Au-to-do API, to retrieve incidents assigned to you.

# Installing the extension #

Once you've configured the extension, installation is straightforward. Visit the `chrome://extensions/` page within Chrome. Click `Load unpacked extension` and select the `extension` directory of Au-to-do. Now the extension is installed. If you make changes to the extension, you can reload the extension by clicking the **Reload** button. You may also disable or remove the extension.

# Using the extension #

After the extension is installed, it will appear to the right of the URL bar by default. The extension requires you to be logged into Au-to-do and will display a sad face, :(, on a red background if login is required. Clicking the icon will display a prompt to log in, and a descriptive message.

If you're logged in, the sad face will be replaced by a counter on a green background, indicating the number of incidents assigned to you. Clicking the icon will present a link to Au-to-do and a list of incidents assigned to you. Clicking one of the incidents will open it (in Au-to-do) in a new tab.

The Chrome extension will automatically refresh itself every 30 seconds. To make this more or less frequent, update the value of `google.devrel.samples.autodo.Extension.FETCH_FREQ` in `extension.js`. The time is measured in milliseconds, so make sure to take this into account when setting a new interval.

# Sharing the extension #

If you'd like others to be able to use the Chrome extension with your version of Au-to-do, you may optionally pack the extension into the `.crx` format for easy distribution. On the `chrome://extensions/` page, click the **Pack extension...** button. In the first box, select the `extension` directory again and click **Pack Extension**. This will create a packed extension called `extension.crx` and a key file you will need in order to update that specific extension. You can distribute the `extension.crx` file to others who you want to access your version of Au-to-do.