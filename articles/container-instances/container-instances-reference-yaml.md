---
title: YAML-verwijzing voor container groep
description: Verwijzing voor het YAML-bestand dat wordt ondersteund door Azure Container Instances voor het configureren van een container groep
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: d0ec8d13eebba1c60f5a52f8c43bdd8b90eeb913
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87084757"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML-verwijzing: Azure Container Instances

In dit artikel worden de syntaxis en eigenschappen beschreven voor het YAML-bestand dat wordt ondersteund door Azure Container Instances om een [container groep](container-instances-container-groups.md)te configureren. Gebruik een YAML-bestand om de groeps configuratie in te voeren op de opdracht [AZ container Create][az-container-create] in de Azure cli. 

Een YAML-bestand is een handige manier om een container groep te configureren voor reproduceer bare implementaties. Het is een beknopt alternatief voor het gebruik van een [Resource Manager-sjabloon](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups) of de Azure container instances sdk's om een container groep te maken of bij te werken.

> [!NOTE]
> Deze verwijzing is van toepassing op YAML-bestanden voor Azure Container Instances REST API-versie `2019-12-01` .

## <a name="schema"></a>Schema 

Het schema voor het YAML-bestand volgt, inclusief opmerkingen om de sleutel eigenschappen te markeren. Zie de sectie [eigenschaps waarden](#property-values) voor een beschrijving van de eigenschappen in dit schema.

```yml
name: string  # Name of the container group
apiVersion: '2019-12-01'
location: string
tags: {}
identity: 
  type: string
  userAssignedIdentities: {}
properties: # Properties of container group
  containers: # Array of container instances in the group
  - name: string # Name of an instance
    properties: # Properties of an instance
      image: string # Container image used to create the instance
      command:
      - string
      ports: # External-facing ports exposed on the instance, must also be set in group ipAddress property 
      - protocol: string
        port: integer
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      resources: # Resource requirements of the instance
        requests:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
        limits:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
      volumeMounts: # Array of volume mounts for the instance
      - name: string
        mountPath: string
        readOnly: boolean
      livenessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
      readinessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
  imageRegistryCredentials: # Credentials to pull a private image
  - server: string
    username: string
    password: string
  restartPolicy: string
  ipAddress: # IP address configuration of container group
    ports:
    - protocol: string
      port: integer
    type: string
    ip: string
    dnsNameLabel: string
  osType: string
  volumes: # Array of volumes available to the instances
  - name: string
    azureFile:
      shareName: string
      readOnly: boolean
      storageAccountName: string
      storageAccountKey: string
    emptyDir: {}
    secret: {}
    gitRepo:
      directory: string
      repository: string
      revision: string
  diagnostics:
    logAnalytics:
      workspaceId: string
      workspaceKey: string
      logType: string
      metadata: {}
  networkProfile: # Virtual network profile for container group
    id: string
  dnsConfig: # DNS configuration for container group
    nameServers:
    - string
    searchDomains: string
    options: string
  sku: string # SKU for the container group
  encryptionProperties:
    vaultBaseUrl: string
    keyName: string
    keyVersion: string
  initContainers: # Array of init containers in the group
  - name: string
    properties:
      image: string
      command:
      - string
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      volumeMounts:
      - name: string
        mountPath: string
        readOnly: boolean
```

## <a name="property-values"></a>Eigenschaps waarden

De volgende tabellen bevatten een beschrijving van de waarden die u moet instellen in het schema.



### <a name="microsoftcontainerinstancecontainergroups-object"></a>Micro soft. ContainerInstance/containerGroups-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  naam | tekenreeks | Ja | De naam van de container groep. |
|  apiVersion | vaste | Ja | 2018-10-01 |
|  location | tekenreeks | No | De resource locatie. |
|  tags | object | Nee | De resource Tags. |
|  identity | object | Nee | De identiteit van de container groep, indien geconfigureerd. - [ContainerGroupIdentity-object](#containergroupidentity-object) |
|  properties | object | Ja | [ContainerGroupProperties-object](#containergroupproperties-object) |




### <a name="containergroupidentity-object"></a>ContainerGroupIdentity-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  type | vaste | Nee | Het type identiteit dat voor de container groep wordt gebruikt. Het type ' SystemAssigned, UserAssigned ' bevat zowel een impliciet gemaakte identiteit als een set door de gebruiker toegewezen identiteiten. Met het type geen worden identiteiten uit de container groep verwijderd. -SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, geen |
|  userAssignedIdentities | object | Nee | De lijst met gebruikers-id's die zijn gekoppeld aan de container groep. De sleutel verwijzingen van de gebruikers-id-woorden lijst worden Azure Resource Manager bron-Id's in de vorm: '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName} '. |




### <a name="containergroupproperties-object"></a>ContainerGroupProperties-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  containers | matrix | Ja | De containers in de container groep. - [Container object](#container-object) |
|  imageRegistryCredentials | matrix | Nee | De register referenties voor de installatie kopie waarmee de container groep wordt gemaakt. - [ImageRegistryCredential-object](#imageregistrycredential-object) |
|  restartPolicy | vaste | Nee | Start het beleid voor alle containers binnen de container groep opnieuw. - `Always` Altijd opnieuw opstarten opnieuw opstarten `OnFailure` bij fout- `Never` nooit opnieuw opstarten. -Always, OnFailure, Never |
|  IPAdres | object | Nee | Het IP-adres type van de container groep. - [IpAddress-object](#ipaddress-object) |
|  osType | vaste | Ja | Het type besturings systeem dat vereist is voor de containers in de container groep. -Windows of Linux |
|  volumes | matrix | Nee | De lijst met volumes die kunnen worden gekoppeld door containers in deze container groep. - [Volume object](#volume-object) |
|  diagnostische gegevens | object | Nee | De diagnostische gegevens voor een container groep. - [ContainerGroupDiagnostics-object](#containergroupdiagnostics-object) |
|  networkProfile | object | Nee | De netwerk profiel gegevens voor een container groep. - [ContainerGroupNetworkProfile-object](#containergroupnetworkprofile-object) |
|  dnsConfig | object | Nee | De DNS-configuratie-informatie voor een container groep. - [DnsConfiguration-object](#dnsconfiguration-object) |
| sku | vaste | Nee | De SKU voor een container groep: Standard of dedicated |
| encryptionProperties | object | Nee | De versleutelings eigenschappen voor een container groep. - [EncryptionProperties-object](#encryptionproperties-object) | 
| initContainers | matrix | Nee | De init-containers voor een container groep. - [InitContainerDefinition-object](#initcontainerdefinition-object) |




### <a name="container-object"></a>Container object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  naam | tekenreeks | Ja | De door de gebruiker gegeven naam van het container exemplaar. |
|  properties | object | Ja | De eigenschappen van het container exemplaar. - [ContainerProperties-object](#containerproperties-object) |




### <a name="imageregistrycredential-object"></a>ImageRegistryCredential-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  server | tekenreeks | Ja | De register server van de docker-installatie kopie zonder een protocol zoals http en HTTPS. |
|  gebruikersnaam | tekenreeks | Ja | De gebruikers naam voor het persoonlijke REGI ster. |
|  wachtwoord | tekenreeks | No | Het wacht woord voor het persoonlijke REGI ster. |




### <a name="ipaddress-object"></a>IpAddress-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  ports | matrix | Ja | De lijst met poorten die worden weer gegeven in de container groep. - [Poort object](#port-object) |
|  type | vaste | Ja | Hiermee geeft u op of het IP-adres beschikbaar is voor het open bare Internet of het persoonlijke VNET. -Openbaar of privé |
|  IP | tekenreeks | No | Het IP-adres dat toegankelijk is voor het open bare Internet. |
|  dnsNameLabel | tekenreeks | No | Het DNS-naam label voor het IP-adres. |




### <a name="volume-object"></a>Volume object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  naam | tekenreeks | Ja | De naam van het volume. |
|  azureFile | object | Nee | Het Azure-bestands volume. - [AzureFileVolume-object](#azurefilevolume-object) |
|  emptyDir | object | Nee | Het lege directory volume. |
|  geheim | object | Nee | Het geheime volume. |
|  gitRepo | object | Nee | Het git opslag plaats-volume. - [GitRepoVolume-object](#gitrepovolume-object) |




### <a name="containergroupdiagnostics-object"></a>ContainerGroupDiagnostics-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | Nee | Informatie over logboek analyse van container groep. - [LogAnalytics-object](#loganalytics-object) |




### <a name="containergroupnetworkprofile-object"></a>ContainerGroupNetworkProfile-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  id | tekenreeks | Ja | De id voor een netwerk profiel. |




### <a name="dnsconfiguration-object"></a>DnsConfiguration-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  Naam servers | matrix | Ja | De DNS-servers voor de container groep. -teken reeks |
|  searchDomains | tekenreeks | No | De DNS-Zoek domeinen voor het opzoeken van hostnamen in de container groep. |
|  opties | tekenreeks | No | De DNS-opties voor de container groep. |


### <a name="encryptionproperties-object"></a>EncryptionProperties-object

| Naam  | Type  | Vereist  | Waarde |
|  ---- | ---- | ---- | ---- |
| vaultBaseUrl  | tekenreeks    | Ja   | De basis-URL van de sleutel kluis. |
| keyName   | tekenreeks    | Ja   | De naam van de versleutelings sleutel. |
| Versie    | tekenreeks    | Ja   | De versie van de versleutelings sleutel. |

### <a name="initcontainerdefinition-object"></a>InitContainerDefinition-object

| Naam  | Type  | Vereist  | Waarde |
|  ---- | ---- | ---- | ---- |
| naam  | tekenreeks |  Ja | De naam voor de init-container. |
| properties    | object    | Ja   | De eigenschappen voor de init-container. - [InitContainerPropertiesDefinition-object](#initcontainerpropertiesdefinition-object)


### <a name="containerproperties-object"></a>ContainerProperties-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  image | tekenreeks | Ja | De naam van de installatie kopie die wordt gebruikt om het container exemplaar te maken. |
|  command | matrix | Nee | De opdrachten die moeten worden uitgevoerd binnen het container exemplaar in het formulier exec. -teken reeks |
|  ports | matrix | Nee | De weer gegeven poorten op het container exemplaar. - [ContainerPort-object](#containerport-object) |
|  Omgevings variabelen | matrix | Nee | De omgevings variabelen die in het container exemplaar moeten worden ingesteld. - [EnvironmentVariable-object](#environmentvariable-object) |
|  resources | object | Ja | De resource vereisten van het container exemplaar. - [ResourceRequirements-object](#resourcerequirements-object) |
|  volumeMounts | matrix | Nee | Het volume koppelt beschikbaar voor het container exemplaar. - [VolumeMount-object](#volumemount-object) |
|  livenessProbe | object | Nee | De duur van de liveiteit. - [ContainerProbe-object](#containerprobe-object) |
|  readinessProbe | object | Nee | De gereedheids test. - [ContainerProbe-object](#containerprobe-object) |




### <a name="port-object"></a>Poort object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  protocol | vaste | Nee | Het protocol dat is gekoppeld aan de poort. -TCP of UDP |
|  poort | geheel getal | Ja | het poortnummer. |




### <a name="azurefilevolume-object"></a>AzureFileVolume-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  shareName | tekenreeks | Ja | De naam van de Azure-bestands share die moet worden gekoppeld als een volume. |
|  Kenmerk | booleaans | Nee | De vlag waarmee wordt aangegeven of het Azure-bestand dat als een volume is gedeeld, alleen-lezen is. |
|  storageAccountName | tekenreeks | Ja | De naam van het opslag account dat de Azure-bestands share bevat. |
|  storageAccountKey | tekenreeks | No | De toegangs sleutel voor het opslag account die wordt gebruikt om toegang te krijgen tot de Azure-bestands share. |




### <a name="gitrepovolume-object"></a>GitRepoVolume-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  map | tekenreeks | No | Naam van de doel directory. Mag niet bevatten of beginnen met...  Als '. ' is opgegeven, wordt de map van het volume de Git-opslag plaats.  Als dat niet het geval is, bevat het volume de Git-opslag plaats in de submap met de opgegeven naam. |
|  repository | tekenreeks | Ja | URL van opslag plaats |
|  revision | tekenreeks | No | Hash voor de opgegeven revisie door voeren. |



### <a name="loganalytics-object"></a>LogAnalytics-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  workspaceId | tekenreeks | Ja | De werk ruimte-id voor log Analytics |
|  workspaceKey | tekenreeks | Ja | De werkruimte sleutel voor log Analytics |
|  logType | vaste | Nee | Het logboek type dat moet worden gebruikt. -ContainerInsights of ContainerInstanceLogs |
|  metagegevens | object | Nee | Meta gegevens voor log Analytics. |


### <a name="initcontainerpropertiesdefinition-object"></a>InitContainerPropertiesDefinition-object

| Naam  | Type  | Vereist  | Waarde |
|  ---- | ---- | ---- | ---- |
| image | tekenreeks    | No    | De afbeelding van de init-container. |
| command   | matrix | Nee    | De opdracht die moet worden uitgevoerd binnen de init-container in het formulier exec. -teken reeks |
| Omgevings variabelen | matrix  | Nee |De omgevings variabelen die moeten worden ingesteld in de init-container. - [EnvironmentVariable-object](#environmentvariable-object)
| volumeMounts |matrix   | Nee    | Het volume koppelt beschikbaar voor de init-container. - [VolumeMount-object](#volumemount-object)

### <a name="containerport-object"></a>ContainerPort-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  protocol | vaste | Nee | Het protocol dat is gekoppeld aan de poort. -TCP of UDP |
|  poort | geheel getal | Ja | Het poort nummer dat wordt weer gegeven in de container groep. |




### <a name="environmentvariable-object"></a>EnvironmentVariable-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  naam | tekenreeks | Ja | De naam van de omgevings variabele. |
|  waarde | tekenreeks | No | De waarde van de omgevings variabele. |
|  Securevalue op | tekenreeks | No | De waarde van de variabele beveiligde omgeving. |




### <a name="resourcerequirements-object"></a>ResourceRequirements-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  requests | object | Ja | De resource aanvragen van dit container exemplaar. - [ResourceRequests-object](#resourcerequests-object) |
|  limieten | object | Nee | De resource limieten van dit container exemplaar. - [ResourceLimits-object](#resourcelimits-object) |




### <a name="volumemount-object"></a>VolumeMount-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  naam | tekenreeks | Ja | De naam van het volume koppeling. |
|  mountPath | tekenreeks | Ja | Het pad binnen de container waarin het volume moet worden gekoppeld. Mag geen dubbele punt (:)) bevatten. |
|  Kenmerk | booleaans | Nee | De vlag waarmee wordt aangegeven of de koppeling van het volume alleen-lezen is. |




### <a name="containerprobe-object"></a>ContainerProbe-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  exec | object | Nee | De opdracht uitvoeren om te testen- [ContainerExec-object](#containerexec-object) |
|  httpGet | object | Nee | De HTTP Get-instellingen voor het probe- [ContainerHttpGet-object](#containerhttpget-object) |
|  initialDelaySeconds | geheel getal | Nee | De eerste vertraging in seconden. |
|  periodSeconds | geheel getal | Nee | De periode seconden. |
|  failureThreshold | geheel getal | Nee | De drempel waarde voor fouten. |
|  successThreshold | geheel getal | Nee | De drempel waarde voor geslaagde pogingen. |
|  timeoutSeconds | geheel getal | Nee | De time-outwaarde seconden. |




### <a name="resourcerequests-object"></a>ResourceRequests-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | getal | Ja | De geheugen aanvraag in GB van dit container exemplaar. |
|  verbruik | getal | Ja | De CPU-aanvraag van dit container exemplaar. |
|  GPU | object | Nee | De GPU-aanvraag van dit container exemplaar. - [GpuResource-object](#gpuresource-object) |




### <a name="resourcelimits-object"></a>ResourceLimits-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | getal | Nee | De geheugen limiet in GB van dit container exemplaar. |
|  verbruik | getal | Nee | De CPU-limiet van dit container exemplaar. |
|  GPU | object | Nee | De GPU-limiet van dit container exemplaar. - [GpuResource-object](#gpuresource-object) |




### <a name="containerexec-object"></a>ContainerExec-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  command | matrix | Nee | De opdrachten die moeten worden uitgevoerd in de container. -teken reeks |




### <a name="containerhttpget-object"></a>ContainerHttpGet-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  leertraject | tekenreeks | No | Het pad dat moet worden getest. |
|  poort | geheel getal | Ja | Het poort nummer dat moet worden getest. |
|  niveaus | vaste | Nee | Het schema. -http of https |




### <a name="gpuresource-object"></a>GpuResource-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  count | geheel getal | Ja | Het aantal GPU-resources. |
|  sku | vaste | Ja | De SKU van de GPU-resource. -K80, P100, V100 |


## <a name="next-steps"></a>Volgende stappen

Zie de zelf studie [een groep met meerdere containers implementeren met behulp van een yaml-bestand](container-instances-multi-container-yaml.md).

Zie voor beelden van het gebruik van een YAML-bestand voor het implementeren van container groepen in een [virtueel netwerk](container-instances-vnet.md) of het [koppelen van een extern volume](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

