# Working with logs

{{ dataproc-name }} cluster logs are collected and displayed by [{{ cloud-logging-full-name }}](../../logging).

To monitor the events on the cluster and its individual hosts, specify, in its settings, a relevant [log group](../../logging/concepts/log-group.md). You can do this when [creating](cluster-create.md) or [updating](cluster-update.md) the cluster. If no log group has been selected for the cluster, a default log group in the cluster directory will be used to send and store logs.

For more information, see [{#T}](../concepts/logs.md).

## Viewing log entries {#logging-cluster}

{% list tabs %}

- Management console

   1. Go to the [folder page]({{ link-console-main }}) and select **{{ ui-key.yacloud.iam.folder.dashboard.label_data-proc }}**.
   1. Click the cluster name.
   1. Under **{{ ui-key.yacloud.mdb.cluster.overview.section_configuration }}**, click the name of the cluster log group. The {{ cloud-logging-name }} page opens.
   1. Click the row of the log group. This will open the cluster logs.
   1. (Optional) Specify the output settings:
      * [Message filter](../concepts/logs.md):
         * Getting the job start output {{ dataproc-name }}:

            ```ini
            job_id="<job_ID>"
            ```

         * Getting the stdout output for all YARN application containers:

            ```ini
            application_id="<YARN_application_ID>" AND yarn_log_type="stdout"
            ```

         * Getting YARN container's stderr output:

            ```ini
            container_id="<YARN_container_ID>" AND yarn_log_type="stderr"
            ```

         * Getting the YARN Resource Manager service logs from the cluster's master host:

            ```ini
            hostname="<master_host_FQDN>" AND log_type="hadoop-yarn-resourcemanager"
            ```

      * Message logging levels: From `TRACE` to `FATAL`.
      * Number of messages per page.
      * Message interval (one of the standard intervals or an ad-hoc one).

- CLI

   {% include [cli-install](../../_includes/cli-install.md) %}

   View a description of the CLI command to get logs:

   ```bash
   yc logging read --help
   ```

   Examples:

   * To get logs of the {{ dataproc-name }}cluster's HDFS NameNode service, run the command:

      ```bash
      yc logging read \
         --group-id=<log_group_ID> \
         --resource-ids=<cluster_ID> \
         --filter=log_type=hadoop-hdfs-namenode
      ```

   * To get logs for the last two hours from all {{ dataproc-name }} clusters assigned to a specific log group, run the command:

      ```bash
      yc logging read \
         --group-id=<log_group_ID> \
         --resource-types=dataproc.cluster \
         --since=2h
      ```

   * To get your cluster's system log for a specific period, run this command:

      ```bash
      yc logging read \
         --group-id <log_group_ID> \
         --resource-ids=<cluster_ID> \
         --filter 'syslog' \
         --since 'YYYY-MM-DDThh:mm:ssZ' \
         --until 'YYYY-MM-DDThh:mm:ssZ'
      ```

      Set the logging period in the `--since` and `--until` parameters. Time format: `YYYY-MM-DDThh:mm:ssZ`. Example: `2020-08-10T12:00:00Z`. The time zone must be specified in UTC format.

   * To get a log for metrics sent from a specific host to [{{ monitoring-full-name }}](../../monitoring/index.yaml), run this command:

      ```bash
      yc logging read \
         --group-id <log_group_ID> \
         --resource-ids=<cluster_ID> \
         --filter 'telegraf and hostname="<host_FQDN>"' \
         --since 'YYYY-MM-DDThh:mm:ssZ' \
         --until 'YYYY-MM-DDThh:mm:ssZ'
      ```

      To get the host FQDN:

      1. Go to the [folder page]({{ link-console-main }}) and select **{{ ui-key.yacloud.iam.folder.dashboard.label_data-proc }}**.
      1. Click the cluster name.
      1. Go to the **{{ ui-key.yacloud.mdb.cluster.switch_hosts }}** tab.
      1. Copy the host FQDN.

{% endlist %}

## Disabling sending logs {#disable-logs}

{% list tabs %}

- Management console

   When [creating](cluster-create.md) or [updating the cluster](cluster-update.md), add the `dataproc:disable_cloud_logging` property set to `true`.

- CLI

   {% include [cli-install](../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../_includes/default-catalogue.md) %}

   When [creating](cluster-create.md) or [updating the cluster](cluster-update.md) pass the `dataproc:disable_cloud_logging=true` value in the `--property` parameter or pass an empty string (`""`) instead of the log group ID in the `--log-group-id` parameter:

   ```bash
   {{ yc-dp }} cluster create <cluster name> \
      ... \
      --log-group-id=""
   ```

   ```bash
   {{ yc-dp }} cluster update <cluster_name_or_ID> \
      --property dataproc:disable_cloud_logging=true
   ```

{% endlist %}

## Storing logs {#logs-storage}

Receiving and storing logs is paid based on the {{ cloud-logging-name }} [pricing rules](../../logging/pricing.md). The default log retention period is three days. To update the retention period, [edit the log group settings](../../logging/operations/retention-period.md).

For more information about working with logs, see the [{{ cloud-logging-name }} documentation](../../logging/operations/index.md).
