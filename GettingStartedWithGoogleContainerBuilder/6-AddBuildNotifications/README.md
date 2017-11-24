<strong>Note:</strong> This is the sixth part in a series of posts <a href="https://blogs.microsoft.co.il/leonj/2017/11/19/getting-started-with-google-container-builder">Getting Started with Google Container Builder</a>

<hr />
&nbsp;

<h1>Add build notifications using Cloud Functions</h1>

In this part we will use Cloud functions to configure notifications for our builds (failed builds). In this example we will send the email notifications through <a href="https://www.mailgun.com/">Mailgun</a>.

&nbsp;

• Create a Google Storage bucket to store the function source code

[code language="text" gutter="false"]
gsutil mb gs://[BUCKET_NAME]
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/6-AddBuildNotifications/Images/6-1.png?raw=true" alt="Image" />

&nbsp;

• Create a folder to store the function files

[code language="text" gutter="false"]
mkdir ~/gcb_email
cd ~/gcb_email
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/6-AddBuildNotifications/Images/6-2.png?raw=true" alt="Image" />

&nbsp;

• Create the functions files below:

[code language="text" title="config.json"]
  {
     "MAILGUN_API_KEY":"<your-mailgun-api-key>",
     "MAILGUN_DOMAIN":"<your-mailgun-domain>",
     "MAILGUN_FROM":"<email-from-address>",
     "MAILGUN_TO":"<email-to-address>"
  }
[/code]

[code language="text" title="package.json"]
  {
    "name": "google-container-email",
    "version": "0.0.1",
    "description": "Email integration for Google Cloud Container Builder, using Google Cloud Functions",
    "main": "index.js",
    "dependencies": {
      "humanize-duration": "3.10.0",
      "mailgun-js": "~0.11.2"
    },
    "devDependencies": {
      "async": "^2.1.5",
      "mocha": "3.2.0",
      "should": "11.2.1"
    }
  }
[/code]

[code language="javascript" title="index.js" htmlscript="true"]
const Mailgun = require('mailgun-js');
const humanizeDuration = require('humanize-duration');
const config = require('./config.json');

module.exports.mailgun = new Mailgun({
  apiKey: config.MAILGUN_API_KEY,
  domain: config.MAILGUN_DOMAIN,
});

// subscribe is the main function called by Cloud Functions.
module.exports.subscribe = (event, callback) => {
  const build = module.exports.eventToBuild(event.data.data);

  // Skip if the current status is not in the status list.
  const status = ['FAILURE', 'INTERNAL_ERROR', 'TIMEOUT'];
  if (status.indexOf(build.status) === -1) {
    return callback();
  }

  // Send email.
  const message = module.exports.createEmail(build);
  module.exports.mailgun.messages().send(message, callback);
};
// eventToBuild transforms pubsub event message to a build object.
module.exports.eventToBuild = (data) => {
  return JSON.parse(new Buffer(data, 'base64').toString());
}
// createEmail create an email message from a build object.
module.exports.createEmail = (build) => {
  let duration = humanizeDuration(new Date(build.finishTime) - new Date(build.startTime));
  let content = `Build ${build.id} finished with status ${build.status}` + `\n` + `${build.logUrl}`;
  let message = {
    from: config.MAILGUN_FROM,
    to: config.MAILGUN_TO,
    subject: `Build ${build.id} finished`,
    text: content
  };
  return message
}
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/6-AddBuildNotifications/Images/6-3.png?raw=true" alt="Image" />

&nbsp;

• Upload the files to the bucket as a cloud function

[code language="text" gutter="false"]
gcloud beta functions deploy subscribe --stage-bucket gs://[BUCKET_NAME] --trigger-topic cloud-builds
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/6-AddBuildNotifications/Images/6-4.png?raw=true" alt="Image" />

&nbsp;

• Edit the <strong><em>primes.js</em></strong> test to cause the build to fail (change the number used for the test from 11 to 12)

[code language="javascript" highlight="5"]
var sut = require('../primes.js');
describe("when evaluating if a given number is a prime", function(){
  describe("when the number is a prime", function(){
    it("should return 'true'", function(){
      expect(sut.isPrime(12)).toBe(true);
    });
  });
});
[/code]

&nbsp;

• Commit and push the changes, the build will failed and you will receive an email notification

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/6-AddBuildNotifications/Images/6-5.png?raw=true" alt="Image" />

&nbsp;
&nbsp;

Congratulations, you have already created your own CI/CD process within GCP using Google Container Builder including the creation of a custom builder and automatic email notifications.
For more information about Google Container Builder visit the <a href="https://cloud.google.com/container-builder/docs/">Official Documentation</a>

&nbsp;
