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

# Getting Started
This section will guide you through the process of setting up XWorkflows and implementing a basic workflow for an entity in your .NET project. By the end of this guide, you will have a clear understanding of how to use XWorkflows to manage workflows on your entities efficiently.

## Step 1: Installation
First, make sure you have the XWorkflows library installed in your .NET project. If you haven't installed it yet, follow the instructions in the Installation section to add the package using NuGet or .NET CLI.

## Step 2: Define Your Entity
To get started with XWorkflows, you'll need an entity that will be subject to workflow state transitions. For demonstration purposes, let's create a simple OrderEntity class representing an order in an e-commerce application:

```csharp
public class OrderEntity : IWorkflowStateEntity<State>
{
    public string Identifier { get; set; }
    public State State { get; set; }

    public async Task<string> GetIdentifier()
    {
        return Identifier;
    }

    public async Task<State> GetState()
    {
        return State;
    }

    public async Task SetState(State state, string identifier)
    {
        State = state;
    }
}
```
In this example, OrderEntity implements the IWorkflowStateEntity<State> interface, which defines methods to get and set the entity's state and identifier.

## Step 3: Create Custom States
Now, let's create custom state classes that define the states the OrderEntity can transition through in its workflow. For simplicity, we'll define three states: Created, Submitted, and Canceled.

```csharp
public class CreatedState : MyWorkflowState
{
    public override string Identifier => "Created";

    public override IEnumerable<Type> AllowedTransitions()
    {
        yield return typeof(SubmittedState);
        yield return typeof(CanceledState);
    }

    public async override Task<State> GetState(OrderEntity data)
    {
        return State.Created;
    }
}

public class SubmittedState : MyWorkflowState
{
    public override string Identifier => "Submitted";

    public override IEnumerable<Type> AllowedTransitions()
    {
        yield return typeof(DeliveredState);
        yield return typeof(CanceledState);
    }

    public async override Task<State> GetState(OrderEntity data)
    {
        return State.Submitted;
    }
}

public class CanceledState : MyWorkflowState
{
    public override string Identifier => "Canceled";

    public override IEnumerable<Type> AllowedTransitions()
    {
        // No transitions allowed from CanceledState
        yield break;
    }

    public async override Task<State> GetState(OrderEntity data)
    {
        return State.Canceled;
    }
}
```
Here, each state class extends MyWorkflowState and overrides the Identifier property and AllowedTransitions method to define its unique behavior.

## Step 4: Implement Actions and Events
Next, let's create actions for our workflow. Actions represent specific operations that trigger state transitions. For this example, we'll create SubmitOrderAction, CancelOrderAction, and DeliverOrderAction.

```csharp
// Implement SubmitOrderAction, CancelOrderAction, and DeliverOrderAction classes
// ...

// Define events for each action (optional)
// ...
For brevity, we'll omit the implementation of actions and events in this example.
```

## Step 5: Define Your Workflow
With custom states, actions, and events in place, we can now define our workflow for the OrderEntity.

```csharp
public class OrderWorkflow : WorkflowBase<OrderWorkflow, OrderEntity, State>
{
    public OrderWorkflow(IEnumerable<IWorkflowOwner<OrderWorkflow>> actions) : base(actions)
    {
    }
}
```
In this example, OrderWorkflow extends WorkflowBase and is initialized with a collection of actions relevant to the OrderEntity.

## Step 6: Execute Actions
Now that we've set up the workflow, let's execute some actions on our OrderEntity. For instance, we can submit an order and deliver it:

```csharp
// Execute SubmitOrderAction
// ...

// Execute DeliverOrderAction
// ...
```

## Step 7: Error Handling
XWorkflows takes care of error handling when an action is executed without permission to transition the entity to a desired state. It will throw an exception with a meaningful error message, guiding you to resolve the issue.

## Step 8: Advanced Usage (Optional)
Once you're comfortable with the basic setup, you can explore more advanced features of XWorkflows, such as customizing the library's behavior, managing multiple workflows, or integrating events to perform post-action tasks.