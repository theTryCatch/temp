# Workflow Engine JSON Documentation

## Overview
This document provides a detailed breakdown of the workflow engine JSON structure. Each property is thoroughly explained, covering possible values, their relationships, and how they impact the workflow’s execution. The workflow engine is designed to be flexible, allowing execution of scripts and commands in a Windows environment, with dynamic error handling, success criteria, and reserved actions for specific cases.

---

## 1. Workflow-Level Properties

### `name`
- **Description**: The name of the workflow.
- **Type**: `string`
- **Example**: `"ClearTeamsClientCache"`
- **Usage**: Provides an identifier for the workflow.

---

## 2. Step-Level Properties

Each step in the workflow contains several key properties:

### `name`
- **Description**: The name of the step.
- **Type**: `string`
- **Example**: `"CheckServiceRunning"`
- **Usage**: Describes the action being taken in this step.

### `runMode`
- **Description**: Defines how the step is executed in relation to other steps.
- **Type**: `string`
- **Possible Values**:
  - **`sequential`**: The step is executed one after another in a strict order.
  - **`parallel`** (future implementation): Allows multiple steps to run concurrently (not implemented in the current version).
- **Example**: `"sequential"`
- **Usage**: Indicates whether the step should be run in sequence or in parallel (sequential is default for now).

### `environment`
- **Description**: Defines the environment configuration in which the step runs.
- **Type**: `object`
  - **`commandType`**: Specifies the type of action or command being executed.
    - **Possible Values**:
      - **`script`**: Indicates that the step is running a script.
      - **`serviceCall`**: Indicates that the step is making a service/API call.
    - **Example**: `"script"`
  
  - **`runtime`**: The runtime environment for executing the command.
    - **Possible Values**:
      - **`PowerShell`**: Runs a PowerShell script.
      - **`Batch`**: Runs a Windows batch script.
    - **Example**: `"PowerShell"`
    
  - **`command`**: The command or script being executed.
    - **Type**: `string`
    - **Example**: `"check_service_running.ps1"`
  
  - **`params`**: A list of parameters to pass to the command or script.
    - **Type**: `object`
    - **Example**:
      ```json
      "params": {
        "serviceName": "Teams"
      }
      ```

### `outputVariable`
- **Description**: Defines the output of the script or service call that the workflow engine will evaluate.
- **Type**: `string`
- **Example**: `"isServiceRunning"`
- **Usage**: The variable that stores the output result of the step, which will be used for success or failure evaluation.

### `condition`
- **Description**: The condition that must be met for the step to be considered successful.
- **Type**: `string`
- **Example**: `"isServiceRunning -eq true"`
- **Usage**: This field checks if the output variable meets the expected condition.

---

## 3. Success and Failure Handling

### `onSuccess`
- **Description**: Defines the action to be taken if the step succeeds.
- **Type**: `object`
  - **`actionType`**: Specifies whether the next action is a workflow step or a reserved action.
    - **Possible Values**:
      - **`WorkflowStep`**: Proceeds to the next defined workflow step.
      - **`ReservedAction`**: Executes a reserved action, such as ending the workflow.
    - **Example**: `"WorkflowStep"`
  
  - **`trigger`**: Defines what happens next upon success.
    - **Type**: `string`
    - **Example**: `"StopService"`

### `onFailure`
- **Description**: Defines the action to be taken if the step fails.
- **Type**: `object`
  - **`actionType`**: Specifies whether the next action is a workflow step or a reserved action.
    - **Possible Values**:
      - **`WorkflowStep`**: Proceeds to the next defined workflow step.
      - **`ReservedAction`**: Executes a reserved action, such as aborting the workflow.
    - **Example**: `"ReservedAction"`
  
  - **`trigger`**: Defines what happens next upon failure.
    - **Type**: `string`
    - **Example**: `"AbortWorkflow"`
  
  - **`actionPayload`**: Passes information to the reserved action.
    - **Type**: `string` or `object`
    - **Example**: `"Service check failed."`
    - **Usage**: This field can pass simple strings (messages) or complex objects (future use).

### `onError`
- **Description**: Defines the action to be taken if an error occurs during the step.
- **Type**: Same structure as **`onFailure`**.

---

## 4. Reserved Actions

Reserved actions are specific predefined actions the workflow can take under certain conditions.

### `AbortWorkflow`
- **Description**: Terminates the workflow if a critical failure occurs.
- **Usage**: Triggered if a step fails or encounters an error that prevents further execution.

### `EndWorkflow`
- **Description**: Gracefully ends the workflow when all steps are completed successfully or under predefined conditions.

