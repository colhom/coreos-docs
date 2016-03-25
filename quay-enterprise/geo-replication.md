# Georeplication of storage in Quay Enterprise

Georeplication allows for a single globally-distributed Quay Enterprise to serve container images from localized storage.

When setup and enabled, all pushes performed will first save their image data to the preferred storage engine for the Quay Enterprise instance. Following push, the image data will be replicated in the background to other storage engines (configurable). Pulling of images will always make use of the closest *avaliable* storage engine, ensuring availability and performance.

**NOTE: Ensure that there is a high availability storage engine (S3, GCS, RADOS, Swift) in each geographic region and that each region can access each storage engine. Local disk storage is not compatible with georeplication at this time.**

## Visit the Management Panel

Sign in to a super user account and visit `http://yourregister/superuser` to view the management panel:

<img src="img/superuser.png" class="img-center" alt="Enterprise Registry Management Panel"/>

## Enable Storage Replication

1. Click the configuration tab (<span class="fa fa-gear"></span>) and scroll down to the section entitled <strong>Registry Storage</strong>.
2. Click "Enable Storage Replication".
3. Add each of the storage engines to which data will be replicated. All storages to be used must be listed and **all regions must have access to _all_ storage engines**.
4. If global and universal replication is required, under each storage engine click "Replicate to storage engine by default". This will ensure that all images are replicated to that storage engine. To instead enable per-namespace replication, please contact support.
5. Click Save to validate.

## Run Quay Enterprise with storage preferences

1. Copy the config.yaml to all machines running Quay Enterprise
2. For each machine in each region, add a `QUAY_DISTRIBUTED_STORAGE_PREFERENCE` environment variable with the preferred storage engine for the region in which the machine is running.

    For example, for a machine running in Europe:

    ```
    docker run -d -p 443:443 -p 80:80 -v /conf/stack:/conf/stack -e QUAY_DISTRIBUTED_STORAGE_PREFERENCE=europestorage quay.io/coreos/registry:versiontag
    ```

    *NOTE: The ID specified must match the name of a storage engine*

3. Restart all Quay Enterprise containers
