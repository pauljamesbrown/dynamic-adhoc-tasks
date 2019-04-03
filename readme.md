## Dynamic & Adhoc Task Example RHPAM Process.

**Note:** This process was developed using RHPAM 7.2

This simple example is intended to demonstrate how you can combine structure workflow (i.e sequential steps) with totally unstructure tasks i.e tasks that do not exist at design or runtime time and are created on the fly.

### Pre-Requisities:

1 - All human tasks have been assigned to the admin group
2 - Basic Authentication is required to invoke the Kie-Server api. The user must have permission to the kie-server group.

[Process Diagram Image](https://github.com/pauljamesbrown/dynamic-adhoc-tasks/blob/master/src/main/resources/com/myspace/caseadhoctasks/demo/com.myspace.caseadhoctasks.demo.DynamicAndAdhoc-image.png)

### Process Explanation.

The process is a case definition (as opposed to a standard bpm process). To create a new case invoke the create case kie-server api: 

 ```
 POST http://localhost:8080/kie-server/services/rest/server/containers/<CONTAINER-ID>/cases/<PROCESS-ID>/instances

 EXAMPLE: http://localhost:8080/kie-server/services/rest/server/containers/CaseAdhocTasks_1.0-SNAPSHOT/cases/com.myspace.caseadhoctasks.demo.DynamicAndAdhoc/instances
 ```

The rest request requires you to specify three elements:
- The kie-server container name
- The process id - This can be found in the "ID" property field in your process definition.

Once a case has been created the first milestone called "Initialised" is automatically triggered by the "Case Initialised" script. The "Initialised" milestone task has been configured to activate when the status attribute which is stored as a case variable changes to "Created".

If you are login into the business central with the correct user permissions you should also be able to view a task called "Case Assessment". If you complete the task (via the business central task inbox) the process will move along to the medical script task. At this stage the process will appear to stop. This is because the "Medical" script node has been placed into an Adhoc group and the Adhoc Autostart property has been set to false.

To activate the task you will need to invoke the tasks kie-server api
```
PUT http://localhost:8080/kie-server/services/rest/server/containers/<CONTAINER-ID>/cases/instances/<CASE_ID>/tasks/<TASK-NAME

EXAMPLE: http://localhost:8080/kie-server/services/rest/server/containers/CaseAdhocTasks_3.0-SNAPSHOT/cases/instances/CASE-0000000038/tasks/Medical
```
The rest request requires you to specify three elements:
- The kie-server container name
- The case id of the case instance where you want to activate the task
- The adhoc task name you want to activate

If you complete this task the process will automatically complete as the final script node has the Adhoc Autostart value set to true, which means it will automatically be created once the step (script node) is reached.

To create a dynamic task you will need to use the following api call

```
POST http://localhost:8080/kie-server/services/rest/server/containers/<CONTAINER-ID>/cases/instances/<CASE-ID>/tasks

EXAMPLE: http://localhost:8080/kie-server/services/rest/server/containers/CaseAdhocTasks_3.0-SNAPSHOT/cases/instances/CASE-0000000038/tasks
```

The rest request requires you to specify two elements as part of the url and also you are required to pass a payload:
- The kie-server container name
- The case id of the case instance where you want to activate the task

**Dynamic Task Payload Example:**
```{
 "name" : "New-Manager-Approval-Task",
 "data" : {
   "caseFile_status" : "no-status"
  }, 
 "description" : "Ask for manager approval again",
 "actors" : "",
  "groups" : "admin" 
}
```




