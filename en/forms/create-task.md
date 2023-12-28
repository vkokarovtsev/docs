# Creating an issue in {{ tracker-full-name }}


{% note warning %}

Integration with {{ tracker-short-name }} can be configured by users of [{{ forms-full-name }} for business](forms-for-org.md).

{% endnote %}


You can integrate your form with [{{ tracker-short-name }}]({{ link-tracker }}) to automatically create issues as users fill out the form. User responses are sent from the form to {{ tracker-short-name }}, where an issue is created based on them. For example, these forms add convenience when accepting service requests or collecting error messages. To learn more about issue, see [Help {{ tracker-full-name }}](../tracker/user/create-ticket.md).

## Set up issue creation in {{ tracker-short-name }} {#setup}

1. Select the form and open the **Integration** tab.

1. Select a [group of actions](notifications.md#add-integration) for which you want to create an issue and click ![](../_assets/forms/tracker-notification-new.png) **{{ tracker-short-name }}** at the bottom of the group.

1. Enter the [queue](../tracker/queue-intro.md) key to create your issue in.

1. To create a [sub-issue](../tracker/user/create-ticket.md#subtask), enable **Convert to sub-issue** and enter the parent issue key.


1. Set up the type, issue priority and other parameters.

   **Format of data input**. No suggestions appear when entering values in the fields such as **Reporter**, **Assignee**, **Tags**, or **Components**.

   - Specify a username in the **Reporter**, **Assignee**, and **Followers** fields.
      To enter multiple usernames in the **Followers** field, separate them with commas (for example, `smith,johnson`).

   - If no fill-out function was configured for the **Reporter** field and the form was filled by a person belonging to the organization, the **Reporter** field will show the person's name.

   - If the form is filled by an unauthorized user, the **Reporter** field will be set to <q>{{ tracker-name }} Robot</q> (`yndx-tracker-cnt-robot@`).

   - Enter the values of the other fields exactly as they are specified in {{ tracker-short-name }}.

   - To add multiple values to the **Components** or **Tags** field, separate them with commas.

   **Adding issue fields**. You can also create a new field if the one you need is not available in the issue parameters. Click **Add task parameter** and start typing its name. Then use the suggestion to select the relevant parameter. To learn more about issue parameters, see [Help {{ tracker-full-name }}](../tracker/user/create-param.md).


   **Add form data to a field**. You can insert a response to a prompt or other form data in issue fields:

   - Select the field and click **Variables** button to the right.

   - Select a [variable](vars.md) from the list to add to the field.

   {% note warning %}

   To add an employee specified in a response to the <q>People</q>, to the **Reporter**, **Assignee**, **Followers** fields in Tracker, add a **Response ID** variable to the field. If you use a **Response to prompt** variable, integration will not work.

   {% endnote %}

   For example, if you collect error messages through a form, you can add a user's message and technical details to the issue description.

   ![](../_assets/forms/tracker-var-example-new.png)

1. To get a link to the new issue after filling out the form, enable the **Show messages about the results of actions** option under the action name.

1. Click **{{ ui-key.yacloud.common.save }}**.

To create multiple issues at once, add new actions by clicking ![](../_assets/forms/tracker-notification-new.png) **{{ tracker-short-name }}** at the bottom of the page.

If you want an issue to only be created for users who give certain responses, [set your conditions](notifications.md#section_xlw_rjc_tbb).

> Example of integration with {{ tracker-short-name }} for a request form for buying equipment. Employees can use this form to make equipment requests as tasks for the purchasing department.
>
> ![](../_assets/forms/tracker-example-new.png)


## Troubleshooting {#troubles}

If issues aren't created in {{ tracker-short-name }} or if they're created incorrectly after the form is filled out, check for integration errors:

1. Open the form where you couldn't create an issue and click **Integration** at the top of the page.

1. Check for an error message in the {{ tracker-short-name }} integration settings.

1. Check if a solution to your problem is provided below. After resolving the problem, try [creating an issue again](notifications.md#status).

1. If the problem persists, [contact support](feedback.md).

### Error in the Reporter, Assignee, and Follower fields

The error may be caused by invalid data being sent from the form to the **Reporter**, **Assignee**, or **Followers** issue field. Fill in these fields in the following way:

- To add an employee manually, enter a username like `smith`.

- To enter multiple usernames in the **Followers** field, separate them with commas (for example, `smith,johnson`).

- To add an employee specified in a response to the <q>People</q>,, insert a **Response ID** [variable](vars.md) in the field. If you use a **Response to prompt** variable, integration will not work.

- To add an employee specified in a response to the <q>Drop-down list</q> or <q>Multiple answers</q> prompt, set usernames (e.g., `smith`) as response options and use the **Response to prompt** [variable](vars.md).

If there is an error in the **Reporter** field even though the field is populated correctly, make sure the user filling out the form is [authorized to create issues in the specified {{ tracker-short-name }} queue](#access).

### Error: No rights to add issues to queue {#access}

This error occurs because the user who filled out the form does not have the rights to create issues in the specified {{ tracker-short-name }} queue. Ask the queue owner to [check access rights](../tracker/manager/queue-access.md).