---

## Example Full JSON

```json
{
  "name": "ClearTeamsClientCache",
  "steps": [
    {
      "name": "CheckServiceRunning",
      "runMode": "sequential",
      "environment": {
        "commandType": "script",
        "runtime": "PowerShell",
        "command": "check_service_running.ps1",
        "params": {
          "serviceName": "Teams"
        }
      },
      "outputVariable": "isServiceRunning",
      "condition": "isServiceRunning -eq true",
      "onSuccess": {
        "actionType": "WorkflowStep",
        "trigger": "StopService"
      },
      "onFailure": {
        "actionType": "WorkflowStep",
        "trigger": "NotifyFailure"
      },
      "onError": {
        "actionType": "ReservedAction",
        "trigger": "AbortWorkflow",
        "actionPayload": "Service check failed."
      }
    },
    {
      "name": "StopService",
      "runMode": "sequential",
      "environment": {
        "commandType": "script",
        "runtime": "PowerShell",
        "command": "stop_service.ps1",
        "params": {
          "serviceName": "Teams"
        }
      },
      "outputVariable": "isServiceStopped",
      "condition": "isServiceStopped -eq true",
      "onSuccess": {
        "actionType": "WorkflowStep",
        "trigger": "ClearCache"
      },
      "onFailure": {
        "actionType": "ReservedAction",
        "trigger": "AbortWorkflow",
        "actionPayload": "Failed to stop service."
      }
    },
    {
      "name": "ClearCache",
      "runMode": "sequential",
      "environment": {
        "commandType": "script",
        "runtime": "PowerShell",
        "command": "clear_cache.ps1",
        "params": {
          "cachePath": "C:\\path\\to\\cache"
        }
      },
      "outputVariable": "cacheCleared",
      "condition": "cacheCleared -eq true",
      "onSuccess": {
        "actionType": "WorkflowStep",
        "trigger": "RestartService"
      },
      "onFailure": {
        "actionType": "ReservedAction",
        "trigger": "EndWorkflow",
        "actionPayload": "Cache clearing unsuccessful, ending workflow."
      },
      "onScriptExecutionError": {
        "actionType": "ReservedAction",
        "trigger": "AbortWorkflow",
        "actionPayload": "Failed to clear cache. Aborting workflow."
      }
    },
    {
      "name": "RestartService",
      "runMode": "sequential",
      "environment": {
        "commandType": "script",
        "runtime": "PowerShell",
        "command": "restart_service.ps1",
        "params": {
          "serviceName": "Teams"
        }
      },
      "outputVariable": "isServiceRunning",
      "condition": "isServiceRunning -eq true",
      "onSuccess": {
        "actionType": "WorkflowStep",
        "trigger": "NotifySuccess"
      },
      "onFailure": {
        "actionType": "ReservedAction",
        "trigger": "AbortWorkflow",
        "actionPayload": "Service restart failed, aborting workflow."
      }
    },
    {
      "name": "NotifySuccess",
      "runMode": "sequential",
      "environment": {
        "commandType": "serviceCall",
        "serviceEndpoint": "https://notify.example.com/success",
        "method": "POST",
        "params": {
          "message": "Service successfully restarted and cache cleared."
        }
      },
      "outputVariable": "notificationSent",
      "condition": "notificationSent -eq true",
      "onSuccess": {
        "actionType": "ReservedAction",
        "trigger": "EndWorkflow",
        "actionPayload": "Workflow completed successfully."
      },
      "onFailure": {
        "actionType": "ReservedAction",
        "trigger": "AbortWorkflow",
        "actionPayload": "Notification failed, aborting workflow."
      }
    }
  ]
}
```
## FAQs

### 1. What is the purpose of `runMode`?
- **Answer**: `runMode` defines how the step is executed—whether it runs sequentially or in parallel (parallel is a placeholder for future implementation).
- **Values**:
  - `sequential`: The step will execute one after another.
  - `parallel`: Placeholder for future implementation to allow multiple steps to run concurrently.

---

### 2. How do reserved actions differ from workflow steps?
- **Answer**: Reserved actions are predefined actions that manage the overall state of the workflow, such as terminating it (`AbortWorkflow`) or ending it gracefully (`EndWorkflow`). Unlike workflow steps, which represent actions taken by the system, reserved actions control the flow or final result of the entire workflow.

---

### 3. Can `actionPayload` accept complex objects?
- **Answer**: Yes. While currently used to pass simple strings (like messages), the `actionPayload` field is designed to accept complex objects in the future. This provides flexibility for passing more detailed information between steps or to reserved actions.

