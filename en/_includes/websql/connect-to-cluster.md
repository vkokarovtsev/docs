{% note warning %}

You can establish connections to managed DB clusters from your {{ yandex-cloud }} [organization's](../../organization/concepts/manage-services.md) clouds and folders. If you [switch to a different organization](../../organization/operations/manage-organizations.md#switch-to-another-org), a set of clusters from other clouds and folders will be available to you.

{% endnote %}

1. Make sure the **{{ ui-key.yacloud.mdb.forms.additional-field-websql }}** option is enabled in the [{{ PG }}](../../managed-postgresql/operations/cluster-create.md), [{{ MY }}](../../managed-mysql/operations/cluster-create.md), or [{{ CH }}](../../managed-clickhouse/operations/cluster-create.md) cluster settings.
1. In {{ websql-full-name }}, go to the **Connection manager** section and click ![image](../../_assets/console-icons/square-plus.svg) in the top-right corner.
1. In the window that opens, click **Import from Yandex Cloud**.
1. Specify the connection parameters:
   * **Cloud**: Select a cloud.
   * **Folder**: Select the folder where you created the database cluster.
   * **Cluster**: Select the cluster.
1. Click **Import** to connect to the selected cluster.

Imported connections will be available while you are working in the current {{ yandex-cloud }} organization.