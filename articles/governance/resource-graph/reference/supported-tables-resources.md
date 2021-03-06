---
title: Unterstützte Resource Manager-Ressourcentypen
description: Stellen Sie eine Liste der Resource Manager-Ressourcentypen bereit, die von Azure Resource Graph und dem Änderungsverlauf unterstützt werden.
ms.date: 03/23/2020
ms.topic: reference
ms.openlocfilehash: 64fd860090cc15cc6914ee926772146b98477edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130611"
---
# <a name="azure-resource-graph-table-and-resource-type-reference"></a>Azure Resource Graph-Tabelle und Ressourcentypreferenz

Azure Resource Graph unterstützt die folgenden **Ressourcentypen** von [Azure Resource Manager](../../../azure-resource-manager/management/overview.md). Jeder **Ressourcentyp** ist Teil einer **Tabelle** in Resource Graph.

## <a name="advisorresources"></a>advisorresources

- microsoft.advisor/configurations
- microsoft.advisor/recommendations
- microsoft.advisor/suppressions

## <a name="alertsmanagementresources"></a>alertsmanagementresources

- microsoft.alertsmanagement/alerts

## <a name="maintenanceresources"></a>maintenanceresources

- microsoft.maintenance/configurationassignments
- microsoft.maintenance/updates

## <a name="resourcecontainers"></a>resourcecontainers

- microsoft.resources/subscriptions
- microsoft.resources/subscriptions/resourcegroups

## <a name="resources"></a>ressourcen

