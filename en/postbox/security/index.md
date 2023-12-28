---
title: "Access management in {{ postbox-full-name }}"
description: "Access management in {{ postbox-full-name }}, a transactional email service. To grant access to {{ postbox-full-name }} resources, assign relevant roles from the list to the user."
---

# Access management in {{ postbox-full-name }}

{{ yandex-cloud }} users can only perform operations on resources that are allowed by the roles assigned to them. If the user has no roles assigned, all operations are forbidden.

To allow access to {{ postbox-name }} resources, assign the required roles from the list below to the Yandex account, [service account](../../iam/concepts/users/service-accounts.md), [federated users](../../iam/concepts/federations.md), [user group](../../organization/operations/manage-groups.md), or [system group](../../iam/concepts/access-control/system-group.md). Currently, a role can only be assigned to a parent resource (folder or cloud). Roles are inherited by nested resources.

{% note info %}

For more information about role inheritance, see [Inheritance of access rights](../../resource-manager/concepts/resources-hierarchy.md#access-rights-inheritance) in the {{ resmgr-name }} documentation.

{% endnote %}

## Which roles exist in the service {#roles-list}

In {{ postbox-name }}, you can manage access using both service and primitive roles.

### Service roles {#service-roles}

{% include [roles-postbox-sender](../../_includes/roles-postbox-sender.md) %}

{% include [roles-postbox-auditor](../../_includes/roles-postbox-auditor.md) %}

{% include [roles-postbox-viewer](../../_includes/roles-postbox-viewer.md) %}

{% include [roles-postbox-editor](../../_includes/roles-postbox-editor.md) %}

{% include [roles-postbox-admin](../../_includes/roles-postbox-admin.md) %}

### Primitive roles {#primitive-roles}

{% include [roles-primitive](../../_includes/roles-primitive.md) %}

## See also {#see-also}

[Hierarchy of {{ yandex-cloud }} resources](../../resource-manager/concepts/resources-hierarchy.md)
