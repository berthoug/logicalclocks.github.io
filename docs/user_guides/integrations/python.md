# Python Environments (Local or Kubeflow)

Connecting to the Feature Store from any Python environment requires setting up a Feature Store API key and installing the library. This guide explains step by step how to connect to the Feature Store from any Python environment such as your local environment or KubeFlow.

## Generate an API key

For instructions on how to generate an API key follow this [user guide](../projects/api_key/create_api_key.md). For the Python client to work correctly make sure you add the following scopes to your API key:

  1. featurestore
  2. project
  3. job
  4. kafka

## Install **HSFS**

To be able to access the Hopsworks Feature Store, the `HSFS` Python library needs to be installed in the environment from which you want to connect to the Feature Store. You can install the library through pip. We recommend using a Python environment manager such as *virtualenv* or *conda*.

```
pip install hsfs[python]~=[HOPSWORKS_VERSION]
```

!!! attention "Hive Dependencies"

    By default, `HSFS` assumes Spark/EMR is used as execution engine and therefore Hive dependencies are not installed. Hence, on a local Python evnironment, if you are planning to use a regular Python Kernel **without Spark/EMR**, make sure to install the **"python"** extra dependencies (`hsfs[python]`).

!!! attention "Matching Hopsworks version"
    The **major version of `HSFS`** needs to match the **major version of Hopsworks**.


<p align="center">
    <figure>
        <img src="../../../../assets/images/guides/integrations/hopsworks-version.png" alt="HSFS version needs to match the major version of Hopsworks">
        <figcaption>You find the Hopsworks version inside any of your Project's settings tab on Hopsworks</figcaption>
    </figure>
</p>

## Connect to the Feature Store

You are now ready to connect to the Hopsworks Feature Store from your Python environment:

```python
import hsfs
conn = hsfs.connection(
    host='my_instance',                 # DNS of your Feature Store instance
    port=443,                           # Port to reach your Hopsworks instance, defaults to 443
    project='my_project',               # Name of your Hopsworks Feature Store project
    api_key_value='apikey',             # The API key to authenticate with Hopsworks
    hostname_verification=True          # Disable for self-signed certificates
)
fs = conn.get_feature_store()           # Get the project's default feature store
```

!!! note "Engine"

    `HSFS` uses either Apache Spark or Pandas on Python as an execution engine to perform queries against the feature store. The `engine` option of the connection let's you overwrite the default behaviour by setting it to `"python"` or `"spark"`. By default, `HSFS` will try to use Spark as engine if PySpark is available. So if you have PySpark installed in your local Python environment, but you have not configured Spark, you will have to set `engine='python'`. Please refer to the [Spark integration guide](spark.md) to configure your local Spark cluster to be able to connect to the Hopsworks Feature Store.

!!! info "Ports"

    If you have trouble to connect, please ensure that your Feature Store can receive incoming traffic from your Python environment on ports 443, 9083 and 9085 (443,9083,9085).

    If you deployed your Hopsworks Feature Store instance with [managed.hopsworks.ai](https://managed.hopsworks.ai), it suffices to enable [outside access of the Feature Store and Online Feature Store services](https://docs.hopsworks.ai/hopsworks-cloud/latest/services/#outside-access-to-the-feature-store).

## Next Steps

For more information about how to connect, see the [Connection](https://docs.hopsworks.ai/feature-store-api/{{{ hopsworks_version }}}/generated/api/connection_api/) API reference. Or continue with the Data Source guide to import your own data to the Feature Store.
