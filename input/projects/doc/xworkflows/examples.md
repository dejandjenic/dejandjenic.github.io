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

# Examples
In this section, we'll explore practical examples of using the XWorkflows library to manage workflows for different entities. Each example will demonstrate how to define custom actions, events, states, and workflows to achieve specific workflow behavior.

## 1. Order Management Workflow
In this example, we'll create a workflow to manage the lifecycle of an order entity with states such as "Created," "Submitted," "Delivered," and "Canceled." We'll define actions for submitting, canceling, and delivering an order, along with events for logging the actions.

```csharp
// Define custom actions, events, and states for Order workflow
// ...

// Create the custom OrderWorkflow
var orderWorkflow = new OrderWorkflow(actions);

// Create an instance of the OrderEntity
var order = new OrderEntity { /* Initialize properties */ };

// Execute actions within the workflow
var submitRequest = new SubmitOrderActionRequest { /* Request data, if needed */ };
var (submitResult, submitResponse) = await orderWorkflow.ExecuteAction(order, submitRequest);

var cancelRequest = new CancelOrderActionRequest { /* Request data, if needed */ };
var (cancelResult, cancelResponse) = await orderWorkflow.ExecuteAction(order, cancelRequest);

var deliverRequest = new DeliverOrderActionRequest { /* Request data, if needed */ };
var (deliverResult, deliverResponse) = await orderWorkflow.ExecuteAction(order, deliverRequest);
```
## 2. Task Management Workflow
In this example, we'll create a workflow to manage the lifecycle of a task entity with states such as "Open," "In Progress," "Completed," and "Canceled." We'll define actions for updating the task status, marking it as completed, and canceling it.

```csharp
// Define custom actions, events, and states for Task workflow
// ...

// Create the custom TaskWorkflow
var taskWorkflow = new TaskWorkflow(actions);

// Create an instance of the TaskEntity
var task = new TaskEntity { /* Initialize properties */ };

// Execute actions within the workflow
var updateStatusRequest = new UpdateTaskStatusActionRequest { /* Request data, if needed */ };
var (updateStatusResult, updateStatusResponse) = await taskWorkflow.ExecuteAction(task, updateStatusRequest);

var completeRequest = new CompleteTaskActionRequest { /* Request data, if needed */ };
var (completeResult, completeResponse) = await taskWorkflow.ExecuteAction(task, completeRequest);

var cancelRequest = new CancelTaskActionRequest { /* Request data, if needed */ };
var (cancelResult, cancelResponse) = await taskWorkflow.ExecuteAction(task, cancelRequest);
```
## 3. Approval Workflow
In this example, we'll create a workflow to manage the approval process for a document entity with states such as "Draft," "Pending Approval," "Approved," and "Rejected." We'll define actions for submitting the document for approval, approving it, and rejecting it, along with events for notifying users.

```csharp
// Define custom actions, events, and states for Approval workflow
// ...

// Create the custom ApprovalWorkflow
var approvalWorkflow = new ApprovalWorkflow(actions);

// Create an instance of the DocumentEntity
var document = new DocumentEntity { /* Initialize properties */ };

// Execute actions within the workflow
var submitRequest = new SubmitForApprovalActionRequest { /* Request data, if needed */ };
var (submitResult, submitResponse) = await approvalWorkflow.ExecuteAction(document, submitRequest);

var approveRequest = new ApproveDocumentActionRequest { /* Request data, if needed */ };
var (approveResult, approveResponse) = await approvalWorkflow.ExecuteAction(document, approveRequest);

var rejectRequest = new RejectDocumentActionRequest { /* Request data, if needed */ };
var (rejectResult, rejectResponse) = await approvalWorkflow.ExecuteAction(document, rejectRequest);
```