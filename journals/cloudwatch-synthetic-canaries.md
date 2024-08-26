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