---

### 4. How does the workflow engine evaluate `condition`?
- **Answer**: The workflow engine uses the `condition` field to evaluate the outcome of each step by checking the value of the `outputVariable`. If the condition is met (e.g., `isServiceRunning -eq true`), the workflow proceeds to the `onSuccess` action. If not, it triggers the `onFailure` action.

---

### 5. What happens if a step encounters an error?
- **Answer**: If a step encounters an error (e.g., a script fails to run), the workflow engine triggers the `onError` action defined in the step. The `onError` action can be configured to either continue with another step, abort the workflow, or take a reserved action like `AbortWorkflow`.

---

### 6. What is the role of `outputVariable`?
- **Answer**: The `outputVariable` holds the output from a script or service call and is used in the `condition` field to determine whether the step succeeded. This allows the workflow engine to dynamically evaluate the result of each step before moving to the next one.

---

### 7. Can the workflow run steps in parallel?
- **Answer**: Not yet. While the `runMode` property supports sequential execution (one step after another), parallel execution is planned for future implementation.

---

### 8. What does `AbortWorkflow` do?
- **Answer**: `AbortWorkflow` is a reserved action that stops the workflow immediately if a critical error occurs or if failure conditions are met that prevent further progress.

---

### 9. How are success and failure conditions handled in the workflow?
- **Answer**: Each step has `onSuccess` and `onFailure` handlers that define what happens when a step completes successfully or fails. The `onSuccess` handler triggers the next workflow step, while `onFailure` can trigger another step, abort the workflow, or take a reserved action like `AbortWorkflow`.

---

### 10. What is the difference between `onSuccess`, `onFailure`, and `onError`?
- **Answer**:
  - **`onSuccess`**: Defines the action taken when a step completes successfully.
  - **`onFailure`**: Defines the action taken when a step fails (e.g., does not meet the success criteria).
  - **`onError`**: Defines the action taken if an error occurs while executing the step (e.g., a script failure).

### 11. What happens if there’s no output variable in a step?
- **Answer**: If there’s no `outputVariable`, the step cannot be evaluated based on a condition, which means the workflow engine will not be able to determine success or failure. Every step that requires success evaluation must have an `outputVariable` defined.

---

### 12. Can multiple reserved actions be triggered in the same step?
- **Answer**: No. Each step can only trigger one reserved action at a time, such as `AbortWorkflow` or `EndWorkflow`. However, you can chain steps to handle multiple scenarios.

---

### 13. How can I handle retry logic for steps that fail?
- **Answer**: In this workflow version, there is no built-in retry mechanism. However, you can handle retries manually by creating a loop-like structure with the `onFailure` handler, where you call the same step again. Future implementations could include a dedicated retry property.

---

### 14. What if a script runs successfully but doesn’t meet the success criteria?
- **Answer**: If a script runs successfully but the `condition` based on the `outputVariable` isn’t met (e.g., `isServiceRunning -eq false`), the workflow engine will trigger the `onFailure` handler for that step.

---

### 15. Can I pass data between steps?
- **Answer**: Yes, the `outputVariable` from one step can be referenced in another step's parameters or conditions, allowing you to pass data between steps.

---

### 16. Is it possible to have conditional branching in the workflow?
- **Answer**: Conditional branching can be achieved by using the `onSuccess` and `onFailure` properties to determine the next step. You can define different triggers based on the result of the current step.

---

### 17. Can the same step be reused multiple times in different conditions?
- **Answer**: Yes. A step can be referenced multiple times by defining it in different `onSuccess` or `onFailure` handlers. This allows you to reuse a step based on different conditions.

---

### 18. What is the difference between `onScriptExecutionError` and `onFailure`?
- **Answer**: 
  - **`onScriptExecutionError`**: This handler is triggered when there’s an error in running the script itself, such as syntax errors, missing files, or runtime exceptions.
  - **`onFailure`**: This handler is triggered when the script runs successfully, but the result doesn’t meet the defined success criteria.

---

