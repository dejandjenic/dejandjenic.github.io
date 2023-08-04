title: xworkflows
created: Monday, July 31, 2023
tags:
  - xworkflows
short: xworkflows
---
<a href="index.html">Introduction</a><br>
<a href="installation.html">Installation</a><br>
<a href="basic.html">Basic concepts</a><br>
<a href="starting.html">Getting Started</a><br>
<a href="entities.html">Entities</a><br>
<a href="actions.html">Actions</a><br>
<a href="events.html">Events</a><br>
<a href="workflow.html">Workflow</a><br>
<a href="errors.html">Error handling</a><br>
<a href="advanced.html">Advanced usage</a><br>
<a href="examples.html">Examples</a><br>

# Events
Events in XWorkflows allow you to perform additional tasks after an action is successfully executed. These tasks may include logging events, triggering notifications, or updating related data. To work with events in XWorkflows, you'll need to define custom event classes and associate them with the relevant actions.

## 1. Creating Custom Events
To create a custom event, you'll need to define a class that inherits from MyWorkflowEvent. This class should be parameterized with the appropriate data type representing the event data. Let's take an example of the SubmitOrderEvent, which logs the submission of an order:

```csharp
public class SubmitOrderActionRequest
{
    // Define any properties related to the action request, if needed
}

public class SubmitOrderEvent : MyWorkflowEvent<SubmitOrderActionRequest>
{
    public override async Task Execute(SubmitOrderActionRequest data, CancellationToken cancellationToken)
    {
        // Implement the event logic here
        // For example, log the submission of the order
        Log($"Order submitted with request data: {data}");

        // You can perform additional tasks like sending notifications or updating related data
        await SendNotificationToCustomerAsync(data);
    }
}
```
In this example, the SubmitOrderEvent inherits from MyWorkflowEvent and is parameterized with SubmitOrderActionRequest, which represents the data associated with the event.

## 2. Associating Events with Actions
To associate events with actions, you'll need to specify the event type when defining the custom action class. When an action is executed successfully, the associated event will be triggered automatically.

```csharp
public class SubmitOrderAction : MyWorkflowAction<SubmitOrderEvent, SubmittedState, SubmitOrderActionRequest, SubmitOrderActionRequest, bool>
{
    protected SubmitOrderAction(SubmittedState state, SubmitOrderEvent e) : base(state, e)
    {
    }

    public override Task<(WorkflowActionResult<bool>, SubmitOrderActionRequest)> Execute(OrderEntity data, SubmitOrderActionRequest p, CancellationToken cancellationToken)
    {
        // Implement the action logic here
        // For example, update the order's state to Submitted and perform other tasks

        data.State = State.Submitted;
        return Task.FromResult((WorkflowActionResult<bool>.Succeeded(true), p));
    }
}
```
In this example, the SubmitOrderAction class is associated with the SubmitOrderEvent by specifying SubmitOrderEvent as the second generic parameter in the MyWorkflowAction inheritance.

## 3. Execution and Post-Action Tasks
When an action is executed, XWorkflows will automatically trigger the associated event after the action's logic is executed successfully. The Execute method of the event class is called, allowing you to perform any post-action tasks.

```csharp
// Assuming you have an instance of the OrderWorkflow
var orderWorkflow = new OrderWorkflow(actions);

// Create an instance of the OrderEntity
var order = new OrderEntity { /* Initialize properties */ };

// Execute the SubmitOrderAction
var submitRequest = new SubmitOrderActionRequest { /* Request data, if needed */ };
var (result, response) = await orderWorkflow.ExecuteAction(order, submitRequest);

// After the action is executed successfully, the associated SubmitOrderEvent will be triggered automatically.
// It will execute the logic defined in the SubmitOrderEvent class, such as logging the submission and sending notifications.
```
## 4. Customizing Event Logic (Optional)
You can customize the event logic to suit your application's needs. For example, you can add more tasks to be performed after the action, modify the event data, or integrate with external services to trigger specific actions.