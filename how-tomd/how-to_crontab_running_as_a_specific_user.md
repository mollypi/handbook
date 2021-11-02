# How-To crontab running as a specific user

Only /etc/crontab and the files in /etc/cron.d/ have a username field. In that file you can do this:

```text
1 1 * * * username /path/to/your/script.sh
```

From root's crontab sudo crontab -e you can use:

```text
1 1 * * * su username -c "/path/to/your/script.sh"
```

Or you can use the user's actual crontab like this:

```text
sudo crontab -u username -e
```

The second column in any crontab file is for the hour that you want the job to run at. Did you mean the sixth field?

