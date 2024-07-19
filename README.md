index="log-55301-nonprod-c" laas_env=dev laas_file="/var/tmp/gatewayapi_dev/transaction.log" "Starting request for the user"
| rex field=_raw "\- (?<url_path>/[^\s]+)\s+\-"
| rex field=_raw "user:\s*(?<username>[^\s]+)"
| rex field=_raw "(?<http_method>GET|POST|PUT|DELETE|HEAD|OPTIONS|PATCH)"
| search url_path=*
| table _time, username, http_method, url_path
