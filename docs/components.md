# Compoments

## Runner

Runner object defines the following:
* Git project repository, branch and access password/token if repository is private.
* Container image with all libraries, packages and dependencies, needed to run application(s) in the repository.

## Task

Task is a child object of Runner and defines the following:
* Command to execute application or script from associated by Runner repository at the time of starting k8s job container.
* Secrets are optionally provided, which are stored as kubernetes secrets and mapped to k8s job containers at the time of execution as files in /etc/secret/ directory or as environmental vars.
* Schema in OpenAPI specification is optionally provided, which allows Kriten to validate input parameters against schema and deny job creation if those are not matching the schema.

Multiple tasks can be associated with the same Runner. It allows exposing same script or application with different parameters as separate tasks, i.e. one allowing only perform read kind operation and another to do write operation and to control via RBAC, which users are allowed to execute jobs against those tasks.

Kriten supports two modes for tasks: synchronous and asynchronous, controlled by parameter “synchronous”: “true|false”. In synchronous mode, Kriten will wait a period of time for a job completion (default is 20 seconds after start of the job container) and will return result back. In asynchronous mode, on successful submission of a job, Kriten will return job id, which then will need to be queried to get result of that job.

Kriten can return custom data in a result of a job by capturing json string printed as stdout output by application/script delimited by "^JSON”.

## Job

Jobs are triggered against defined Tasks. Permission is controlled via RBAC.

Executor of a Job may need to supply Input parameters as it may be required by application or script at the time of execution as optionally defined in schema. Those Input parameters are provided in the body of REST API request and mapped to k8s job containers as Environmental var 'EXTRA_VARS' as a json string.
