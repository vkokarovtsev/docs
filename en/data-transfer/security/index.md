---
title: "Access management in {{ data-transfer-full-name }}"
description: "Access management in {{ data-transfer-full-name }}, a service for data transfer between storages. This section describes the roles required to perform a particular action, the resources for which you can assign a role, and the roles existing in the service."
---

# Access management in {{ data-transfer-name }}


In this section, you will learn:

* [Which resources you can assign a role for](#resources).
* [Which roles exist in the service](#roles-list).
* [Which roles are required](#required-roles) for particular actions.

To use the service, log in to the management console with a [Yandex account](../../iam/concepts/index.md#passport) or [federated account](../../iam/concepts/index.md#saml-federation).

{% include [about-access-management](../../_includes/iam/about-access-management.md) %}

## Which resources you can assign a role for {#resources}

You can assign a role for a [cloud](../../resource-manager/concepts/resources-hierarchy.md#cloud) or [folder](../../resource-manager/concepts/resources-hierarchy.md#folder). Cloud roles also apply to nested folders.

## Which roles exist in the service {#roles-list}

{% include [data-transfer-auditor](../../_includes/iam/roles/data-transfer-auditor.md) %}

{% include [data-transfer-viewer](../../_includes/iam/roles/data-transfer-viewer.md) %}

{% include [data-transfer-privateadmin](../../_includes/iam/roles/data-transfer-privateadmin.md) %}

{% include [data-transfer-admin](../../_includes/iam/roles/data-transfer-admin.md) %}

### {{ roles-viewer }} {#viewer}

{% include [roles-viewer](../../_includes/roles-viewer.md) %}

### {{ roles-editor }} {#editor}

{% include [roles-editor](../../_includes/roles-editor.md) %}

### {{ roles-admin }} {#admin}

{% include [roles-admin](../../_includes/roles-admin.md) %}

## Roles required {#required-roles}

To use the service, you need the `editor` [role](../../iam/concepts/access-control/roles.md) or higher to the folder that projects are being created in. With the `viewer` role, you can only view the list of projects and the contents of files that were downloaded.

To create or edit an endpoint of a managed database, you need the service or primitive [`viewer` role](../../iam/concepts/access-control/roles.md) assigned for the folder hosting a cluster of this managed database.

You can always assign a role granting more permissions than the role specified. For example, assign the `admin` role instead of `editor`.

## What's next {#whats-next}

* [How to assign a role](../../iam/operations/roles/grant.md).
* [How to revoke a role](../../iam/operations/roles/revoke.md).
* [Learn more about access management in {{ yandex-cloud }}](../../iam/concepts/access-control/index.md).
* [Learn more about inheriting roles](../../resource-manager/concepts/resources-hierarchy.md#access-rights-inheritance).

