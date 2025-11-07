# Wazuh Manager (Ubuntu Server)
### ⚠️ Important Warning (Backup)

It is highly recommended to take a **Backup** of your **Wazuh Manager** before performing the update. It is especially crucial **to back up files containing Agent Keys and Configuration files**.

```bash
# Backup command for critical files
sudo cp -r /var/ossec/etc /root/wazuh_backup_etc_$(date +%Y%m%d)
sudo cp -r /var/ossec/var/db /root/wazuh_backup_db_$(date +%Y%m%d)
```

### How to Update Wazuh Manager (For Ubuntu/Debian)

Run the following commands one by one in the Terminal of your **Wazuh Manager (Ubuntu Server)**.

#### Step 1: Update the Wazuh Repository

Update the repository so your system can find the latest Wazuh packages.

```bash
sudo apt-get update
```

#### Step 2: Upgrade the Wazuh Manager Package

Upgrade the `wazuh-manager` package.

```bash
sudo apt-get install wazuh-manager
```

  * **`apt-get`** will automatically check dependencies and upgrade them if necessary.
  * During the update, a prompt may appear asking how to handle the **Configuration File (e.g., `ossec.conf`)**. You should always choose **N (keep the currently installed version)**. This ensures your previously configured IP addresses and settings are not lost.

#### Step 3: Restart the Service

After the update is complete, it is necessary to restart the Wazuh Manager service.

```bash
sudo systemctl restart wazuh-manager
```

#### Step 4: Verify the Version

After starting the Wazuh Manager service, verify that the version has been updated correctly.

```bash
/var/ossec/bin/wazuh-control info
```

# How to Update Wazuh Manager (Docker)

If you are using Wazuh Docker, you will primarily use **`docker-compose`** (or **`kubectl`**). Most users rely on `docker-compose`.

#### Step 1: Take a Backup (Most Important)

To prevent data loss, it is essential to **back up** the Configuration and Databases located in your **Persistent Volumes**.

First, check where your Docker volumes are located (they are typically found inside the `wazuh-docker` directory under **`volumes`**).

#### Step 2: Modify the Docker Compose File

You need to modify the **`docker-compose.yml`** file that you used to start your **Wazuh Cluster**.

1.  **Change the Image Version:**

      * Open the `docker-compose.yml` file with a text editor like Notepad or Nano.
      * On the **`image:`** line for services like `wazuh-manager`, `elasticsearch/opensearch`, and `kibana/dashboard`, change the current version number to the **new version number you want to update to** (e.g., from `wazuh/wazuh-manager:4.x.x` to `wazuh/wazuh-manager:4.y.y`).

    ```yaml
    # Example: Changing from version 4.1.5 to 4.4.0
    services:
      wazuh-manager:
        image: wazuh/wazuh-manager:4.4.0  # <--- Change this line
        # ...
      wazuh-dashboard:
        image: wazuh/wazuh-dashboard:4.4.0 # <--- Change this line too
        # ...
    ```

#### Step 3: Rebuild the Containers

After saving the `docker-compose.yml` file, run the following command in the same directory (folder) where the file is located.

```bash
docker-compose pull
docker-compose up -d
```

  * **`docker-compose pull`**: This will download the new Docker Images for the new versions from Docker Hub.
  * **`docker-compose up -d`**: This will stop the current containers and recreate new ones using the **new Images** and the **old Data Volumes**.

-----

### 💡 Should You Avoid Updating?

In a **Docker Environment**, avoiding updates has **more significant drawbacks compared to a regular server**.

  * **Container Rebuild:** Docker containers are stateless, making them easy to recreate. You should always be ready to update.
  * **Image Security:** Old images may contain unpatched security vulnerabilities. Therefore, it is highly recommended to **update** your **containers** frequently.


### Single-node (standalone) setup

In a **Single-node** setup, the Wazuh Manager, Indexer (OpenSearch/Elasticsearch), and Dashboard are all running, either in a single container or as separate services.

### How to Update Single-node Docker Wazuh

Since your Wazuh system uses **Volumes that contain your Data (Configuration, Agent Keys, Logs)**, you must follow the steps carefully to **prevent data loss**.

#### Step 1: Take a Backup (Most Important)

Before updating, take a **Backup** of your **Volume Data**.

*   Navigate to the directory (folder) where your `docker-compose.yml` file is located.
*   Inside it, find the **`volumes`** folder where the Wazuh Data is stored and save it to a secure location, either by creating a **Zip** file or by making a copy.

#### Step 2: Stop the Docker Containers

You need to stop and remove the currently running containers. **Your Volume Data will remain intact.**

```bash
docker-compose down
```

#### Step 3: Pull the New Docker Images

