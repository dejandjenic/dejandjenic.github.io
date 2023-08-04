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

# Workflows
Workflows in XWorkflows provide a structured way to manage the behavior of entities throughout their lifecycles. A workflow defines a logical grouping of related actions and states for a specific entity. To work with workflows in XWorkflows, you'll need to create custom workflow classes and register the relevant actions and states for each entity.

## 1. Defining a Custom Workflow
To create a custom workflow, define a class that inherits from WorkflowBase<TWorkflow, TEntity, TState>. This class will serve as the blueprint for managing the workflow behavior of your entity.

```csharp
public class OrderWorkflow : WorkflowBase<OrderWorkflow, OrderEntity, State>
{
    public OrderWorkflow(IEnumerable<IWorkflowOwner<OrderWorkflow>> actions) : base(actions)
    {
        
    }
}
```
XWorkflows use dependency injection to register workflow related classes. In your Program.cs your should invoke method RegisterWorkflows.

In this example, we have defined the OrderWorkflow class, which inherits from WorkflowBase. We have registered the relevant actions (SubmitOrderAction, CancelOrderAction, DeliverOrderAction) and states (CreatedState, SubmittedState, DeliveredState, CanceledState) for the OrderEntity workflow.

## 2. Registering Actions and States
XWorkflows use dependency injection to register workflow related classes. In your Program.cs your should invoke method RegisterWorkflows after this all workflow related classes will be registered with DI.

## 3. Executing Actions in the Workflow
To execute an action within a workflow, create an instance of your custom workflow class, and then call the ExecuteAction() method:

```csharp
var orderWorkflow = new OrderWorkflow(actions);

var order = new OrderEntity { /* Initialize properties */ };
var submitRequest = new SubmitOrderActionRequest { /* Request data, if needed */ };
var (result, response) = await orderWorkflow.ExecuteAction(order, submitRequest);
```
By executing an action within a workflow, XWorkflows ensures that the entity transitions through the defined states based on the action's logic and AllowedTransitions defined in the states.

## 4. Handling Errors and Exceptions
XWorkflows provides robust error handling to ensure that the workflow execution is smooth and reliable. If an action is executed without permission to transition the entity to a desired state or if an action fails during execution, XWorkflows will throw an exception with a meaningful error message to help diagnose and handle the issue gracefully.

## 5. Multiple Workflows (Optional)
In XWorkflows, you can manage multiple workflows for different entities in your application. Simply define separate custom workflow classes for each entity and register their relevant actions and states. This enables you to keep the workflows separate and organized, ensuring a clear and modular workflow management structure.