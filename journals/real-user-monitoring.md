# CloudWatch RUM

CloudWatch RUM is a monitoring technology that captures and records all the user interactions with your website or application. With RUM, you can gain valuable insights into your application’s frontend performance from the perspective of real users. It helps you correlate the client side and server side and provides an end-to-end view of the application performance.

CloudWatch RUM will help you identify performance issues of the website, the Application Performance Index (Apdex) by country, and errors on the website. It also helps you understand the browsers/devices accessing your website and analyze the sessions and traffic on the website.

Besides these benefits, CloudWatch RUM offers all the benefits of Amazon CloudWatch, such as metrics, alarms, anomaly detection, and so on. It provides flexibility in configuration, allows you to customize session samples and capture response times of specific pages, and also add X-Ray tracing.

To enable CloudWatch RUM monitoring for your web application, the first step is to include a small script in the application’s frontend code. Once the web application loads in a user’s browser, the script retrieves the credentials from the Cognito identity pool, which is configured as part of CloudWatch RUM.

With the credentials to post the data to CloudWatch RUM, the CloudWatch RUM script collects client- side performance telemetry, navigation events, and any JavaScript/HTTP errors that occur during user journeys on your website. This data provides valuable insights into how users interact with your application and can identify performance issues and other areas for improvement.

The following are the default metrics provided by CloudWatch RUM:

- **Users**: Active user count, device, screen resolution, browser, and location
- **User interaction**: Sessions, entry point, exit point, user journey, and top viewed pages
- **Site behavior**: Page load times, latencies, and web vitals

The logs stored can generate insights by leveraging the CloudWatch Logs Insights service.

# Setting up CloudWatch RUM for an S3 static website

We will deploy a static website on Amazon S3 and configure CloudWatch RUM to monitor user interactions and provide insights into the performance of the *Single Page Application (SPA)* running on the S3. By setting up CloudWatch RUM, we can gain a deeper understanding of how users interact with our SPA, identify potential performance issues, and take steps to optimize the user experience.

As a part of the first step, let’s deploy the static website and upload a simple HTML page, *index.html*:

1. Let’s deploy the following [CloudFormation template](../creates3staticwebsite.yaml) as a quick start, which will deploy an S3 bucket along with making it public using the ACLs. Name the stack `S3WebappRUM`. I do not recommend this configuration for production deployment, as the S3 bucket is made public

2. Navigate to the output of the CloudFormation template deployment to confirm the creation of the S3 website URL. This URL will serve as our endpoint for the website and will be used to verify and observe the website’s performance using CloudWatch RUM:

![s3-spa](/images/s3-spa.png)

3. Create an [*index.html*](../index.html) file. This is a simple plain HTML file with a test message.

> *This has been purposefully kept for download and upload to the S3 bucket as RUM requires the addition of a script to capture the real user data.*

4. Browse to the S3 website URL that was noted down in the `Outputs` tab during Step 2. By visiting this WebSite URL, we can verify that the S3 static web page is available to users. This step is essential to ensure that the website is properly deployed and functioning as intended before proceeding with the CloudWatch RUM setup.

![verify-site-url](/images/verify-site-url.png)

5. The CloudWatch RUM setup is divided into three steps:

  - Add the app you want to monitor
  - Copy and paste the JavaScript code into the header of the application
  - Monitor and troubleshoot the issues

![rum-setup](/images/rum-setup.png)

6. To set up CloudWatch RUM, there are two options available: configuring it through the AWS Console or using CloudFormation. For this exercise, we will utilize the AWS Console to set up CloudWatch RUM.

  Navigate to `AWS Console` | `CloudWatch` | `RUM` | `Add app monitor`:

![add-app-monitor](/images/add-app-monitor.png)

7. Provide `S3RUM-App` as the app monitor name and the website URL as noted in the Step 2 output `S3BucketSecureURL` without `http(s)`. We are selecting to capture performance telemetry, JavaScript errors, and HTTP errors, though we do not receive any JavaScript errors in this exercise.

![setup-app-monitor](/images/setup-app-monitor.png)
![setup-app-monitor-2](/images/setup-app-monitor-2.png)

8. Select to allow cookies by selecting `Check this option to allow the CloudWatch RUM Web Client to set cookies` and type `100%` in `Session samples`, which will allow you to sample 100% of the sessions. Finally, select `Check this option to store your application telemetry data in your CloudWatch Logs account` to log the request to CloudWatch Logs

![cloudwatch-logging](/images/cloudwatch-logging.png)

9. Select `Create new identity pool` and configure it to capture information from `All pages`:

![cognito-pool-creation](/images/cognito-pool-creation.png)

10. Click on `Add app monitor`.

11. The app monitor will generate a script that needs to be added to the SPA. This process may take a few minutes. It’s worth noting that if the web application has multiple pages with disjoint domain names/URLs, the RUM monitor needs to be created separately for each page. This will ensure that the performance data for each page is captured accurately.

![html-script](/images/html-script.png)

12. Copy the script generated and edit *index.html* using any editor, such as Notepad on Windows, TextEdit on macOS, or Vscode and add the script to the header of the HTML page after the HTML tag <head>:

![add-script](/images/add-script.png)

13. Upload the changed *index.html* file to the S3 bucket noted in Step 2 by replacing the old index.html file.

14. To verify that the S3 static website is posting the user experience data to the CloudWatch RUM, you can browse the S3 static website URL and check the CloudWatch console for the RUM metrics. This will confirm that the RUM monitor works properly and is collecting data from the website.

15. Navigate to `CloudWatch` | `RUM` and verify the summary of the RUM results:

