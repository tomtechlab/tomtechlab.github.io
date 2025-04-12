```bash
#!/bin/bash
#v0.1

# Duration in minutes
duration="21"
# Unit - 's' for seconds (the default), 'm' for minutes, 'h' for hours or 'd' for days
duration+="m"

# STEP1 - Execute CURL query to disable the alert

echo "Start time: $(date -u)"
echo ""
echo "Disabling the alerts."
echo ""

curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20ECRS%20SL1/disable -X POST > /dev/null 2>&1
sleep 0.1
curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20ECRS%20SL2/disable -X POST > /dev/null 2>&1
sleep 0.1
curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20ECRS%20SL3/disable -X POST > /dev/null 2>&1
sleep 0.1
curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20ECRS%20SL5/disable -X POST > /dev/null 2>&1
sleep 0.1
curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20RPTS%20SL1/disable -X POST > /dev/null 2>&1
sleep 0.1
curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20RPTS%20SL2/disable -X POST > /dev/null 2>&1
sleep 0.1
curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20RPTS%20SL3/disable -X POST > /dev/null 2>&1
sleep 0.1
curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20RPTS%20SL5/disable -X POST > /dev/null 2>&1
sleep 0.1
curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20SL1/disable -X POST > /dev/null 2>&1
sleep 0.1
curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20SL2/disable -X POST > /dev/null 2>&1
sleep 0.1
curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20SL3/disable -X POST > /dev/null 2>&1
sleep 0.1
curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20SL5/disable -X POST > /dev/null 2>&1
sleep 0.1
curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20SL5%20-%20KNET/disable -X POST > /dev/null 2>&1
sleep 0.1


# STEP2 - Wait for the scheduled (downtime) period of time

# Wait for the specified number of minutes
echo "Sleep for $duration"
sleep "$duration"
echo "$duration have passed. Continuing..."
echo ""

# STEP3 - Execute CURL query to enable the alert

echo "Enabling the alerts back."

curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20ECRS%20SL1/enable -X POST > /dev/null 2>&1
sleep 0.1
curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20ECRS%20SL2/enable -X POST > /dev/null 2>&1
sleep 0.1
curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20ECRS%20SL3/enable -X POST > /dev/null 2>&1
sleep 0.1
curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20ECRS%20SL5/enable -X POST > /dev/null 2>&1
sleep 0.1
curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20RPTS%20SL1/enable -X POST > /dev/null 2>&1
sleep 0.1
curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20RPTS%20SL2/enable -X POST > /dev/null 2>&1
sleep 0.1
curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20RPTS%20SL3/enable -X POST > /dev/null 2>&1
sleep 0.1
curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20RPTS%20SL5/enable -X POST > /dev/null 2>&1
sleep 0.1
curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20SL1/enable -X POST > /dev/null 2>&1
sleep 0.1
curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20SL2/enable -X POST > /dev/null 2>&1
sleep 0.1
curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20SL3/enable -X POST > /dev/null 2>&1
sleep 0.1
curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20SL5/enable -X POST > /dev/null 2>&1
sleep 0.1
curl -k -u $username:$password https://10.10.83.112:8089/servicesNS/nobody/search/saved/searches/Latency%20for%20LAX%20PRD%20SL5%20-%20KNET/enable -X POST > /dev/null 2>&1
sleep 0.1

# STEP4 - Finish the task
echo ""
echo "End time: $(date -u)"
```
