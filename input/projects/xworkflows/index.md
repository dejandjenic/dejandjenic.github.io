title: xworkflows
created: Monday, July 31, 2023
tags:
  - xworkflows
short: xworkflows
---
# XWorkflows Library

XWorkflows is a .NET library designed to simplify the management of workflows on entities, providing a clean and organized way to handle state transitions and actions for your entities. With XWorkflows, you can easily define and enforce a set of state transitions and actions, reducing spaghetti code and duplication in your application.

## Key Concepts

Before diving into the details of the library, it's essential to understand some of the key concepts and terms used within XWorkflows:

* Workflow Entity: An entity whose state transitions are subject to a workflow behavior.
* Workflow Action: A piece of code executed upon a trigger, such as an HTTP endpoint. This code is executed within the Execute method of an * action and can perform tasks like storing field values or performing calculations.
* Workflow Event: A piece of code that executes after an action execution. It can handle tasks like logging events, post-action execution logic, etc.
* Workflow State: A state to which an entity transitions after an action is executed.
* Workflow: A logical grouping of actions on the same entity.

## Getting Started

```bash
dotnet add package XWorkflows
```


## Basic Usage

* Define your entity class implementing IWorkflowStateEntity<TState>, where TState is the type representing the state of your entity.
* Create your custom state classes that inherit from WorkflowStateBase<TEntity, TState, TWorkflow>, providing the necessary state-specific logic.
* Implement your workflow-specific actions by inheriting from MyWorkflowAction and override the Execute method to provide your custom action logic.
* Define your workflow by creating a class that inherits from WorkflowBase.

Here's a basic example to create a workflow for an order entity with three actions: Create, Submit, and Cancel:


```csharp
// Define your entity class
public class OrderEntity : IWorkflowStateEntity<State>
{
    // Implement IWorkflowStateEntity members...
}

// Create your custom states
public class CreateOrderState : MyWorkflowState
{
    // Implement CreateOrderState specific logic...
}

public class SubmitOrderState : MyWorkflowState
{
    // Implement SubmitOrderState specific logic...
}

public class CancelOrderState : MyWorkflowState
{
    // Implement CancelOrderState specific logic...
}

// Define your actions
public class CreateOrderAction : MyWorkflowAction<CreateOrderEvent, CreateOrderState, CreateOrderActionRequest, CreateOrderActionRequest, string>, IWorkflowStartAction
{
    // Implement CreateOrderAction logic...
}

public class SubmitOrderAction : MyWorkflowAction<SubmitOrderEvent, SubmitOrderState, SubmitOrderActionRequest, SubmitOrderActionRequest, bool>
{
    // Implement SubmitOrderAction logic...
}

public class CancelOrderAction : MyWorkflowAction<CancelOrderEvent, CancelOrderState, CancelOrderActionRequest, CancelOrderActionRequest, bool>
{
    // Implement CancelOrderAction logic...
}

// Define your workflow
public class OrderWorkflow : WorkflowBase<OrderWorkflow, OrderEntity, State>
{
    public OrderWorkflow(IEnumerable<IWorkflowOwner<OrderWorkflow>> actions) : base(actions)
    {
    }
}

```

## Documentation

read more about XWorkflows documentation [here](../doc/xworkflows/index.html)