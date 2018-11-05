### Advanced Process Development with Red Hat Process Automation Manager 7 Homework Submission 

#### The following repositories represent all components of the submission.

slibonati/solarvillage-web - mock web services for government permits  
slibonati/solarvillage-data-model - shared data model  
slibonati/solarvillage-rest-work-item-handler - custom work item handlers  
slibonati/solarvillage-new-order-permitting - red hat process automation manager project

#### slibonati/solarvillage-web
mvn clean install  
java -jar target/permit-1.0.jar --context-path=electric --server.port=9090  
java -jar target/permit-1.0.jar --context-path=structural --server.port=9091  
*note the arguments correspond to settings within the process

#### slibonati/solarvillage-data-model
mvn clean install

#### slibonati/solarvillage-rest-work-item-handler
mvn clean install

#### slibonati/solarvillage-new-order-permitting

##### create users and groups  
from application-roles.properties  

sales=rest-query,rest-task,agent,kie-server,sales-all,user  
hoa=rest-query,hoa-all,rest-task,agent,kie-server,user

##### Dependencies	

com.solarvillage	permit-rest-work-item-handler	1.0	
com.solarvillage	permit-datamodel	1.0	

##### Work Item Handlers

GetPermitRequestStatus	new com.solarvillage.permit.workitemhandler.GetPermitRequestStatusWorkItemHandler()	

SubmitPermitRequest	new com.solarvillage.permit.workitemhandler.SubmitPermitRequestWorkItemHandler()	

RescindPermit	new com.solarvillage.permit.workitemhandler.RescindPermitWorkItemHandler()

#### mock data 
void com.solarvillage.permit.service.impl.PermitServiceImpl.mock(Permit permit)
in the solarvillage-web project defines mock data to drive process scenarios.

Freddy Kreuger's electrical permit is denied, but structural permit is approved.  
Hannibal Lecter's electrical permit is approved, but structural permit is denied.    
Jason Voorhees' electrical and structural permits are approved.  
Michael Meyers's permits are initially in progress, but 3 subsequent requests for status will flip the status to approved.  
Any other input will produce random statuses for permit requests.

#### KIE Execution Server REST API

create container  
curl -X PUT -H "Accept:application/json" -H "Content-Type:application/json" --user jboss:jboss123 \
-d '{"release-id":{"group-id":"com.solar-village","artifact-id":"new-order-permitting","version":"4.20"}}' \
"http://localhost:8080/kie-server/services/rest/server/containers/new-order-permitting_4.20"

get containers  
curl -X GET "http://localhost:8080/kie-server/services/rest/server/containers" -H "accept: application/json" --user jboss:jboss123

create process instance  
curl -X POST -H "Accept: application/json" -H "Content-Type: application/json" --user jboss:jboss123 \
-d '{"newOrderFirstName":"Freddy", "newOrderLastName":"Kreuger", "newOrderEmail":"freddy@nightmare.com", "newOrderProjectDescription":"Elm St. solar project"}' \
http://localhost:8080/kie-server/services/rest/server/containers/new-order-permitting_4.20/processes/new-order-permitting.new-order-permitting/instances

get tasks by group name  
curl -X GET -H "Accept: application/json" --user jboss:jboss123 "http://localhost:8080/kie-server/services/rest/server/queries/tasks/instances/pot-owners?groups=sales-all"

claim task  
curl -X PUT -H "Accept: application/json" --user sales:sales123 "http://localhost:8080/kie-server/services/rest/server/containers/new-order-permitting_4.20/tasks/103/states/claimed"

list claimed tasks  
curl -X GET -H "Accept: application/json" --user sales:sales123 "http://localhost:8080/kie-server/services/rest/server/queries/tasks/instances/owners"

start tasks  
curl -X PUT -H "Accept: application/json" --user sales:sales123 "http://localhost:8080/kie-server/services/rest/server/containers/new-order-permitting_4.20/tasks/103/states/started"

complete claimed task  
curl -X PUT "http://localhost:8080/kie-server/services/rest/server/containers/new-order-permitting_4.20/tasks/103/states/completed" -H "accept: application/json" --user sales:sales123 -H "content-type: application/json" -d "{\"outHoaDate\":\"11/24/2018\"}"

get tasks by group name  
curl -X GET -H "Accept: application/json" --user jboss:jboss123 "http://localhost:8080/kie-server/services/rest/server/queries/tasks/instances/pot-owners?groups=hoa-all"

claim task  
curl -X PUT -H "Accept: application/json" --user hoa:hoa123 "http://localhost:8080/kie-server/services/rest/server/containers/new-order-permitting_4.20/tasks/104/states/claimed"

start tasks  
curl -X PUT -H "Accept: application/json" --user hoa:hoa123 "http://localhost:8080/kie-server/services/rest/server/containers/new-order-permitting_4.20/tasks/104/states/started"

complete claimed task  
curl -X PUT "http://localhost:8080/kie-server/services/rest/server/containers/new-order-permitting_4.20/tasks/104/states/completed" -H "accept: application/json" --user hoa:hoa123 -H "content-type: application/json" -d "{\"outHoaApproval\":\"true\", \"outSolarVillageRepAttendedHOA\":\"true\"}"


