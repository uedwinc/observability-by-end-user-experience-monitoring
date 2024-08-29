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

![rum-overview](/images/rum-overview.png)

`Apdex` is an open standard solution used to measure user satisfaction with the response time of web applications and services. It is a ratio of total requests made over a period to the value of the number of satisfied and tolerable requests. The Apdex score measures customer satisfaction. It will be in the range of 0 to 1, with 0 being the worst and 1 being the best. If you look at the RUM output, in our case it is 1.

16. Navigate to `CloudWatch` | `RUM` | `S3RUM-App` and verify the statistics provided by CloudWatch RUM:

![rum-performance-output](/images/rum-performance-output.png)

In the figure above, you can see the overall number of page loads, load time, and any errors caused while browsing the application:

- **Page loads**: Page loads provides the number of page loads over the period of time selected.
- **Errors**: Here, you can find JavaScript and HTTP errors. You can also find the number of sessions and the number of sessions with errors.

To comprehend the results produced by CloudWatch RUM, it’s important to note that the tool’s performance output includes the term web vitals, which is a concept introduced by Google (https://support.google.com/webmasters/answer/9205520) to offer crucial data points for evaluating the user experience.

Refer to the figure below to understand the user’s experience details:

![rum-web-vitals](/images/rum-web-vitals.png)

`Web vitals` is an initiative by Google that provides unified guidance for quality signals that are essential to delivering a great user experience on the web. It is a way to simplify the understanding of the performance landscape to focus on the metrics that matter the most.

Core web vitals include *Largest Contentful Paint (LCP)*, *First Input Delay (FID)*, and *Cumulative Layout Shift (CLS)*, and we could interpret the results as follows along with some possible remediations:

- The LCP metric reports the render time of the largest image or text block visible within the viewport, relative to when the page first started loading. LCP considers the largest image, video, and background images of the website, and block-level elements containing text. LCP issues and solutions include improving server performance, optimizing JS/CSS bundles, compressing/caching images, pre-fetch data, using *server-side rendering (SSR)* if possible, and using edge solutions for your data.

- The FID metric measures the time from when a user first interacts with a page (i.e., when they click a link, tap on a button, or use a custom JavaScript-powered control) to the time when the browser can begin processing event handlers in response to that interaction. The ideal score for this web vital is 100 ms or less. FID solutions include avoiding large blocking times (long tasks), reviewing your dependencies, removing unused polyfills, caching your assets, and lazy loading of third-party assets.

- CLS measures visual stability – the percentage of content shifting around your website, caused by images loading slowly, and new pieces of content pushing items. CLS solutions include adding size attributes to images/videos, reserving space for slow-loading content (skeleton), allowing your text to be visible while your fonts are loading, and preloading your fonts.

When you look at *Step and duration* in the figure below, which is part of the CloudWatch RUM *Summary* tab, you can find the duration of the steps.

![steps-and-duration-of-ui](/images/steps-and-duration-of-ui.png)

> One important metric to look into is *Time to first byte (TTFB)*. TTFB is a metric for determining the responsiveness of a web server. It measures the amount of time between creating a connection to the server and downloading the contents of a web page.

If you are looking to understand the user performance by browser, navigate to the `Browsers & Devices` tab and verify the detail metrics by browser and by device:

![browser-and-device-experience](/images/browser-and-device-experience.png)

You can also analyze the user journey by navigating the `User Journey` tab.