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

4. When you navigate to `Script editor`, you will notice that the script is generated automatically and populated with the parameters based on your selected options. 

  For example, the URL is included in the script, and the `Screenshots` option is enabled. You can select your preferred `Runtime version` from the drop-down list, with options for either `Puppeteer` or `Selenium Python` scripts, depending on the specific capabilities available with your organization. 

  This streamlined process makes it easy to customize your script and run it efficiently, without needing to manually write the code from scratch:

![script-editor](/images/script-editor.png)

5. You can easily schedule your canaries to run at specific intervals, either continuously or just once. You can choose a preset interval or customize your schedule using a CRON expression. For this exercise, we set the interval to every 5 minutes. 

  This flexibility in scheduling allows you to tailor your canary runs to your specific needs and also control costs.

![schedule-canaries](/images/schedule-canaries.png)

6. The output data from the execution of Synthetic canaries is saved in an S3 bucket, and we can adjust the retention settings for this data to manage storage costs. 

  Select the `Failure data retention` and `Success data retention` intervals. Understand that this retention setting affects the S3 storage cost. `S3 location` is auto-populated if it is the first time, otherwise, select a bucket to store the canary artifacts such as screenshots, HAR (short for `HTTP Archive`) information, and so on:

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

### Now, let’s explore the results of the Synthetics canaries created.

1. You can verify the availability of the website, the last state, and any issues as a `Summary` view:

![canary-metrics](/images/canary-metrics.png)

2. When you navigate to the `Availability` tab and see the tabs at the bottom-right corner of the Synthetic canaries created, you can verify `Screenshots`, `Logs`, `HAR File`, and `Traces`. 

  Additionally, HAR tells you where the greatest amount of time is spent in loading the website, which will be further useful to understand where the issues are and fine-tuning the page load times:

![synthetics-results](/images/synthetics-results.png)

The output of CloudWatch Synthetics consists primarily of **metrics, events, logs, and traces (MELT)**, which are stored in CloudWatch primary services:

- **CloudWatch Metrics:** CloudWatch Synthetics provides a range of metrics including `2xx`, `4xx`, `5xx`, `duration`, `failed request count`, and `success percent`. These metrics are available as a custom CloudWatch metric namespace, which can be used to create custom dashboards, set alarms, and detect anomalies. 

  You can browse the metrics in two ways: by navigating to the `Synthetics canaries` section of CloudWatch and selecting your canary to see its `Monitoring` tab, as shown in the figure below, or by viewing `All metrics` in the CloudWatch console.

![synthetic-metrics](/images/synthetic-metrics.png)

- **X-Ray Traces:** AWS X-Ray can record the details of a trace, offering a complete perspective of the synthetic transaction and enabling the results to be viewed through Service Lens. So far, X-Ray tracing is only available for Node.js-based Puppeteer scripts. You can visualize the traces generated by CloudWatch Synthetics, as shown below:

![synthetic-traces](/images/synthetic-traces.png)

- **CloudWatch Logs:** Logs generated during the execution of the synthetic canary are stored in CloudWatch logs, offering a comprehensive view of activities performed. You can view this by clicking on the `Logs` section, under the `Availability` tab.

- **CloudWatch Events:** Through CloudWatch Events, you can understand the status of the Synthetics canaries, including changes in status, failure, and success. You can configure notifications for these based on EventBridge rules. 

  Click to create an EventBridge rule and select any of the Synthetic canary event:

![synthetic-events](/images/synthetic-events.png)

## 2. API canary

Let’s set up a synthetic canary for the REST API endpoint that we deployed as a part of [_Observability for Serverless Applications on AWS_](https://github.com/uedwinc/observability-for-serverless-apps-on-aws/blob/main/journals/tracing-application.md#deploying-a-new-sample-application), and understand how to use a canary to understand issues with the API endpoint.

1. Navigate to `CloudWatch` | `Synthetics Canaries` | `Create canary` | `Use a blueprint` | `API canary` and enter the name `apicanary_restapi`

2. If the API is a third-party service such as an external API (for example, Boomi or Apigee), you could directly select `Add HTTP request` and provide the URL along with the methods to invoke. As we are using an Amazon API Gateway API, select the `I’m using an Amazon API Gateway API` checkbox, then select `Choose API` | `Choose API and stage from API Gateway` | `serverless-app2`, and set the `Stage` option to `Prod`:

![selecting-api-gateway](/images/selecting-api-gateway.png)

3. Application or endpoint URLs are available to select automatically based on the Amazon API Gateway URL. Next, click on `Add HTTP request`:

![http-request](/images/http-request.png)

4. Select `/items` for `Resource`, set `Method` to `GET`, and click on `Save`:

![get-method](/images/get-method.png)

5. Optionally, if you are looking to capture the headers and response body, you can select the same in `Reporting configuration` before clicking `Save`. This will be especially useful when you would like to analyze the response details of the API; click `Save`:

![optional-response](/images/optional-response.png)

Now when you navigate to `Script editor`, the Synthetics canaries script has been auto-generated based on the selected options:

![canary-script](/images/canary-script.png)

6. Leave the remaining options as default and click `Create canary`.

  See the `restapi-canary` created below:

![restapi-canary](/images/restapi-canary.png)

7. If you navigate to the `HTTP steps` section, under the `Availability` tab of the canary, you can find details of the execution along with other options to investigate like `Logs` and `Traces`.

![http-steps](/images/http-steps.png)

> The implementation of broken link monitoring and visual monitoring could follow a similar approach to heartbeat monitoring. 

If the default blueprints are inadequate for the task, we can explore the usage of Canary Recorder.

## 3. Canary Recorder

CloudWatch Synthetics Recorder is a useful tool that can assist you in recording the steps performed on a website. This tool is available as a Chrome extension and can be easily installed on your browser:

![chrome-webstore-canary-recorder](/images/chrome-webstore-canary-recorder.png)

Once installed, you can simply click on the Start recording button and the recorder will begin capturing all the actions you perform on the website:

![canary-recording](/images/canary-recording.png)

The `Synthetics Recorder` is helpful when you need to generate Synthetics canary scripts quickly. By recording the steps you take on the website; the recorder auto-generates the Synthetics canary script for you. This feature saves time and effort, especially if you are not familiar with scripting.

Here's a sample canary script generated:

![canary-record](/images/canary-record.png)

Using the Synthetics Recorder can be an effective solution if the available default blueprints do not meet your requirements. With the recorder, you have the flexibility to customize the steps and actions you want to capture, enabling you to create more tailored and effective monitoring scripts for your website.