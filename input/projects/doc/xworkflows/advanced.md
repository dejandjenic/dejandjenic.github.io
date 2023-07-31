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

# Advanced Usage
In addition to the basic workflow management features, XWorkflows offers several advanced functionalities to cater to complex workflow scenarios and customization requirements.

## 1. Customizing Action and Event Execution
XWorkflows allows you to customize the execution of actions and events. By overriding methods in the action and event classes, you can implement specific behavior before and after the execution of actions and events. This enables you to integrate with external systems, perform additional validation, or modify the action/event data as needed.

```csharp
public class CustomSubmitOrderAction : SubmitOrderAction
{
    protected CustomSubmitOrderAction(SubmittedState state, SubmitOrderEvent e) : base(state, e)
    {
    }

    // Override the Execute method to add custom logic before and after action execution
    public override async Task<(WorkflowActionResult<bool>, SubmitOrderActionRequest)> Execute(OrderEntity data, SubmitOrderActionRequest p, CancellationToken cancellationToken)
    {
        // Custom pre-execution logic
        Log("Executing CustomSubmitOrderAction...");

        // Execute the base action's logic
        var result = await base.Execute(data, p, cancellationToken);

        // Custom post-execution logic
        Log("CustomSubmitOrderAction executed successfully!");

        return result;
    }
}
```
## 2. Handling Action Parameters and Responses
XWorkflows allows you to pass custom parameters to actions and retrieve responses from actions. This allows you to provide additional data to actions and receive feedback after execution.

```csharp
// Define custom action parameters and response
public class CustomActionParameters
{
    // Define custom properties here
}

public class CustomActionResponse
{
    // Define custom properties here
}

public class CustomAction : MyWorkflowAction<MyWorkflowEvent, MyWorkflowState, CustomActionParameters, CustomActionParameters, CustomActionResponse>
{
    protected CustomAction(MyWorkflowState state, MyWorkflowEvent e) : base(state, e)
    {
    }

    public override Task<(WorkflowActionResult<CustomActionResponse>, CustomActionParameters)> Execute(MyWorkflowEntity data, CustomActionParameters p, CancellationToken cancellationToken)
    {
        // Implement the action logic here

        // Access custom action parameters
        var customData = p;

        // Return custom action response
        var response = new CustomActionResponse { /* Populate properties */ };
        return Task.FromResult((WorkflowActionResult<CustomActionResponse>.Succeeded(response), p));
    }
}
```
## 3. Managing Multiple Workflows
XWorkflows allows you to manage multiple workflows for different entities in your application. Simply create separate custom workflow classes for each entity and register their relevant actions and states. This enables you to keep the workflows separate and organized, ensuring a clear and modular workflow management structure.

## 4. Integrating with Dependency Injection
XWorkflows can be easily integrated with dependency injection frameworks like Microsoft's ASP.NET Core Dependency Injection. By registering actions and workflow classes as services, you can achieve better decoupling and enable easier unit testing.

```csharp
// Register the OrderWorkflow and its dependencies with DI container
services.AddScoped<OrderWorkflow>();
services.AddScoped<IWorkflowOwner<OrderWorkflow>, SubmitOrderAction>();
services.AddScoped<IWorkflowOwner<OrderWorkflow>, CancelOrderAction>();
services.AddScoped<IWorkflowOwner<OrderWorkflow>, DeliverOrderAction>();
// Add more actions as needed
```
## 5. Implementing Conditional Transitions
XWorkflows provides flexibility in defining conditional transitions between states. You can override the AllowedTransitions method in a state class to define complex logic for allowing or denying state transitions based on specific conditions.

```csharp
public class ConditionalTransitionState : MyWorkflowState
{
    public override IEnumerable<Type> AllowedTransitions()
    {
        // Implement custom logic for conditional transitions
        if (/* Some condition here */)
        {
            yield return typeof(AllowedAction);
        }
        else
        {
            yield return typeof(DeniedAction);
        }
    }
}
```
