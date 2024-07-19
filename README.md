
index="log-55301-nonprod-c" laas_env=dev laas_file="/var/tmp/gatewayapi_dev/transaction.log" "Starting request for the user"
| rex field=Event "\-\s+(?<url_path>/[^\s]+)\s+\-"
| table _time, url_path
