# Using UAC with CrowdStrike Falcon Real Time Response

CrowdStrike Falcon Real Time Response (RTR) enables analysts to remotely access and interact with endpoints in real time. This allows for immediate visibility into a system and the ability to collect valuable forensic data by deploying files and executing custom scripts.

In the instructions below, the UAC package (e.g., `uac-3.0.0.tar.gz`) is assumed to be stored in the CrowdStrike cloud. This allows deployment even if the endpoint is isolated using the Network Containment feature.

1\. Create a temporary working directory

Create a temporary directory on the live endpoint and navigate into it:

```shell
/> mkdir /tmp/uac
/> cd /tmp/uac
```

2\. Deploy and extract the UAC package

Deploy the UAC archive and extract its contents:

```shell
/tmp/uac> put "uac-3.0.0.tar.gz"
/tmp/uac> runscript -Raw=```tar -zxf uac-3.0.0.tar.gz``` -Timeout=60
```

3\. Run the collection

Change to the UAC directory and start the data collection. Note that Falcon RTR sessions timeout after 10 minutes, so you may need to interact with the session to keep it alive.

```shell
/tmp/uac> cd uac-3.0.0
/tmp/uac/uac-3.0.0> runscript -Raw=```./uac -p ir_triage /tmp/uac``` -Timeout=9999
```

4\. Upload output and log files

Use the `get` command to upload the resulting archive and log files to the CrowdStrike cloud:

```shell
/tmp/uac/uac-3.0.0> get "/tmp/uac/uac-hostname-os-20240707120101.tar.gz"
/tmp/uac/uac-3.0.0> get "/tmp/uac/uac-hostname-os-20240707120101.log"
```

> **Note:** The maximum file size supported by `get` is 4 GB. If necessary, use the `split` command to divide larger files.

## Running UAC in the background

To run UAC in the background and prevent it from being interrupted by session timeouts, use the following command. This command launches UAC using `bash -c` and appends its output to a log file:

```shell
/tmp/uac/uac-3.0.0> runscript -Raw=```bash -c "./uac -p ir_triage -v /tmp/uac &" >>/tmp/uac/uac.run.log``` -Timeout=9999
```

You can monitor the progress using:

```shell
/tmp/uac/uac-3.0.0> cat /tmp/uac/uac.run.log
```
