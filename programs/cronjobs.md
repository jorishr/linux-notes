# Cronjobs

`sudo crontab -e`

On one single lines you write: 

MINUTE 0-59, 

HOURS 0-23, 

DAY OTM 1-31, 

MONTH 1-12, 

DAY OTW 0-6, 

COMMAND TO RUN

To have it run every day: use *

Every day at 12h00 run the ls command: 
	
`0 12 * * * ls > output.txt` 

You can use online tools such cron guru to help you configure cronjobs.