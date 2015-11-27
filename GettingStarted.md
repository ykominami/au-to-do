# Introduction #

The following guide will walk you through the steps to configure your own instance of Au-to-do.

This guide assumes general knowledge of Python and Google App Engine, and is not recommended for beginners. For those just starting with App Engine, check out a simpler example such as the [Guestbook application](http://code.google.com/appengine/docs/python/gettingstarted/introduction.html).

**Au-to-do is designed and tested with Python 2.5 and 2.6 and may not work with Python 2.7.**

# Getting the code #

Start by downloading a copy of the Au-to-do [source](http://code.google.com/p/au-to-do/source/checkout) from this project's git repository. This will create a directory on your local file system containing most of the files required by Au-to-do.

Au-to-do also requires a copy of the [Google APIs Client Library for Python](http://code.google.com/p/google-api-python-client/). The easiest way to install the library on your system, and make it available to Au-to-do is to use [easy\_install](http://packages.python.org/distribute/easy_install.html). First, install the library on your system:

```
easy_install --upgrade google-api-python-client
```

This may require `sudo` privileges on your machine. If you encounter an error, updating `setuptools` may be required:

```
easy_install --upgrade setuptools
```

Once the library is installed, enable your local copy of Au-to-do to use it:

```
enable-app-engine-project /path/to/au-to-do
```

At this point, the Python client library and its dependencies should now be installed within the root of your Au-to-do directory.

# Start a local instance #

Now that you have a complete set of code to run Au-to-do, it's time to fire up a local instance of the application, using dev\_appserver.py:

```
/path/to/dev_appserver.py --port=9999 /path/to/au-to-do
```

If you visit http://localhost:9999/ you should now see the running instance of Au-to-do. The next few sections will walk you through the remaining configuration steps to make it fully operational.

**Note:** that if Python 2.7 is the default version of Python on your system, you should prefix the above command with the full path to an earlier Python runtime. For instance, to run `dev_appserver.py` with Python 2.5, on Mac OS X, the command will look something like this:

```
/usr/bin/python2.5 /usr/local/bin/dev_appserver.py --port=9999 /path/to/au-to-do
```

# Configuration #

## Creating an application ID ##

To allow Au-to-do to work on a production instance of Google App Engine, you'll need to register an application ID using the [Administration Console](https://appengine.google.com/). Once the ID is registered, edit app.yaml (in the root of the Au-to-do directory) to include this value, instead of the placeholder. Remember this ID, you'll need it later.

## Creating a project in the Google APIs console ##

Au-to-do uses several APIs which are enabled and configured via the [Google APIs Console](https://code.google.com/apis/console). Visit the console and create a new project.

Once the project is created, click on the **Services** link in the left navigation. On the list of APIs that displays, enable the following APIs:
  * Google Cloud Storage
  * Prediction API

Next, visit the **Billing** link in the left navigation. While Google Cloud Storage provides [free quota](https://code.google.com/apis/storage/docs/pricingandterms.html#pricing), billing must be enabled to create buckets. Enable billing using Google Checkout.

Enabling Google Cloud Storage adds a new item into the left navigation, **Google Cloud Storage**. Click this link. The resulting page will present you with some information on how to identify your project when communicating with the Google Cloud Storage API. Au-to-do uses the Interoperable Access technique for communicating with Cloud Storage. Click the button **Make this my default project for interoperable storage access**, after reading and accepting the disclaimer. _If you have other projects that require Interoperable Access, clicking this button may break them._

Once Interoperable Access is enabled click the **Interoperable Access** link in the left navigation below **Google Cloud Storage**. This page will present you with a list of Interoperable Storage access keys, and the option to generate more. If no key is listed click the **Generate new key** button.

Open up settings.py from the root of the Au-to-do source directory. Copy and paste the value in the Access Key column into the string assigned to `GS_INTEROPERABLE_ACCESS`. Take the secret and paste it into `GS_INTEROPERABLE_SECRET`. Finally, modify the `GS_BUCKET` value so that it's prefixed with your App Engine application ID. For example, if your application ID is 'my-au-to-do', set `GS_BUCKET` to 'my-au-to-do-autodo-predictionmodels'. Save your changes, but keep the file open.

Click on the **Storage Access** link in the left navigation to return to the main Google Cloud Storage page. Then, click **Google Cloud Storage Manager** link, which will open a view of your buckets stored in Google Cloud Storage. In the top right, click **Create Bucket** and enter the value you used for `GS_BUCKET` in settings.py.

Now, return the APIs Console window, and click on **API Access** in the left navigation. Click on the **Create an OAuth 2.0 Client ID...** button. This will display a window with some options. Leave "Application type" at the current setting. Next to "Your site or hostname" click **more options**. In the first box, enter the following URLs, replace `APP_ID` with your App Engine application ID:

```
http://APP_ID.appspot.com/oauth2callback
http://localhost:9999/oauth2callback
```

Then for the second box:

```
http://APP_ID.appspot.com
http://localhost:9999
```

Finally, click **Create client ID**. This will close the dialog and show you a page listing a newly-created key and secret for this set of URIs. Copy and paste the value next to Client ID into the `CLIENT_ID` value in settings.py. Copy and paste the value next to Client secret into the `CLIENT_SECRET` value in settings.py. Save and close this file.

# Deploying to production #

Your instance of the application should now be ready to commit to production. Run the following command to upload your instance to App Engine:

```
/path/to/appcfg.py update /path/to/au-to-do
```

# Post-deployment configuration #

## Setting OAuth2 admin credentials ##

In order to access the Prediction API, Au-to-do needs to be authorized access by one of the administrators of the APIs Console project that you created earlier. Visit your deployed instance of Au-to-do, hit the 'gear' icon in the top right corner, and click the **Use my credentials** button that displays. This will perform the OAuth2 dance, and grant the application access to the Prediction API.

## Directing mail to the application ##

In order for Au-to-do to have information to display, it needs a source of information. Currently, Au-to-do generates "inicidents" by receiving emails from mailing lists and constructing email threads from those emails (one thread equals one incident).

Your instance of Au-to-do is already configured to receive mail by a setting in app.yaml. However, mail needs to be directed to the application. The easiest way to do this, is by subscribing Au-to-do to a mailing list that you own, such as one hosted on [Google Groups](http://groups.google.com).

Create a group in Google Groups, unless you already have one you'd like to use. Then, click on **Invite members** in the right navigation inside the Groups interface. Click **Add members directly**. In the first box that appears, enter `mail@APP_ID.appspotmail.com`, replacing `APP_ID` with your application's ID. Enter a welcome message into the second box (it will be ignored by Au-to-do, but is required by Groups). Make sure the email subscription option remains at the default, which is to send an email for every update. Finally, click **Add members**. If this is successful, Au-to-do will now receive a copy of all mail sent to this Group.

To confirm that this worked, send an email to your Group. After a few seconds, return to Au-to-do. Click on **Unassigned** in the left navigation. You should see a single incident with the title of your recent email.

# Next steps #

Now that Au-to-do is configured, it's time to start playing with the features of the application. Check out UsingTheApp for more information.