Decide on the new version number you want to update to (e.g., **4.x.y**) and modify the **`docker-compose.yml`** file.

1.  Open the **`docker-compose.yml`** file with a text editor.
2.  Change the version number on the **`image:`** line for `wazuh-manager`, `wazuh-indexer`, `wazuh-dashboard` (or other services) to the **new Wazuh Version** you want to update to.
    *   Example: `image: wazuh/wazuh-manager:4.4.0`
3.  Save the file.

#### Step 4: Download the New Images

Pull the new images as specified in your modified file.

```bash
docker-compose pull
```

#### Step 5: Build the New Containers (Update)

Start the containers again using the new Images and the old Configuration (Data Volumes).

```bash
docker-compose up -d
```

*   This command will remove the existing containers and create new ones using the **new Images** without affecting your **Volumes (Data)**.


# Certificate Problem

### The Root Cause of the Problem

The recurring text in the logs you provided points directly to the issue:

> **`[ConnectionError]: unable to verify the first certificate`**

-----

###  Explanation

1.  **Certificate Problem:** The Wazuh Docker environment uses **HTTPS/TLS** (Secure Connections) for communication between its components, specifically between the **Wazuh Dashboard** and the **Wazuh Indexer (OpenSearch)**. This error means the **Dashboard Container** does **not trust the SSL Certificate** presented by the **Indexer Container**.
2.  **Caused by the Upgrade:** When you perform a Wazuh version update, it's common for a **Temporary Certificate Mismatch** to occur if the versions of the Manager, Indexer, and Dashboard become out of sync, or due to the container restart process.
3.  **Root Cause:** Typically in a Docker-based Wazuh setup, this error occurs because the **pre-generated certificates have expired** or are no longer valid for the new container instances.

-----

### Solution (Regenerating Certificates)

The solution is to **regenerate new certificates** and restart the containers.

#### Step 1: Stop the Containers

Ensure you are in your `single-node` directory and run:

```bash
docker-compose down
```

#### Step 2: Run the Docker Compose File to Generate Certificates

The Wazuh Docker package includes a utility file to automatically generate the necessary certificates.

```bash
docker-compose -f generate-indexer-certs.yml run --rm generator
```

  * **`generate-indexer-certs.yml`**: This is a specially created Docker Compose file for generating certificates.
  * **`--rm`**: This flag ensures the temporary container used for generation is removed immediately after the task is complete.

> **Note:** Running this command will **overwrite and replace** the old certificates in the **`config/certs`** directory with new ones.

#### Step 3: Restart the Wazuh Containers

Once the new certificates are generated, restart all three containers.

```bash
docker-compose up -d
```

### Final Step

After the containers are back `Up` (you can check with `docker-compose ps`), please wait for about **3 to 5 minutes** for the services to stabilize. Then, **refresh** your browser and try to log in again.

The new certificates should resolve the trust issue, and the connection between the Dashboard and Indexer should be restored.


# The Root Cause of the Problem

### Reassessing the Situation

The log message you provided, `[ConnectionError]: unable to verify the first certificate`, is indeed related to **Certificate (SSL/TLS)** trust.

The steps we took were:
1.  Stopped containers with `docker-compose down`.
2.  Generated **new Certificates** with `docker-compose -f generate-indexer-certs.yml run --rm generator`.
3.  Restarted containers with `docker-compose up -d`.

**If new certificates were generated but the Dashboard still cannot connect to the Indexer...**

### Solution (Workaround - Disabling Certificate Verification)

When facing persistent certificate verification issues after an update, a common and quick **workaround** is to temporarily modify the **Wazuh Dashboard** configuration to **disable certificate verification**.

#### Step 1: Open the Dashboard Configuration File

Navigate to your `single-node` directory and open the **Dashboard configuration file**.

```bash
nano config/wazuh_dashboard.yml
```

#### Step 2: Disable SSL Verification

Inside this file, look for the **OpenSearch (Indexer)** settings, typically found towards the end. Find and change the following line to **`none`**.

```yaml
# Wazuh Indexer (OpenSearch) Settings
opensearch.ssl.verificationMode: none
```

*   (It might originally be set to `full` or `certificate`. Changing it to **`none`** is the most common and effective setting for this workaround).
*   Be careful not to modify other parts of the file. Save and exit the editor (Ctrl+S, then Ctrl+X).

#### Step 3: Restart the Dashboard Container

For the new configuration to take effect, restart only the Dashboard container.

```bash
docker-compose restart wazuh.dashboard
```

*   This will force the Dashboard container to reload the configuration and connect to the Indexer **without performing SSL verification**.

-----

### Accessing the Dashboard

After the restart, wait for about **1-2 minutes** for the service to become stable, then refresh your browser.
