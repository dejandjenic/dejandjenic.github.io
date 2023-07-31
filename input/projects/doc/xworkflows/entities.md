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

# Entities
Entities are the core objects in XWorkflows that undergo state transitions within defined workflows. To work with entities in XWorkflows, you need to define a class that represents your entity and implement the IWorkflowStateEntity<TState> interface. This interface enables XWorkflows to manage the state transitions and actions for your entity.

## 1. Defining Your Entity Class
To create a workflow entity, define a class that represents the entity in your application. This class should contain properties and methods necessary to interact with the entity's data. For example, let's continue with our OrderEntity from the previous section:

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
In this example, the OrderEntity class implements the IWorkflowStateEntity<State> interface, which requires three methods:

* GetIdentifier: Returns the unique identifier of the entity.
* GetState: Returns the current state of the entity.
* SetState: Sets the state of the entity after a successful state transition.

## 2. IWorkflowStateEntity<TState> Interface
The IWorkflowStateEntity<TState> interface is a crucial component of XWorkflows that allows the library to interact with your entity and manage its state transitions. By implementing this interface, your entity becomes compatible with XWorkflows and gains the ability to participate in workflows.

Properties
* Identifier: A unique identifier representing the entity. It can be a string, integer, or any other data type that uniquely identifies your entity.
* State: The current state of the entity. This property is required to manage state transitions effectively.

Methods
* GetIdentifier: This method returns the unique identifier of the entity. It is used by XWorkflows to identify entities during state transitions.
* GetState: This method returns the current state of the entity. XWorkflows uses this method to determine the starting state before executing actions.
* SetState: This method is called after a successful state transition. It updates the entity's state to the new state after an action is executed.

## 3. Using Your Entity in Workflows
Once you have defined your entity class and implemented the IWorkflowStateEntity<TState> interface, you can integrate it into workflows. Workflows will manage the state transitions and actions for your entity, providing a structured way to handle its behavior throughout its lifecycle.

To use your entity in a workflow, create custom states, actions, and events that define the entity's workflow behavior. Then, define a custom workflow class that inherits from WorkflowBase<TWorkflow, TEntity, TState> and register the relevant actions for your entity.

## 4. Supported Data Types
The IWorkflowStateEntity<TState> interface is designed to be flexible and can be used with various data types for the entity's identifier and state. XWorkflows allows you to use any data type that implements equality comparison (Equals) and hash code generation (GetHashCode).