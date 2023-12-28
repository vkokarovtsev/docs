---
title: "Access management in {{ backup-full-name }} (S3)"
description: "Access management in {{ backup-full-name }}, a service for backing up data in {{ yandex-cloud }}. This section describes the resources for which you can assign a role and the roles existing in the service."
---

# Access management in {{ backup-name }}

In this section, you will learn:

* [Which resources you can assign a role for](#resources).
* [Which roles exist in the service](#roles-list).

{% include [about-access-management](../../_includes/iam/about-access-management.md) %}

## Which resources you can assign a role for {#resources}

Using the {{ yandex-cloud }} console or the YC CLI, you can assign a role to a [cloud](../../resource-manager/concepts/resources-hierarchy.md#cloud) or [folder](../../resource-manager/concepts/resources-hierarchy.md#folder). These assigned roles will also apply to nested resources.

## Which roles exist in the service {#roles-list}

### Service roles {#service-roles}

#### backup.admin {#backup-admin}

{% include notitle [roles-backup-admin](../../_includes/roles-backup-admin.md) %}

#### backup.editor {#backup-editor}

{% include notitle [roles-backup-editor](../../_includes/roles-backup-editor.md) %}

#### backup.viewer {#backup-viewer}

{% include notitle [roles-backup-viewer](../../_includes/roles-backup-viewer.md) %}

### Primitive roles {#primitive-roles}

{% include [roles-primitive](../../_includes/roles-primitive.md) %}