- 84codes.cloudamqp/servers
- citrix.services/xenappessentials
- citrix.services/xendesktopessentials
- conexlink.mycloudit/accounts
- crypteron.datasecurity/apps
- gridpro.evops/accounts
- gridpro.evops/accounts/eventrules
- gridpro.evops/accounts/requesttemplates
- gridpro.evops/accounts/views
- hive.streaming/services
- incapsula.waf/accounts
- livearena.broadcast/services
- mailjet.email/services
- microsoft.aad/domainservices
- microsoft.aadiam/tenants
- microsoft.alertsmanagement/actionrules
- microsoft.alertsmanagement/smartdetectoralertrules
- microsoft.analysisservices/servers
- microsoft.apimanagement/service
- microsoft.appconfiguration/configurationstores
- microsoft.appplatform/spring
- microsoft.archive/collections
- microsoft.automation/automationaccounts
- microsoft.automation/automationaccounts/configurations
- microsoft.automation/automationaccounts/runbooks
- microsoft.azconfig/configurationstores
- microsoft.azureactivedirectory/b2cdirectories
- microsoft.azuredata/hybriddatamanagers
- microsoft.azuredata/postgresinstances
- microsoft.azuredata/sqlbigdataclusters
- microsoft.azuredata/sqlinstances
- microsoft.azuredata/sqlserverregistrations
- microsoft.azurestack/registrations
- microsoft.baremetal/consoleconnections
- microsoft.baremetal/crayservers
- microsoft.baremetal/monitoringservers
- microsoft.batch/batchaccounts
- microsoft.batchai/clusters
- microsoft.batchai/fileservers
- microsoft.batchai/jobs
- microsoft.batchai/workspaces
- microsoft.bingmaps/mapapis
- microsoft.biztalkservices/biztalk
- microsoft.blockchain/blockchainmembers
- microsoft.blockchain/cordamembers
- microsoft.blockchain/watchers
- microsoft.botservice/botservices
- microsoft.cache/redis
- microsoft.cdn/cdnwebapplicationfirewallpolicies
- microsoft.cdn/profiles
- microsoft.cdn/profiles/endpoints
- microsoft.certificateregistration/certificateorders
- microsoft.classiccompute/domainnames
- microsoft.classiccompute/virtualmachines
- microsoft.classicnetwork/networksecuritygroups
- microsoft.classicnetwork/reservedips
- microsoft.classicnetwork/virtualnetworks
- microsoft.classicstorage/storageaccounts
- microsoft.cloudes/accounts
- microsoft.cloudsearch/indexes
- microsoft.cognition/syntheticsaccounts
- microsoft.cognitiveservices/accounts
- microsoft.compute/availabilitysets
- microsoft.compute/diskencryptionsets
- microsoft.compute/disks
- microsoft.compute/galleries
- microsoft.compute/galleries/applications
- microsoft.compute/galleries/applications/versions
- microsoft.compute/galleries/images
- microsoft.compute/galleries/images/versions
- microsoft.compute/hostgroups
- microsoft.compute/hostgroups/hosts
- microsoft.compute/images
- microsoft.compute/proximityplacementgroups
- microsoft.compute/restorepointcollections
- microsoft.compute/sharedvmextensions
- microsoft.compute/sharedvmextensions/versions
- microsoft.compute/sharedvmimages
- microsoft.compute/sharedvmimages/versions
- microsoft.compute/snapshots
- microsoft.compute/sshpublickeys
- microsoft.compute/virtualmachines
- microsoft.compute/virtualmachines/extensions
- microsoft.compute/virtualmachinescalesets
- microsoft.containerinstance/containergroups
- microsoft.containerregistry/registries
- microsoft.containerregistry/registries/agentpools
- microsoft.containerregistry/registries/buildtasks
- microsoft.containerregistry/registries/replications
- microsoft.containerregistry/registries/taskruns
- microsoft.containerregistry/registries/tasks
- microsoft.containerregistry/registries/webhooks
- microsoft.containerservice/containerservices
- microsoft.containerservice/managedclusters
- microsoft.containerservice/openshiftmanagedclusters
- microsoft.contoso/employees
- microsoft.costmanagement/connectors
- microsoft.customproviders/resourceproviders
- microsoft.databox/jobs
- microsoft.databoxedge/databoxedgedevices
- microsoft.databricks/workspaces
- microsoft.datacatalog/catalogs
- microsoft.datacatalog/datacatalogs
- microsoft.datafactory/datafactories
- microsoft.datafactory/factories
- microsoft.datalakeanalytics/accounts
- microsoft.datalakestore/accounts
- microsoft.datamigration/services
- microsoft.datamigration/services/projects
- microsoft.datamigration/slots
- microsoft.dataprotection/backupvaults
- microsoft.datashare/accounts
- microsoft.dbformariadb/servers
- microsoft.dbformysql/servers
- microsoft.dbforpostgresql/servergroups
- microsoft.dbforpostgresql/servers
- microsoft.dbforpostgresql/serversv2
- microsoft.dbforpostgresql/singleservers
- microsoft.deploymentmanager/artifactsources
- microsoft.deploymentmanager/rollouts
- microsoft.deploymentmanager/servicetopologies
- microsoft.deploymentmanager/servicetopologies/services
- microsoft.deploymentmanager/servicetopologies/services/serviceunits
- microsoft.deploymentmanager/steps
- microsoft.desktopvirtualization/applicationgroups
- microsoft.desktopvirtualization/hostpools
- microsoft.desktopvirtualization/workspaces
- microsoft.detonationservice/detonationinstances
- microsoft.devices/elasticpools
- microsoft.devices/elasticpools/iothubtenants
- microsoft.devices/iothubs
- microsoft.devices/provisioningservices
- microsoft.devops/pipelines
- microsoft.devspaces/controllers
- microsoft.devtestlab/labcenters
- microsoft.devtestlab/labs
- microsoft.devtestlab/labs/servicerunners
- microsoft.devtestlab/labs/virtualmachines
- microsoft.devtestlab/schedules
- microsoft.digitaltwins/digitaltwinsinstances
- microsoft.documentdb/databaseaccounts
- microsoft.domainregistration/domains
- microsoft.enterpriseknowledgegraph/services
- microsoft.eventgrid/domains
- microsoft.eventgrid/partnernamespaces
- microsoft.eventgrid/partnerregistrations
- microsoft.eventgrid/partnertopics
- microsoft.eventgrid/systemtopics
- microsoft.eventgrid/topics
- microsoft.eventhub/clusters
- microsoft.eventhub/namespaces
- microsoft.experimentation/experimentworkspaces
- microsoft.gaming/titles
- microsoft.genomics/accounts
- microsoft.guestconfiguration/automanagedaccounts
- microsoft.hanaonazure/hanainstances
- microsoft.hanaonazure/sapmonitors
- microsoft.hardwaresecuritymodules/dedicatedhsms
- microsoft.hdinsight/clusters
- microsoft.healthcareapis/services
- microsoft.hybridcompute/machines
- microsoft.hybridcompute/machines/extensions
- microsoft.hybriddata/datamanagers
- microsoft.hydra/components
- microsoft.hydra/networkscopes
- microsoft.importexport/jobs
- microsoft.insights/actiongroups
- microsoft.insights/activitylogalerts
- microsoft.insights/alertrules
- microsoft.insights/autoscalesettings
- microsoft.insights/components
- microsoft.insights/datacollectionrules
- microsoft.insights/guestdiagnosticsettings
- microsoft.insights/metricalerts
- microsoft.insights/notificationgroups
- microsoft.insights/notificationrules
- microsoft.insights/privatelinkscopes
- microsoft.insights/scheduledqueryrules
- microsoft.insights/webtests
- microsoft.insights/workbooks
- microsoft.insights/workbooktemplates
- microsoft.iotcentral/iotapps
- microsoft.iotspaces/graph
- microsoft.keyvault/hsmpools
- microsoft.keyvault/vaults
- microsoft.kubernetes/connectedclusters
- microsoft.kusto/clusters
- microsoft.kusto/clusters/databases
- microsoft.labservices/labaccounts
- microsoft.logic/integrationaccounts
- microsoft.logic/integrationserviceenvironments
- microsoft.logic/integrationserviceenvironments/managedapis
- microsoft.logic/workflows
- microsoft.machinelearning/commitmentplans
- microsoft.machinelearning/webservices
- microsoft.machinelearning/workspaces
- microsoft.machinelearningcompute/operationalizationclusters
- microsoft.machinelearningservices/workspaces
- microsoft.maintenance/maintenanceconfigurations
- microsoft.maintenance/maintenancepolicies
- microsoft.managedidentity/groups
- microsoft.managedidentity/userassignedidentities
- microsoft.managednetwork/managednetworkgroups
- microsoft.managednetwork/managednetworkpeeringpolicies
- microsoft.managednetwork/managednetworks
- microsoft.managednetwork/managednetworks/managednetworkgroups
- microsoft.managednetwork/managednetworks/managednetworkpeeringpolicies
- microsoft.maps/accounts
- microsoft.maps/accounts/privateatlases
- microsoft.marketplaceapps/classicdevservices
- microsoft.media/mediaservices
- microsoft.media/mediaservices/liveevents
- microsoft.media/mediaservices/streamingendpoints
- microsoft.media/mediaservices/transforms
- microsoft.microservices4spring/appclusters
- microsoft.migrate/assessmentprojects
- microsoft.migrate/migrateprojects
- microsoft.migrate/movecollections
- microsoft.migrate/projects
- microsoft.mixedreality/holographicsbroadcastaccounts
- microsoft.mixedreality/objectunderstandingaccounts
- microsoft.mixedreality/remoterenderingaccounts
- microsoft.mixedreality/spatialanchorsaccounts
- microsoft.mixedreality/surfacereconstructionaccounts
- microsoft.netapp/netappaccounts
- microsoft.netapp/netappaccounts/backuppolicies
- microsoft.netapp/netappaccounts/capacitypools
- microsoft.netapp/netappaccounts/capacitypools/volumes
- microsoft.netapp/netappaccounts/capacitypools/volumes/mounttargets
- microsoft.netapp/netappaccounts/capacitypools/volumes/snapshots
- microsoft.network/applicationgateways
- microsoft.network/applicationgatewaywebapplicationfirewallpolicies
- microsoft.network/applicationsecuritygroups
- microsoft.network/azurefirewalls
- microsoft.network/bastionhosts
- microsoft.network/connections
- microsoft.network/ddoscustompolicies
- microsoft.network/ddosprotectionplans
- microsoft.network/dnszones
- microsoft.network/expressroutecircuits
- microsoft.network/expressroutecrossconnections
- microsoft.network/expressroutegateways
- microsoft.network/expressrouteports
- microsoft.network/firewallpolicies
- microsoft.network/frontdoors
- microsoft.network/frontdoorwebapplicationfirewallpolicies
- microsoft.network/ipallocations
- microsoft.network/ipgroups
- microsoft.network/loadbalancers
- microsoft.network/localnetworkgateways
- microsoft.network/natgateways
- microsoft.network/networkexperimentprofiles
- microsoft.network/networkintentpolicies
- microsoft.network/networkinterfaces
- microsoft.network/networkprofiles
- microsoft.network/networksecuritygroups
- microsoft.network/networkvirtualappliances
- microsoft.network/networkwatchers
- microsoft.network/networkwatchers/connectionmonitors
- microsoft.network/networkwatchers/flowlogs
- microsoft.network/networkwatchers/lenses
- microsoft.network/networkwatchers/pingmeshes
- microsoft.network/p2svpngateways
- microsoft.network/privatednszones
- microsoft.network/privatednszones/virtualnetworklinks
- microsoft.network/privateendpointredirectmaps
- microsoft.network/privateendpoints
- microsoft.network/privatelinkservices
- microsoft.network/publicipaddresses
- microsoft.network/publicipprefixes
- microsoft.network/routefilters
- microsoft.network/routetables
- microsoft.network/sampleresources
- microsoft.network/securitypartnerproviders
- microsoft.network/serviceendpointpolicies
- microsoft.network/trafficmanagerprofiles
- microsoft.network/virtualhubs
- microsoft.network/virtualnetworkgateways
- microsoft.network/virtualnetworks
- microsoft.network/virtualnetworktaps
- microsoft.network/virtualrouters
- microsoft.network/virtualwans
- microsoft.network/vpngateways
- microsoft.network/vpnserverconfigurations
- microsoft.network/vpnsites
- microsoft.notificationhubs/namespaces
- microsoft.notificationhubs/namespaces/notificationhubs
- microsoft.objectstore/osnamespaces
- microsoft.offazure/hypervsites
- microsoft.offazure/importsites
- microsoft.offazure/serversites
- microsoft.offazure/vmwaresites
- microsoft.operationalinsights/clusters
- microsoft.operationalinsights/workspaces
- microsoft.operationsmanagement/solutions
- microsoft.operationsmanagement/views
- microsoft.peering/peerings
- microsoft.peering/peeringservices
- microsoft.portal/dashboards
- microsoft.portalsdk/rootresources
- microsoft.powerbi/workspacecollections
- microsoft.powerbidedicated/capacities
- microsoft.projectarcadia/workspaces
- microsoft.projectarcadia/workspaces/sparkcomputes
- microsoft.projectarcadia/workspaces/sqlcomputes
- microsoft.projectbabylon/accounts
- microsoft.quantum/workspaces
- microsoft.recoveryservices/vaults
- microsoft.redhatopenshift/openshiftclusters
- microsoft.relay/namespaces
- microsoft.remoteapp/collections
- microsoft.resourcegraph/queries
- microsoft.resources/deploymentscripts
- microsoft.saas/applications
- microsoft.scheduler/jobcollections
- microsoft.search/searchservices
- microsoft.security/automations
- microsoft.security/iotsecuritysolutions
- microsoft.securitydetonation/chambers
- microsoft.servicebus/namespaces
- microsoft.servicefabric/clusters
- microsoft.servicefabric/containergroupsets
- microsoft.servicefabric/managedclusters
- microsoft.servicefabricmesh/applications
- microsoft.servicefabricmesh/gateways
- microsoft.servicefabricmesh/networks
- microsoft.servicefabricmesh/secrets
- microsoft.servicefabricmesh/volumes
- microsoft.signalrservice/signalr
- microsoft.solutions/appliancedefinitions
- microsoft.solutions/appliances
- microsoft.solutions/applicationdefinitions
- microsoft.solutions/applications
- microsoft.solutions/jitrequests
- microsoft.spoolservice/spools
- microsoft.sql/instancepools
- microsoft.sql/managedinstances
- microsoft.sql/managedinstances/databases
- microsoft.sql/servers
- microsoft.sql/servers/databases
- microsoft.sql/servers/elasticpools
- microsoft.sql/servers/jobaccounts
- microsoft.sql/servers/jobagents
- microsoft.sql/virtualclusters
- microsoft.sqlvirtualmachine/sqlvirtualmachinegroups
- microsoft.sqlvirtualmachine/sqlvirtualmachines
- microsoft.sqlvm/dwvm
- microsoft.storage/storageaccounts
- microsoft.storagecache/caches
- microsoft.storagesync/storagesyncservices
- microsoft.storagesyncdev/storagesyncservices
- microsoft.storagesyncint/storagesyncservices
- microsoft.storsimple/managers
- microsoft.streamanalytics/streamingjobs
- microsoft.support/supporttickets
- microsoft.synapse/workspaces
- microsoft.synapse/workspaces/bigdatapools
- microsoft.synapse/workspaces/sqlpools
- microsoft.terraformoss/providerregistrations
- microsoft.timeseriesinsights/environments
- microsoft.timeseriesinsights/environments/eventsources
- microsoft.timeseriesinsights/environments/referencedatasets
- microsoft.token/stores
- microsoft.tokenvault/vaults
- microsoft.virtualmachineimages/imagetemplates
- microsoft.visualstudio/account
- microsoft.visualstudio/account/extension
- microsoft.visualstudio/account/project
- microsoft.vmwarecloudsimple/dedicatedcloudnodes
- microsoft.vmwarecloudsimple/dedicatedcloudservices
- microsoft.vmwarecloudsimple/virtualmachines
- microsoft.vmwareonazure/privateclouds
- microsoft.vmwarevirtustream/privateclouds
- microsoft.vnfmanager/devices
- microsoft.vnfmanager/vnfs
- microsoft.vsonline/accounts
- microsoft.vsonline/plans
- microsoft.web/apimanagementaccounts/apis
- microsoft.web/certificates
- microsoft.web/connectiongateways
- microsoft.web/connections
- microsoft.web/customapis
- microsoft.web/hostingenvironments
- microsoft.web/kubeenvironments
- microsoft.web/serverfarms
- microsoft.web/sites
- microsoft.web/sites/premieraddons
- microsoft.web/sites/slots
- microsoft.web/staticsites
- microsoft.windowsesu/multipleactivationkeys
- microsoft.windowsiot/deviceservices
- myget.packagemanagement/services
- paraleap.cloudmonix/services
- pokitdok.platform/services
- providers.test/statefulibizaengines
- providers.test/statefulresources
- providers.test/statefulresources/nestedresources
- providers.test/statelessresources
- ravenhq.db/databases
- raygun.crashreporting/apps
- sendgrid.email/accounts
- sparkpost.basic/services
- stackify.retrace/services
- test.shoebox/testresources
- test.shoebox/testresources2
- trendmicro.deepsecurity/accounts
- u2uconsult.theidentityhub/services
- wandisco.fusion/fusiongroups
- wandisco.fusion/fusiongroups/azurezones
- wandisco.fusion/fusiongroups/azurezones/plugins
- wandisco.fusion/fusiongroups/managedonpremzones
- wandisco.fusion/fusiongroups/onpremzones
- wandisco.fusion/fusiongroups/replicationrules

## <a name="securityresources"></a>securityresources

- assessmentmetadata
- microsoft.security/assessments
- microsoft.security/assessments/subassessments
- microsoft.security/pricings
- microsoft.security/regulatorycompliancestandards
- microsoft.security/regulatorycompliancestandards/regulatorycompliancecontrols
- microsoft.security/regulatorycompliancestandards/regulatorycompliancecontrols/regulatorycomplianceassessments
- microsoft.security/securitystatuses
- microsoft.security/securitystatuses/containerhosts
- microsoft.security/securitystatuses/onpremisemachines
- microsoft.security/securitystatuses/servers
- microsoft.security/securitystatuses/subnets
- microsoft.security/securitystatuses/virtualmachines
- microsoft.security/securitystatusessummaries

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Abfragesprache](../concepts/query-language.md).
- Erfahren Sie mehr über das [Erkunden von Ressourcen](../concepts/explore-resources.md).
- Sehen Sie sich Beispiele für [einfache Abfragen](../samples/starter.md) an.
