# Dyntrace configuration as code Example

To run this example download monaco (asumes this is running on linux)

```
curl -L https://github.com/Dynatrace/dynatrace-configuration-as-code/releases/latest/download/monaco-linux-amd64 -o monaco-linux-amd64
```

Then rename monaco and add permissions

```
mv monaco-linux-amd64 /usr/local/bin/monaco
chmod +rx /usr/local/bin/monaco
```

You can add it to your .bashrc file to have it always accesible.

## Downloading configurations

For this quick demo we will create a synthetic monitor + dashboard in Dynatrace manually and then we will modify the synthetic and apply it to a new environment using Dynatrace Configuration as Code.

### Steps:

1. To learn how to create a synthetic monitor [check the official docs](https://docs.dynatrace.com/docs/observe/digital-experience/synthetic-monitoring/browser-monitors/create-a-single-url-browser-monitor)

2. To download the configuration we will need a token with the scope to download dashboards and synthetics. For more details on token and scopes required [check this link](https://docs.dynatrace.com/docs/deliver/configuration-as-code/monaco/reference/supported-configuration)

3. Next, we will export the token generated into our console

```
export API_TOKEN_DT=something_secret_here
export CLIENT_ID_DT=something_secret_here
export CLIENT_SECRET_DT=something_secret_here
```

4. Then we will execute the direct download command. This will grab all the configurations for the tenant.

I have uploaded the 2 synthetics and the dashboard that I downloaded from my tenant as an example.

```
cd ./download
monaco download --url https://mcy56832.apps.dynatrace.com --token API_TOKEN_DT --oauth-client-id CLIENT_ID_DT --oauth-client-secret CLIENT_SECRET_DT
```

5. In the deploy folder copy the manifest.yaml definition from the download folder and create a folder called `demo1`. this will contain the configurations we will apply to the new tenant. For this use case we will copy one of the synthetic monitor configs in the `demo1` folder.

```
cd ..
cp download/manifest.yaml deploy/manifest.yaml
```

Note: I have changed the reference to `project` in the manifest.yaml in the deploy folder into `demo1`.

6. To deploy the config to the new tenant, change the URL value in the `manifest.yaml` create a new set of tokens to target the new tenant and export them as in step 3.
   The run the following command.

```
cd ./deploy
monaco  deploy --dry-run manifest.yaml
```

You should get something like this

```
2025-04-03T20:36:30Z    info    Monaco version 2.22.1
2025-04-03T20:36:30Z    info    Loading manifest "monaco-examples/deploy/manifest.yaml". Restrictions: groups=[], environments=[]
2025-04-03T20:36:30Z    info    Projects to be deployed (1):
2025-04-03T20:36:30Z    info      - demo1
2025-04-03T20:36:30Z    info    Environments to deploy to (1):
2025-04-03T20:36:30Z    info      - project
2025-04-03T20:36:30Z    info    Deploying configurations to environment "project"...
2025-04-03T20:36:30Z    info    Deploying 1 independent configuration sets in parallel...
2025-04-03T20:36:30Z    info    [coord=demo1:synthetic-location:SYNTHETIC_LOCATION-CCF8A71E8E5D6EFD][gid=0]     Deploying config
2025-04-03T20:36:30Z    info    [coord=demo1:synthetic-location:SYNTHETIC_LOCATION-CCF8A71E8E5D6EFD][gid=0]     Deployment successful
2025-04-03T20:36:30Z    info    [coord=demo1:synthetic-monitor:SYNTHETIC_TEST-7FA55FC6FD2F3CAB][gid=0]  Deploying config
2025-04-03T20:36:30Z    info    [coord=demo1:synthetic-monitor:SYNTHETIC_TEST-7FA55FC6FD2F3CAB][gid=0]  Deployment successful
2025-04-03T20:36:30Z    info    Deployment successful for environment "project"
2025-04-03T20:36:30Z    info    Validation finished without errors
```
