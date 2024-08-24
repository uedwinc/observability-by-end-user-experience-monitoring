# End User Experience Monitoring on AWS

We build and run applications to serve the users, and it is always good to understand how our applications are performing from the user’s perspective, not just from the server’s standpoint. This is where end user experience monitoring comes into play.

End user experience monitoring is also known as `digital experience monitoring (DEM)`. It offers insights into how the application performs from the user’s point of view, allowing you to understand the impact on users of the application. DEM provides the outside-in view of the application performance on how a user is experiencing the application when accessed. The outside-in view begins with establishing what looks good from the end user’s point of view. Examples include web page response times, client-side JavaScript errors, visual stability, interactivity, API latencies, and so on.

## AWS Tools for End User Experience Monitoring

1. `CloudWatch Synthetics:` The modular and lightweight canary scripts in CloudWatch Synthetics help you monitor your web applications 24x7 and quickly identify and help address any issues that arise.

2. `CloudWatch RUM:` By monitoring actual user interactions, CloudWatch RUM provides you with a comprehensive understanding of your application’s performance and usability based on actual user data.

3. `CloudWatch Evidently:` Allows you to launch new application features and validate your web application decisions by running online experiments. With this feature, you can safely test new ideas and configurations to determine their impact on application performance and user experience and measure business outcomes. This will help you make data-driven decisions for any new application releases.

## Instructions

The `/journals` directory contains

- [ ] [Synthetic monitoring/CloudWatch Synthetics canaries](journals/cloudwatch-synthetic-canaries.md) for round-the-clock web monitoring