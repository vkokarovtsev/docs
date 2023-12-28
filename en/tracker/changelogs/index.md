---
title: "{{ tracker-full-name }} revision history for November 2023"
description: "See below the {{ tracker-full-name }} revision history for November 2023."
---

# {{ tracker-full-name }} revision history for November 2023

* [Ideas from our users](#your-ideas)
* [Updates](#top-news)
* [Fixes and improvements](#fixes)

## Ideas from our users {#your-ideas}


While working on our main goals, we also try to listen to our users' ideas. This month, we implemented one of their ideas:


### PATCH method for HTTP requests {#patch-method}

**29 votes**

You can now select the PATCH method for an [HTTP request](../user/set-action.md#create-http) in the action settings when creating a trigger.

## Updates {#top-news}


### Onboarding for {{ tracker-name }} users {#onboarding}

Onboarding is now available to new users, including a number of step-by-step tutorials in the {{ tracker-name }} interface:

* Creating an issue from navigation
* Setting up workflows in queues
* Inviting users
* Downloading an application

To see the tutorials, click ![](../../_assets/console-icons/circle-question.svg) **Help center** → **Run quick guide** in the left-hand panel.


### Portfolio and project change history {#notifications-projects-portfolios}

You can now view the change history for portfolios and projects. For more information about what notifications are sent to users depending on their role in a portfolio or project, see our [documentation](../user/notifications-projects-portfolios.md).

### Issues filter {#task-filter}

An [issue list]({{ link-tracker }}issues) now has a permanent Issues filter that allows displaying all available issues, either in progress or completed. An issue is considered completed if its [status](../manager/workflow-status-edit.md#status-types) is **Completed** or **Canceled** and/or it has a [resolution](../manager/create-resolution.md).

![](../../_assets/tracker/changelogs/issues-tasks-filter.png =250x)

### Adding links when creating an issue {#task-links}

The [issue creation](../user/create-ticket.md) interface has been updated to enable you to [add links](../user/ticket-links.md) in a pop-up window. You can select a link type and specify the link to or key of the issue you want to link.

![](../../_assets/tracker/changelogs/new-task-links.png =350x)

### Cloning dashboards {#create-dashboard-copy}

You can now clone dashboards. You can create copies of any dashboard, except for empty ones (those without widgets) and the [**My page** dashboard](../user/startpage.md#my-page).

## Fixes and improvements {#fixes}


### Adding nested portfolios and projects {#add-projects-and-portfolio}

You can now add nested portfolios and projects to portfolios by clicking ![](../../_assets/console-icons/plus.svg). The add button is now available on the following pages:
* [**Portfolios and projects**]({{ link-tracker }}pages/projects), the **Structure** tab.
* Individual portfolio page, the **Projects** and **Gantt chart** tabs.

### Improvements in project operations {#improvements-in-projects}

* You can now add and change users in the **Follower** field in a project.
* The header of a project now displays quarters next to the project name. A quarter corresponds to specified project timeframes.
* Now, attached files in projects and portfolios are shown separately for a description and comments, as they are in issues. Files attached to comments are displayed in the **Attachments** tab.

### Viewing and creating queue components {#queue-components}

The queue components page now has a new {{ tracker-name }} interface. To view a queue's components, click ![](../../_assets/console-icons/ellipsis.svg) → ![](../../_assets/console-icons/tags.svg) **Components** on the queue page.

### Queue team {#queue-team}

The **Queue team** tab in the queue settings now has a new interface. You can use this tab to add employees: the team members will be able to see the queue under **My queues**.

You can edit the queue team by removing its members, including through bulk edits. You can also search for team members and use suggestions in the search field by English and Russian characters.

### Filtering by SLA {#sla-filter}

You can now filter issue lists by the **SLA** field, if [configured](../manager/sla.md) for the queue.
