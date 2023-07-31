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

# Actions
Actions in XWorkflows represent specific operations that trigger state transitions for entities. When an action is executed, it performs tasks and updates the state of the entity accordingly. To work with actions in XWorkflows, you need to define custom action classes and integrate them into the relevant workflows.

## 1. Creating Custom Actions
To create a custom action, you'll need to define a class that inherits from MyWorkflowAction. This class should be parameterized with the appropriate types, depending on the action's behavior. Let's take an example of the SubmitOrderAction, which triggers the transition of an order from the Created state to the Submitted state:

```csharp
public class SubmitOrderActionRequest
{
    // Define any properties related to the action request, if needed
}

public class SubmitOrderEvent : MyWorkflowEvent<SubmitOrderActionRequest>
{
    public override Task Execute(SubmitOrderActionRequest data, CancellationToken cancellationToken)
    {
        // Implement any post-action logic here (optional)
        return Task.CompletedTask;
    }
}

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
In this example, we have created the SubmitOrderAction, which inherits from MyWorkflowAction. The action is parameterized with the SubmitOrderEvent, SubmittedState, SubmitOrderActionRequest, SubmitOrderActionRequest, and bool types. The Execute method contains the logic for the action, updating the entity's state and performing any other necessary tasks.

## 2. Using Actions in Workflows
To use actions in your workflows, you need to create a custom workflow class that inherits from WorkflowBase<TWorkflow, TEntity, TState>. Then, you can register the relevant actions for your entity within the workflow's constructor.

```csharp
public class OrderWorkflow : WorkflowBase<OrderWorkflow, OrderEntity, State>
{
    public OrderWorkflow(IEnumerable<IWorkflowOwner<OrderWorkflow>> actions) : base(actions)
    {
        // Register actions for the OrderEntity workflow
        RegisterAction<SubmitOrderAction>();
        RegisterAction<CancelOrderAction>();
        // Add more actions as needed
    }
}
```
In this example, OrderWorkflow extends WorkflowBase and registers the SubmitOrderAction and CancelOrderAction for the OrderEntity workflow.

## 3. Execution and Error Handling
To execute an action on an entity, simply call the ExecuteAction method on the corresponding workflow instance:

```csharp
// Assuming you have an instance of the OrderWorkflow
var orderWorkflow = new OrderWorkflow(actions);

// Create an instance of the OrderEntity
var order = new OrderEntity { /* Initialize properties */ };

// Execute the SubmitOrderAction
var submitRequest = new SubmitOrderActionRequest { /* Request data, if needed */ };
var (result, response) = await orderWorkflow.ExecuteAction(order, submitRequest);
```

XWorkflows takes care of error handling when an action is executed without permission to transition the entity to a desired state or if the action fails. It will throw an exception with a meaningful error message, guiding you to resolve the issue.