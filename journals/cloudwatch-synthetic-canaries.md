# CloudWatch Synthetics

CloudWatch Synthetics allows you to proactively monitor the website and API endpoints every minute, 24x7, using modular canary scripts. This service helps you receive instant alerts when your application does not behave as expected, allowing you to quickly identify and address issues before they impact your users.

CloudWatch Synthetics will act like a user and perform a health check on a defined schedule, even when there is no user traffic, helping you to catch issues proactively and prevent user-facing problems.

CloudWatch Synthetics helps you gain a comprehensive understanding of the availability and performance of your web applications and ensures you meet your _Service-Level Agreement (SLA)_ requirements.

## Use cases of CloudWatch Synthetics monitoring

1. Availability and latency monitoring: To monitor URL availability and understand the latency in loading the website by the users

2. Easy web testing: Quick testing of the website experience without any user traffic

3. SLAs/SLOs made easy: To measure the availability of the website against the SLA

4. Visual regression monitoring: To understand issues in the visual components of the website

5. Proactive alerting: To alert for any issues proactively before affecting the end users

6. Anomaly detection: To understand any anomalies in the website performance before it affects the end users

## Understanding CloudWatch Synthetics canaries

Upon logging in to the CloudWatch Synthetics console, you will encounter three distinct options for creating canaries: `using a blueprint`, `using the inline code editor`, or `importing scripts from S3`.

1. **Blueprints**: Predefined blueprints are available to generate canary scripts automatically based on configured options for specific use cases. Available blueprints include:

  - **Heartbeat monitoring**: Heartbeat monitoring allows you to check the availability of a website by measuring its basic page-load of a single URL. It’s useful for measuring the Service Level Objective (SLO) of a website’s availability.

  - **API canary monitoring**: This blueprint is designed to test REST APIs and their availability. By invoking the methods in the API and verifying the response time and output, you can ensure that APIs are functioning properly.

  - **Broken link checker**: When updating a website with multiple URL references, it’s common for some of the referenced URLs to become unavailable. This blueprint provides a solution to this issue by checking all the URLs on the listed website based on the blueprint.

  - **Canary Recorder**: The AWS Canary Recorder is a Chrome browser extension that allows you to record clicks and actions on a website and auto-generate the script based on the actions performed.

  - **GUI Workflow Builder**: The GUI workflow builder is ideal for creating secure monitoring of user workflows. For instance, if you want canaries to mimic usernames and passwords but do not want to store them in the script, this tool allows you to pass them as variables and leverage AWS Secrets Manager to store the credentials.

  - **Visual monitoring**: This blueprint is useful for understanding the visual differences in your web interfaces and comparing any changes to the baseline. It provides a way to monitor the user experience and quickly identify any issues with the design or layout of the website.

2. **Inline Code Editor**: The inline code editor is suitable for creating custom scripts or importing existing scripts from your own local system.

3. **Import Scripts from S3**: You can also upload your existing Puppeteer or Selenium scripts into S3 storage and import them into the Synthetic canaries.

# Configuring CloudWatch Synthetics canaries

## 1. Heartbeat monitoring

Let’s take a scenario where you are looking to configure the availability monitoring for a public-facing website such as `aws.amazon.com`. You can use the heartbeat blueprint to configure and measure the availability of the website.

1. Navigate to `AWS Console` | `CloudWatch` | `Application signals` | `Synthetics Canaries` | `Create canary`:

![creating-canaries](/images/creating-canaries.png)

2. Select `Use a blueprint` | `Heartbeat monitoring`:

![heartbeat-monitoring](/images/heartbeat-monitoring.png)

3. Provide `awswebsite` as the name and https://aws.amazon.com as the application or endpoint URL. In one synthetic canary, you can add up to five different endpoints. If you would like to take a screenshot of the website during each canary run, you can select the `Take screenshots` checkbox:

![canary-endpoint](/images/canary-endpoint.png)

4. When you navigate to `Script editor`, you will notice that the script is generated automatically and populated with the parameters based on your selected options. For example, the URL is included in the script, and the `Screenshots` option is enabled. You can select your preferred `Runtime version` from the drop-down list, with options for either `Puppeteer` or `Selenium Python` scripts, depending on the specific capabilities available with your organization. This streamlined process makes it easy to customize your script and run it efficiently, without needing to manually write the code from scratch:

![script-editor](/images/script-editor.png)

5. You can easily schedule your canaries to run at specific intervals, either continuously or just once. You can choose a preset interval or customize your schedule using a CRON expression. For this exercise, we set the interval to every 5 minutes. This flexibility in scheduling allows you to tailor your canary runs to your specific needs and also control costs.

![schedule-canaries](/images/schedule-canaries.png)

6. Select the `Failure data retention` and `Success data retention` intervals. Understand that this retention setting affects the S3 storage cost. `S3 location` is auto-populated if it is the first time, otherwise, select a bucket to store the canary artifacts such as screenshots, HAR (short for `HTTP Archive`) information, and so on:

![storage-settings](/images/storage-settings.png)

Synthetics canaries will create a new AWS IAM role by default to execute the conceptual flow:

![access-permissions](/images/access-permissions.png)

7. You can create CloudWatch alarms by clicking on the `Add new alarm` button based on `SuccessPercentage` and `Duration`. You can also select a `Simple Notification Services (SNS)` service to notify you of any additional actions you would like to take based on the alarms. It can create an incident in the ITSM system or action of remediation if required:

![canary-alarm](/images/canary-alarm.png)

8. If your website is hosted within AWS VPC, you have the option to select the VPC as the location for executing the canary. However, in our current example, the website URL is hosted outside of our AWS account, hence we have selected `No VPC` in `VPC settings`:

![vpc-settings](/images/vpc-settings.png)

9. You can enable `Active tracing` (as we have selected Node.js script) to track how the Synthetics canary is executing, and click on the `Create canary` button:

![xray-canary](/images/xray-canary.png)

It will take a minute or two to create the Synthetics canary.

![synthetic-overview](/images/synthetic-overview.png)

Now, let’s explore the results of the Synthetics canaries created.