# Introduction #

Au-to-do users can provide labels to help categorize incoming incidents. Au-to-do uses the [Google Prediction API](http://code.google.com/apis/predict/) to suggest labels for uncategorized (or partially categorized) incidents.

# Labeling an incident #

To label an incident, you must first decide which incident you want to label. Click the **Unassigned** link in the left navigation of Au-to-do. Then check the checkbox next to an incident you want to label. Click on the **Label** button above the incident view and enter a label in the box.

You define the labels. Possible examples include:
  * The tone of the incident (happy, angry, etc.)
  * Contents of the message (discussion of one API versus another)
  * Who might handle the incident the best (Dan versus Pat versus Ellen)

In Au-to-do, we have chosen to divide labels into groups. Each group gets its own predictive model in the Prediction API. When a user provides a label in the Au-to-do, it's just a string, but with two parts. The first part is the label group, such as Tone, API, or Owner (from the three examples above). The second part is the categorization within the group. For the Tone group, this might be: Happy, Angry, or Neutral.

If you want to indicate that a message has a happy tone, you'd enter `Tone-Happy` in the label box and then click **Apply**. Here are more example labels corresponding to the example groups above.

For the Tone group:
  * `Tone-Happy`
  * `Tone-Angry`
  * `Tone-Neutral`

For the Contents group:
  * `Content-AppEngine`
  * `Content-Prediction`
  * `Content-CloudStorage`

For the Owners group:
  * `Owner-Dan`
  * `Owner-Pat`
  * `Owner-Ellen`

As noted above, these are just examples. You can create your own label groups and categories within the groups.

# How labels are used #

The Prediction API uses the labels you provide as examples for each training model. Each incident that you tag with a label becomes training data for future categorization. The more examples you provide, the better the Prediction API will be at finding similar incidents in the incoming stream.

The Prediction API classifies an incident based on the similarity of the words it contains to other incidents you've already provided as examples.

Once the Prediction API is trained, one suggested label will be applied to the incident for each label group. These labels will appear with a gray background next to each incident in the incident view, just like the labels applied manually, but with one other difference. Each suggested label will have both a check-mark and an x-mark, which users can use to indicate the quality of the suggestion. When the check-mark is clicked on a suggested label, it will feed back into the training data, further reinforcing the quality of the model.

# Training the models #

Au-to-do periodically updates the models you have configured by using a cron that runs every 15 minutes. Once the cron has been envoked, the model will update automatically, using any additional training data that you have provided.