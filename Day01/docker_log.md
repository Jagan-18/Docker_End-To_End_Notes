---
**Interviewer:** "How do you check Docker container logs, particularly the `json.log` files?"

To check Docker container logs, especially the `json.log` files, I typically follow these steps:

1. **Use `docker logs`:**
   The easiest way is to run `docker logs <container-id>`. This shows the logs of the container. If I want to see the last few lines, I can use `--tail`, and if I want to follow the logs in real-time, I use `--follow`.

2. **Directly Access the Log Files:**
   Docker stores the log files under `/var/lib/docker/containers/<container-id>/`. The log file is named `<container-id>-json.log`. I can use `tail` or `cat` to directly view these logs. For example:
   ```bash
   tail -f /var/lib/docker/containers/<container-id>/<container-id>-json.log
   ```

3. **Parsing Logs with `jq`:**
   Since the logs are in JSON format, I can use `jq` to filter and extract specific data from them. For example:
   ```bash
   cat /var/lib/docker/containers/<container-id>/<container-id>-json.log | jq '.log'
   ```

4. **Log Rotation:**
   Logs can grow large, so it’s important to manage log size. I would use Docker’s `--log-opt` flags to limit the log size and number of files, for example:
   ```bash
   docker run --log-driver=json-file --log-opt max-size=10m --log-opt max-file=3 <image-name>
   ```

5. **Other Logging Drivers:**
   Depending on the environment, I may also configure Docker to use different logging drivers like `syslog` or `journald`.

---
