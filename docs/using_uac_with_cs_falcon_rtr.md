# Using UAC with CrowdStrike Falcon Real Time Response

With CrowdStrike Falcon Real Time Response (RTR), analysts can remotely access and interact with endpoints in real-time, gaining instant visibility into the system and collecting valuable forensic data by deploying files and running custom scripts.

In the instructions below the UAC package (i.e. uac-3.0.0.tar.gz) was stored in the CrowdStrike cloud. This way it can be deployed even if an endpoint is isolated via Network Containment feature.

1\. Start by creating a temporary working directory on the live endpoint, then change the working directory to it.
    
```shell
/> mkdir /tmp/uac
/> cd /tmp/uac
```

2\. Deploy and uncompress the UAC package.

```shell
/tmp/uac> put "uac-3.0.0.tar.gz"
/tmp/uac> runscript -Raw=```tar -zxf uac-3.0.0.tar.gz``` -Timeout=60
```

3\. Change the working directory and run the collection. Note that CrowdStrike Falcon RTR session times out after 10 minutes. Make sure to keep the Falcon RTR session active.

```shell
/tmp/uac> cd uac-3.0.0
/tmp/uac/uac-3.0.0> runscript -Raw=```./uac -p ir_triage /tmp/uac``` -Timeout=9999
```

4\. Upload the output and log files to the CrowdStrike cloud using the ```get``` command.

```shell
/tmp/uac/uac-3.0.0> get "/tmp/uac/uac-hostname-os-20240707120101.tar.gz"
/tmp/uac/uac-3.0.0> get "/tmp/uac/uac-hostname-os-20240707120101.log"
```

> Note: The maximum file size for ```get``` is 4 GB. When needed, split the files using the ```split``` command.