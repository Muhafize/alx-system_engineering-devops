Postmortem: Project 19 Outage

Summary
During Project 19 of the ALX System Engineering & DevOps program, an isolated Ubuntu 14.04 container running an Apache web server experienced an outage at 18:00 GMT in Ghana. Several GET requests to the server resulted in a 500 Internal Server Error. The expected response was an HTML file for a simple Holberton WordPress site.


Incident Discovery
The issue was discovered by Amoaful at 18:20 GMT upon opening the project. Amoaful immediately started debugging.

Debugging Process
Checking Running Processes

Command Used: ps aux
Observation: Both root and www-data processes of Apache2 were running properly.
Examining Apache Configuration

Checked Directory: /etc/apache2/sites-available/
Content Serving Directory: /var/www/html/
Using strace on Apache Processes

First Attempt: Strace on Apache root PID
Result: No useful information.
Second Attempt: Strace on www-data PID
Error Discovered: -1 ENOENT (No such file or directory) when accessing /var/www/html/wp-includes/class-wp-locale.phpp.
Identifying the Erroneous File Extension

Tool Used: Vim pattern matching
File Examined: /var/www/html/wp-settings.php
Issue Found: On line 137, the code require_once( ABSPATH . WPINC . '/class-wp-locale.php' ); contained an erroneous .phpp extension.
Fixing the Bug

Action Taken: Deleted the trailing 'p' from .phpp.
Verification: Executed another curl command, which returned a 200 OK response.
Automation
To prevent this issue in the future, Amoaful wrote a Puppet manifest to automate the detection and correction of similar errors.

Root Cause
The error in the wp-settings.php file of the WordPress application caused a failure when attempting to load class-wp-locale.phpp. The correct file name should have been class-wp-locale.php.

Prevention Measures
Testing:
Implement comprehensive testing during the development stages to catch errors early.
Monitoring:
Utilize uptime-monitoring services like UptimeRobot to alert the team of any outages.
Conclusion
This incident underscored the importance of thorough testing and effective monitoring in the development process. By implementing these measures, future issues can be detected and resolved more efficiently, ensuring a more stable and reliable web service.
