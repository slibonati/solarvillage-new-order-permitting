Advanced Process Development with Red Hat Process Automation Manager 7 Homework Submission 

### The following repositories represent all components of the submission.

slibonati/solarvillage-web - mock web services for government permits  
slibonati/solarvillage-data-model - shared data model  
slibonati/solarvillage-rest-work-item-handler - custom work item handlers  
slibonati/solarvillage-new-order-permitting - red hat process automation manager project

### slibonati/solarvillage-web
mvn clean install  
java -jar target/permit-1.0.jar --context-path=electric --server.port=9090  
java -jar target/permit-1.0.jar --context-path=structural --server.port=9091  
note the arguments correspond to settings within the process

### slibonati/solarvillage-data-model
mvn clean install

### slibonati/solarvillage-rest-work-item-handler
mvn clean install

### slibonati/solarvillage-new-order-permitting

create users and groups  
from application-roles.properties  

sales=rest-query,rest-task,agent,kie-server,sales-all,user
hoa=rest-query,hoa-all,rest-task,agent,kie-server,user

Dependencies	

com.solarvillage	permit-rest-work-item-handler	1.0	
com.solarvillage	permit-datamodel	1.0	

Work Item Handlers

GetPermitRequestStatus	new com.solarvillage.permit.workitemhandler.GetPermitRequestStatusWorkItemHandler()	

SubmitPermitRequest	new com.solarvillage.permit.workitemhandler.SubmitPermitRequestWorkItemHandler()	

RescindPermit	new com.solarvillage.permit.workitemhandler.RescindPermitWorkItemHandler()

### mock data 
void com.solarvillage.permit.service.impl.PermitServiceImpl.mock(Permit permit)
in the solarvillage-web project defines mock data to drive process scenarios.

Freddy Kreuger's electrical permit is denied, but structural permit is approved.  
Hannibal Lecter's electrical permit is approved, but structural permit is denied.  
Hannibal Lecter's electrical permit is approved, but structural permit is denied.  
Jason Voorhees' electrical and structural permits are approved.  
Michael Meyers's permits are initially in progress, but 3 subsequent requests for status will flip the status to approved.  
Any other input will produce random statuses for permit requests.



