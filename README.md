# AzureML pipeline use and publish as an Endpoint

This is an end-to-end machine learning project on Microsoft Azure ML. Using the Bank Marketing Dataset, A Machine Learning model is trained, leveraging the AutoML feature of Azure. The model is deployed into production using Azure Container Instance (ACI) and consumed it using REST endpoints. An azureML pipeline is created, published and consumed as well.

## Architectural Diagram

Now this whole process has lot of component from creating and using a Service Principal, enabling the authentication, to using swagger for the API documentation to troubleshooting using the logs. <br>
We will discuss each step in detail, but the overall general idea of this project is listed in the diagram below. <br>

![Diagram of the project](./scrnshts/diagram.png)


The central idea is an pipeline can be publised as an REST endpoint which can be triggered by authenticated user. <br>
Let's consider a scenario where we need to retrain our trained model, whenever new data is available. A process can automatically be trigger when new data is gathered in data store, and re-train the model with the help of an API call. No mannual intervention is requred for the whole training. <br>

## Key Steps

**Step 1** *creating a service principal*<br>
A service pricipal is one of the authentication method for azure [details](https://docs.microsoft.com/en-us/azure/active-directory/develop/app-objects-and-service-principals). A service principal can be given rbac access and the access can expire over a periord of time. Here we have created a Service Principal and given it access of an owner of the ML workspace.

![Creation of Service principal](./scrnshts/SP_Creation.JPG)

The command to share the workspace to the service principal with a role of owner.
![Addigning role to service principal](./scrnshts/workspace_share.JPG)

**Step 2** *Completing the experiment*<br>
The first step of machine learning is gathering of Data, in our case we have downloaded the data from a link and registed the Dataset. <br>
![Registered Dataset](./scrnshts/Dataset.JPG)
<br>
The experiment is run on AML Compute and it is completed after sometime.
![Experiment Completion](./scrnshts/experiment_finished.JPG)
<br>
The best modcel came out as an Voting ensemble model.
![The AutoML Model](./scrnshts/Model.JPG)
<br>
**Step 3** *Deploying the best model*<br>
We choose the best model for deployment and enable "Authentication" while deploying the model using Azure Container Instance (ACI). The executed code in logs.py enables Application Insights. "Application Insights enabled" was disabled before executing logs.py.
![Logs enabled](./scrnshts/insights_enabled.JPG)
<br>
**Step 4** *Swagger Documentation*<br>
The Swagger documentation is fone of the most useful thing in the whole set up it gives Input and Output format of the deployed REST api. The swagger.json file is downloaded and swagger.py is run on a local host.
![swagger scrnsht](./scrnshts/swagger.png)
<br>
**Step 5** *Consuming the model endpoint* <br>
As we can see the exact post and response format from swagger, it is tome that we call that endpoint in same format to get result out of it. We have triggered endpoint.py with 2 records and got the results for those 2.<br>
![swagger scrnsht](./scrnshts/endpoint.JPG)
<br>
**Step 6** *Benchmarking the Above endpoint* <br>
We can check how fast our deployed service is using Azure Benchmark. The responsetime is more than suffficient for this simple model.
![Benchmark scrnsht](./scrnshts/benchmark.JPG)
<br>
**Step 7** *Creating publishing and conduming the Pipeline* <br>
The notebook has been uploaded and ran and here are the screenshots below.
![Benchmark scrnsht](./scrnshts/pipeline.JPG)
**The Pipeline endpoint with in active status**
![Active pipeline endpoint](./scrnshts/PipelineEP.JPG)
![Active Pipelinerun](./scrnshts/Pipeline3.JPG)



## Screen Recording
[Youtoube Screencast](https://youtu.be/z5YAJNxYWqc)

## Standout Suggestions
The suggestion would be we are only putting an AutoML step in the whole Pipeline. THe AutoML step uses a compute target which is created beforehand. Now if we trigger the pipeline it will succeed only if the Compute instance is running.<br>
<br>
Instead if we add another step in pipeline to create a Compute instance and then use than instance for AutoML then I think the whole Pipeline would have been more roboust.