### 19. How can I implement custom logic in success criteria?
- **Answer**: The `condition` field allows you to define custom logic using output variables. For example, you can check multiple conditions like:
  ```json
  "condition": "isServiceRunning -eq true -and cacheCleared -eq true"
### 20. How do I specify dependencies between steps?
- **Answer**: Dependencies between steps are automatically handled through `onSuccess` and `onFailure`. By specifying what the next step is upon success or failure, you implicitly define the dependencies. This allows the workflow to follow a specific path based on conditions or errors without manual intervention.

---

### 21. What does `serviceCall` mean in `commandType`?
- **Answer**: `serviceCall` indicates that instead of running a script, the workflow step makes an HTTP request to an external service or API. This is useful for interacting with REST APIs, sending notifications, or calling external services in your workflow. It includes the `serviceEndpoint`, `method` (e.g., POST or GET), and `params` to define the HTTP request.

---

### 22. Can I skip a step?
- **Answer**: To skip a step, you can handle it in the `onSuccess` or `onFailure` handler by triggering a different step, effectively bypassing the current one. Skipping can be conditional by checking an output or predefined criteria, ensuring certain steps are only executed if necessary.

---

### 23. What happens if I don’t define an `onSuccess` or `onFailure` action?
- **Answer**: If neither `onSuccess` nor `onFailure` is defined, the workflow engine will not know what to do after the step completes, and the workflow will stop at that step. It is essential to define at least one handler to ensure the workflow progresses. This also applies to error handling—if there’s no `onError`, the workflow will halt on encountering an error.

---

### 24. How are PowerShell scripts passed parameters in this workflow engine?
- **Answer**: Parameters are passed to PowerShell scripts using the `params` object. The key-value pairs in the `params` object correspond to the parameters expected by the script. For example, if the script expects a `serviceName` parameter, it can be passed as follows:
  ```json
  "params": {
    "serviceName": "Teams"
  }

