# Managing database connections

{% include notitle [preview](../../_includes/note-preview.md) %}

You can use the **Connection manager** (![image](../../_assets/console-icons/folder-tree.svg)) panel to:

* Add connections to [databases](#connect-db) or [database clusters](#connect-cluster).
* [Check connection settings](#change-connection-settings).
* [Re-establish previously created connections](#update-connection).
* [Delete connections](#delete-connection) that you no longer need.
* [Work with demo connections](#demo) to explore {{ websql-full-name }}.

## Connecting to a database {#connect-db}

{% include notitle [connect-to-db](../../_includes/websql/connect-to-db.md) %}

## Connecting to a database cluster in {{ yandex-cloud }} {#connect-cluster}

If you have a [{{ PG }}](../../managed-postgresql/operations/cluster-list.md), [{{ CH }}](../../managed-clickhouse/operations/cluster-list.md), or [{{ MY }}](../../managed-mysql/operations/cluster-list.md) cluster configured in {{ yandex-cloud }}, you can connect directly to it:

{% include notitle [connect-to-cluster](../../_includes/websql/connect-to-cluster.md) %}

## Renaming a connection group {#rename-group}

The databases belonging to the same cluster are displayed in the connections tree as _connection groups_. In the connection settings, such databases will have the same URL and port number.

To rename a connection group:

1. Select a group in the connections tree.
1. Click **...** to the right of the group name and choose **Rename group**.
1. In the window that opens, specify a new group name and click **Apply**.

## Viewing connection settings {#change-connection-settings}

To view connection settings:

1. Select a connection or a connection group.
1. Click **...** to the right of the connection name and choose **Connection information**.

A panel with connection settings will open.

## Viewing information about a table {#view-table}

To view detailed information about a table and its columns:

1. Select a table in the database you are connected to.
1. To the right of the table name, click ![image](../../_assets/console-icons/sliders-vertical.svg) and select **Table information**.

This opens a panel with information about the table, including its name, size, and an approximate number of table rows. You can also get the following information about table columns:

* Column name.
* Data type.
* Whether the column is Nullable.
* Whether the column is a primary key.

## Reconnecting to databases and clusters {#update-connection}

In the Connection manager, you can view the status of connections and refresh them or reconnect to databases and clusters. Active connections are marked in green, inactive connections are marked in red.

{% note tip %}

Use the search field in the **Connection manager** panel to find a connection with the appropriate database, table, or schema.

{% endnote %}

To refresh an active connection:

1. Select an active connection.
1. Click **...** to the right of the connection name and choose **Refresh connection**.

To refresh all active connections, click ![image](../../_assets/console-icons/arrows-rotate-right.svg) in the top-right corner of the **Connection manager** panel.

To re-establish inactive connections:

1. Select an inactive connection.
1. Click **...** to the right of the connection name and choose **Reconnect**.
1. Enter your username and password for database access and click **Connect**.

## Deleting a connection {#delete-connection}

To delete the connections you no longer use:

1. Select a connection or a connection group.
1. Click **...** to the right of their name and choose **Delete connection** or **Delete group**.

## Managing demo connections {#demo}

Demo connections are pre-established connections with test databases that allow you to explore the {{ websql-full-name }} features. Demo connections are added by default. You cannot [delete](#delete-connection) or [rename](#rename-group) them, but you can hide them in the **Connection manager** panel:

1. Click **Settings** (![image](../../_assets/console-icons/gear.svg)) in the bottom-left corner.
1. Disable **Show demo connections** in the general settings.

{% include [clickhouse-disclaimer](../../_includes/clickhouse-disclaimer.md) %}