### 25. What kind of data can be passed in `actionPayload` for reserved actions?
- **Answer**: While `actionPayload` is currently used for passing simple strings (such as messages), it is designed to support more complex objects in the future. You can pass strings, numbers, or structured data like JSON objects or arrays, depending on the action’s requirements. For example:
  ```json
  "actionPayload": {
    "message": "Service successfully restarted.",
    "details": {
      "timestamp": "2024-10-09T12:34:56Z",
      "status": "Completed"
    }
  }

## Scenario 1: Simple Service Check and Cache Clearing
- **Description**: The workflow checks if a service (e.g., Teams) is running. If the service is running, it proceeds to stop the service, clear the cache, and restart the service.
- **Steps**:
  1. **CheckServiceRunning**: Checks if the Teams service is running.
  2. **StopService**: Stops the service if it’s running.
  3. **ClearCache**: Clears the service cache.
  4. **RestartService**: Restarts the service.

```json
{
  "name": "ServiceCheckAndCacheClear",
  "steps": [
    {
      "name": "CheckServiceRunning",
      "runMode": "sequential",
      "environment": {
        "commandType": "script",
        "runtime": "PowerShell",
        "command": "check_service_running.ps1",
        "params": {
          "serviceName": "Teams"
        }
      },
      "outputVariable": "isServiceRunning",
      "condition": "isServiceRunning -eq true",
      "onSuccess": {
        "actionType": "WorkflowStep",
        "trigger": "StopService"
      },
      "onFailure": {
        "actionType": "ReservedAction",
        "trigger": "AbortWorkflow",
        "actionPayload": "Service is not running, aborting workflow."
      }
    },
    {
      "name": "StopService",
      "runMode": "sequential",
      "environment": {
        "commandType": "script",
        "runtime": "PowerShell",
        "command": "stop_service.ps1",
        "params": {
          "serviceName": "Teams"
        }
      },
      "outputVariable": "isServiceStopped",
      "condition": "isServiceStopped -eq true",
      "onSuccess": {
        "actionType": "WorkflowStep",
        "trigger": "ClearCache"
      },
      "onFailure": {
        "actionType": "ReservedAction",
        "trigger": "AbortWorkflow",
        "actionPayload": "Failed to stop service."
      }
    },
    {
      "name": "ClearCache",
      "runMode": "sequential",
      "environment": {
        "commandType": "script",
        "runtime": "PowerShell",
        "command": "clear_cache.ps1",
        "params": {
          "cachePath": "C:\\path\\to\\cache"
        }
      },
      "outputVariable": "cacheCleared",
      "condition": "cacheCleared -eq true",
      "onSuccess": {
        "actionType": "WorkflowStep",
        "trigger": "RestartService"
      },
      "onFailure": {
        "actionType": "ReservedAction",
        "trigger": "EndWorkflow",
        "actionPayload": "Failed to clear cache, ending workflow."
      }
    },
    {
      "name": "RestartService",
      "runMode": "sequential",
      "environment": {
        "commandType": "script",
        "runtime": "PowerShell",
        "command": "restart_service.ps1",
        "params": {
          "serviceName": "Teams"
        }
      },
      "outputVariable": "isServiceRunning",
      "condition": "isServiceRunning -eq true",
      "onSuccess": {
        "actionType": "ReservedAction",
        "trigger": "EndWorkflow",
        "actionPayload": "Service restarted successfully."
      },
      "onFailure": {
        "actionType": "ReservedAction",
        "trigger": "AbortWorkflow",
        "actionPayload": "Failed to restart service, aborting workflow."
      }
    }
  ]
}
```
## Scenario 2: Conditional Branching Based on Disk Space
- **Description**: The workflow checks available disk space and either proceeds with file cleanup if disk space is low, or skips the cleanup if disk space is sufficient.
- **Steps**:
  1. **CheckDiskSpace**: Checks available disk space.
  2. **CleanupFiles**: Only runs if disk space is below a threshold.

```json
{
  "name": "DiskSpaceMonitor",
  "steps": [
    {
      "name": "CheckDiskSpace",
      "runMode": "sequential",
      "environment": {
        "commandType": "script",
        "runtime": "PowerShell",
        "command": "check_disk_space.ps1",
        "params": {
          "diskPath": "C:"
        }
      },
      "outputVariable": "availableSpace",
      "condition": "availableSpace -lt 1024",
      "onSuccess": {
        "actionType": "WorkflowStep",
        "trigger": "CleanupFiles"
      },
      "onFailure": {
        "actionType": "ReservedAction",
        "trigger": "EndWorkflow",
        "actionPayload": "Sufficient disk space, no cleanup required."
      }
    },
    {
      "name": "CleanupFiles",
      "runMode": "sequential",
      "environment": {
        "commandType": "script",
        "runtime": "PowerShell",
        "command": "cleanup_files.ps1",
        "params": {
          "cleanupPath": "C:\\Temp"
        }
      },
      "outputVariable": "cleanupSuccess",
      "condition": "cleanupSuccess -eq true",
      "onSuccess": {
        "actionType": "ReservedAction",
        "trigger": "EndWorkflow",
        "actionPayload": "Files cleaned up successfully."
      },
      "onFailure": {
        "actionType": "ReservedAction",
        "trigger": "AbortWorkflow",
        "actionPayload": "File cleanup failed, aborting workflow."
      }
    }
  ]
}
```
## Scenario 3: HTTP API Call for Notification
- **Description**: The workflow performs an HTTP call to send a notification to a service. The workflow ends based on the success of the notification.
- **Steps**:
  1. **SendNotification**: Sends an HTTP request to notify a service.

```json
{
  "name": "SendNotification",
  "steps": [
    {
      "name": "SendNotification",
      "runMode": "sequential",
      "environment": {
        "commandType": "serviceCall",
        "serviceEndpoint": "https://api.notifyservice.com/send",
        "method": "POST",
        "params": {
          "message": "Task completed successfully.",
          "user": "admin@example.com"
        }
      },
      "outputVariable": "notificationSent",
      "condition": "notificationSent -eq true",
      "onSuccess": {
        "actionType": "ReservedAction",
        "trigger": "EndWorkflow",
        "actionPayload": "Notification sent successfully."
      },
      "onFailure": {
        "actionType": "ReservedAction",
        "trigger": "AbortWorkflow",
        "actionPayload": "Failed to send notification, aborting workflow."
      }
    }
  ]
}
```
## Scenario 4: Restart Service Only If Error Occurs
- **Description**: This workflow checks if an error occurred in a service. If the service is down, it restarts it.
- **Steps**:
  1. **CheckServiceStatus**: Checks if the service is running.
  2. **RestartService**: Restarts the service only if it’s down.

```json
{
  "name": "ServiceStatusCheckAndRestart",
  "steps": [
    {
      "name": "CheckServiceStatus",
      "runMode": "sequential",
      "environment": {
        "commandType": "script",
        "runtime": "PowerShell",
        "command": "check_service_status.ps1",
        "params": {
          "serviceName": "MyService"
        }
      },
      "outputVariable": "isServiceRunning",
      "condition": "isServiceRunning -eq false",
      "onSuccess": {
        "actionType": "WorkflowStep",
        "trigger": "RestartService"
      },
      "onFailure": {
        "actionType": "ReservedAction",
        "trigger": "EndWorkflow",
        "actionPayload": "Service is running, no need to restart."
      }
    },
    {
      "name": "RestartService",
      "runMode": "sequential",
      "environment": {
        "commandType": "script",
        "runtime": "PowerShell",
        "command": "restart_service.ps1",
        "params": {
          "serviceName": "MyService"
        }
      },
      "outputVariable": "isServiceRunning",
      "condition": "isServiceRunning -eq true",
      "onSuccess": {
        "actionType": "ReservedAction",
        "trigger": "EndWorkflow",
        "actionPayload": "Service restarted successfully."
      },
      "onFailure": {
        "actionType": "ReservedAction",
        "trigger": "AbortWorkflow",
        "actionPayload": "Failed to restart service, aborting workflow."
      }
    }
  ]
}
```
## Scenario 5: Multiple Steps with Error Handling
- **Description**: The workflow performs multiple steps and includes robust error handling. If any step encounters an error, it triggers an error action.
- **Steps**:
  1. **Step 1: Perform Task A**
  2. **Step 2: Perform Task B**
  3. **Error Handling**: If any task fails, handle the error gracefully.

```json
{
  "name": "MultiStepWorkflowWithErrorHandling",
  "steps": [
    {
      "name": "TaskA",
      "runMode": "sequential",
      "environment": {
        "commandType": "script",
        "runtime": "PowerShell",
        "command": "task_a.ps1"
      },
      "outputVariable": "taskACompleted",
      "condition": "taskACompleted -eq true",
      "onSuccess": {
        "actionType": "WorkflowStep",
        "trigger": "TaskB"
      },
      "onFailure": {
        "actionType": "ReservedAction",
        "trigger": "AbortWorkflow",
        "actionPayload": "Task A failed, aborting workflow."
      }
    },
    {
      "name": "TaskB",
      "runMode": "sequential",
      "environment": {
        "commandType": "script",
        "runtime": "PowerShell",
        "command": "task_b.ps1"
      },
      "outputVariable": "taskBCompleted",
      "condition": "taskBCompleted -eq true",
      "onSuccess": {
        "actionType": "ReservedAction",
        "trigger": "EndWorkflow",
        "actionPayload": "Workflow completed successfully."
      },
      "onFailure": {
        "actionType": "ReservedAction",
        "trigger": "AbortWorkflow",
        "actionPayload": "Task B failed, aborting workflow."
      }
    }
  ]
}
```
-------------------------------------------------------------------------------------------------
# Workflow Documentation Table

| **Name**                | **Description**                                                                                      | **Datatype**         | **Values**                                                      | **Example**                                                     |
|-------------------------|------------------------------------------------------------------------------------------------------|----------------------|-----------------------------------------------------------------|-----------------------------------------------------------------|
| `name`                  | The name of the workflow or step.                                                                    | `string`             | Any valid string                                                 | `"ClearTeamsClientCache"`                                        |
| `workflowOwningGroup`    | The group responsible for managing this workflow.                                                    | `string`             | Any valid string                                                 | `"ITSupport"`                                                   |
| `emailAddress`          | Contact email for notifications or support.                                                          | `string`             | Valid email addresses                                            | `"support@example.com"`                                         |
| `globals`               | Global variables used throughout the workflow.                                                       | `object`             | Any valid key-value pair                                         | `{ "teamsAppDataPath": "%APPDATA%\\Microsoft\\Teams" }`         |
| `description`           | A brief description of the workflow or step.                                                         | `string`             | Any valid string                                                 | `"A workflow to clear the Microsoft Teams client cache."`       |
| `steps`                 | A list of steps that comprise the workflow.                                                          | `array of objects`    | Each step is an object with its own properties (see below)       | See steps example in the full JSON above                        |
| `executionType`         | Defines whether the step should be executed sequentially or in parallel.                             | `string`             | `"sequential"`, `"parallel"`                                     | `"sequential"`                                                  |
| `environment.commandType`| The type of execution environment for this step.                                                     | `string`             | `"script"`, `"serviceCall"`, etc.                                | `"script"`                                                      |
| `environment.runtime`    | Specifies the runtime environment used to execute the command.                                      | `string`             | `"PowerShell"`, `"Bash"`, `"Python"`                             | `"PowerShell"`                                                  |
| `environment.command`    | The command or script to be executed in the step.                                                   | `string`             | Any valid script or command                                      | `"clear_cache.ps1"`                                             |
| `environment.params`     | Parameters passed to the command or script.                                                         | `object`             | Key-value pairs representing the parameters                      | `{ "serviceName": "Teams.exe" }`                                |
| `outputVariable`         | The name of the variable that holds the result of the command.                                       | `string`             | Any valid string                                                 | `"isServiceRunning"`                                            |
| `successCriteria`        | The condition used to evaluate whether the step was successful.                                      | `string`             | Any valid condition or expression                                | `"isServiceRunning -eq true"`                                   |
| `timeout`               | Time in seconds after which the step should be considered as timed out.                              | `number`             | Positive integer values                                          | `60`                                                            |
| `onSuccessSequential`    | Defines the action to take if the step is successful (sequential execution).                         | `object`             | `"WorkflowStep"`, `"ReservedAction"`                             | `{ "actionType": "WorkflowStep", "trigger": "ClearCache" }`     |
| `onUnsuccessSequential`  | Defines the action to take if the step fails (sequential execution).                                 | `object`             | `"WorkflowStep"`, `"ReservedAction"`                             | `{ "actionType": "ReservedAction", "trigger": "AbortWorkflow" }`|
| `onError`               | Defines the action to take if an error occurs during execution.                                      | `object`             | `"WorkflowStep"`, `"ReservedAction"`                             | `{ "actionType": "ReservedAction", "trigger": "AbortWorkflow" }`|
| `onTimeout`             | Defines the action to take if the step times out.                                                    | `object`             | `"WorkflowStep"`, `"ReservedAction"`                             | `{ "actionType": "ReservedAction", "trigger": "AbortWorkflow" }`|
| `inputValue`            | Custom input for reserved actions, such as a message or complex object.                              | `string`/`object`     | Any string or complex object                                     | `"Service check timed out, aborting workflow."`                 |
| `wikiLink`              | Link to documentation or wiki page that explains the step.                                           | `string`             | Valid URLs                                                       | `"https://wiki.example.com/check_service_running"`              |
| `versionRange.lowestVersion` | Minimum version of the workflow in which this step can be used.                                   | `string`             | Valid version strings                                            | `"1.0.0"`                                                       |
| `versionRange.highestVersion` | Maximum version of the workflow in which this step can be used.                                   | `string`             | Valid version strings                                            | `"3.0.0"`                                                       |

### Example Explanation:

- **`name`**: For steps, this can be something like `"CheckServiceRunning"`. For the workflow, it can be `"ClearTeamsClientCache"`.
- **`description`**: Describes the purpose of each step, such as `"Check if Microsoft Teams is running."`.
- **`executionType`**: Determines if steps are executed one after the other (`"sequential"`) or at the same time (`"parallel"`).
- **`timeout`**: Specifies the maximum allowed time for each step in seconds, e.g., `60`.


{
  "name": "ClearTeamsClientCache",
  "workflowOwningGroup": "ITSupport",
  "emailAddress": "support@example.com",
  "globals": {
    "teamsAppDataPath": "%APPDATA%\\Microsoft\\Teams",
    "teamsProcessName": "Teams.exe",
    "pathToTeamsExe": "%LOCALAPPDATA%\\Microsoft\\Teams\\Update.exe --processStart Teams.exe",
    "maxRetryAttempts": 2,
    "retryDelaySeconds": 5
  },
  "description": "A workflow to clear the Microsoft Teams client cache in multiple steps.",
  "steps": [
    {
      "name": "CheckServiceRunning",
      "description": "Check if Microsoft Teams is running.",
      "executionType": "sequential",
      "environment": {
        "commandType": "script",
        "runtime": "PowerShell",
        "command": "check_service_running.ps1",
        "params": {
          "serviceName": "{{globals.teamsProcessName}}"
        }
      },
      "outputVariable": "isServiceRunning",
      "successCriteria": "isServiceRunning -eq true",
      "timeout": 60,
      "onSuccessSequential": {
        "actionType": "WorkflowStep",
        "trigger": "StopService"
      },
      "onUnsuccessSequential": {
        "actionType": "WorkflowStep",
        "trigger": "NotifyFailure"
      },
      "onError": {
        "actionType": "ReservedAction",
        "trigger": "AbortWorkflow",
        "inputValue": "Service check failed."
      },
      "onTimeout": {
        "actionType": "ReservedAction",
        "trigger": "AbortWorkflow",
        "inputValue": "Service check timed out, aborting workflow."
      },
      "wikiLink": "https://wiki.example.com/check_service_running",
      "versionRange": {
        "lowestVersion": "1.0.0",
        "highestVersion": "3.0.0"
      }
    },
    {
      "name": "StopService",
      "description": "Stop the Microsoft Teams service.",
      "executionType": "sequential",
      "environment": {
        "commandType": "script",
        "runtime": "Bash",
        "command": "stop_service.sh",
        "params": {
          "serviceName": "{{globals.teamsProcessName}}"
        }
      },
      "outputVariable": "isServiceStopped",
      "successCriteria": "isServiceStopped -eq true",
      "timeout": 120,
      "onSuccessSequential": {
        "actionType": "WorkflowStep",
        "trigger": "ClearCache"
      },
      "onUnsuccessSequential": {
        "actionType": "ReservedAction",
        "trigger": "AbortWorkflow",
        "inputValue": "Failed to stop service."
      },
      "onTimeout": {
        "actionType": "ReservedAction",
        "trigger": "AbortWorkflow",
        "inputValue": "Service stop timed out, aborting workflow."
      },
      "wikiLink": "https://wiki.example.com/stop_service",
      "versionRange": {
        "lowestVersion": "1.0.0",
        "highestVersion": "3.0.0"
      }
    },
    {
      "name": "ClearCache",
      "description": "Clear the Teams cache by deleting specific cache folders and files.",
      "executionType": "sequential",
      "environment": {
        "commandType": "script",
        "runtime": "PowerShell",
        "command": "clear_cache.ps1",
        "params": {
          "cachePath": "{{globals.teamsAppDataPath}}"
        }
      },
      "outputVariable": "cacheCleared",
      "successCriteria": "cacheCleared -eq true",
      "timeout": 180,
      "onSuccessSequential": {
        "actionType": "WorkflowStep",
        "trigger": "RestartService"
      },
      "onUnsuccessSequential": {
        "actionType": "ReservedAction",
        "trigger": "EndWorkflow",
        "inputValue": "Cache clearing unsuccessful, ending workflow."
      },
      "onScriptExecutionError": {
        "actionType": "ReservedAction",
        "trigger": "AbortWorkflow",
        "inputValue": "Failed to clear Teams cache. Aborting workflow."
      },
      "onTimeout": {
        "actionType": "ReservedAction",
        "trigger": "AbortWorkflow",
        "inputValue": "Cache clearing timed out, aborting workflow."
      },
      "wikiLink": "https://wiki.example.com/clear_cache",
      "versionRange": {
        "lowestVersion": "1.0.0",
        "highestVersion": "3.0.0"
      }
    },
    {
      "name": "RestartService",
      "description": "Restart Microsoft Teams after clearing the cache.",
      "executionType": "sequential",
      "environment": {
        "commandType": "script",
        "runtime": "Python",
        "command": "restart_service.py",
        "params": {
          "serviceName": "{{globals.teamsProcessName}}",
          "pathToTeams": "{{globals.pathToTeamsExe}}"
        }
      },
      "outputVariable": "isServiceRunning",
      "successCriteria": "isServiceRunning -eq true",
      "timeout": 120,
      "onSuccessSequential": {
        "actionType": "WorkflowStep",
        "trigger": "NotifySuccess"
      },
      "onUnsuccessSequential": {
        "actionType": "ReservedAction",
        "trigger": "AbortWorkflow",
        "inputValue": "Service restart failed, aborting workflow."
      },
      "onTimeout": {
        "actionType": "ReservedAction",
        "trigger": "AbortWorkflow",
        "inputValue": "Service restart timed out, aborting workflow."
      },
      "wikiLink": "https://wiki.example.com/restart_service",
      "versionRange": {
        "lowestVersion": "1.0.0",
        "highestVersion": "3.0.0"
      }
    },
    {
      "name": "NotifySuccess",
      "description": "Notify the user that the operation has been completed successfully.",
      "executionType": "sequential",
      "environment": {
        "commandType": "serviceCall",
        "serviceEndpoint": "https://notify.example.com/success",
        "method": "POST",
        "params": {
          "message": "Microsoft Teams cache has been cleared successfully and Teams has been restarted."
        }
      },
      "outputVariable": "notificationSent",
      "successCriteria": "notificationSent -eq true",
      "timeout": 60,
      "onSuccessSequential": {
        "actionType": "ReservedAction",
        "trigger": "EndWorkflow",
        "inputValue": "Workflow completed successfully."
      },
      "onUnsuccessSequential": {
        "actionType": "ReservedAction",
        "trigger": "AbortWorkflow",
        "inputValue": "Notification failed, aborting workflow."
      },
      "onTimeout": {
        "actionType": "ReservedAction",
        "trigger": "AbortWorkflow",
        "inputValue": "Notification process timed out, aborting workflow."
      },
      "wikiLink": "https://wiki.example.com/notify_success",
      "versionRange": {
        "lowestVersion": "1.0.0",
        "highestVersion": "3.0.0"
      }
    }
  ]
}

