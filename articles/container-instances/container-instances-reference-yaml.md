---
title: YAML-verwijzing voor containergroep
description: Naslag voor het YAML-bestand dat wordt Azure Container Instances om een containergroep te configureren
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: efbea7b64ccdf685d4c82bd406f2aa09227e53e1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771133"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML-naslag: Azure Container Instances

In dit artikel worden de syntaxis en eigenschappen voor het YAML-bestand beschreven die worden Azure Container Instances om een [containergroep te configureren.](container-instances-container-groups.md) Gebruik een YAML-bestand om de groepsconfiguratie in te voeren in [de opdracht az container create][az-container-create] in de Azure CLI. 

Een YAML-bestand is een handige manier om een containergroep te configureren voor reproduceerbare implementaties. Het is een beknopt alternatief voor het gebruik van [een Resource Manager-sjabloon](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups) of de Azure Container Instances-SDK's om een containergroep te maken of bij te werken.

> [!NOTE]
> Deze verwijzing is van toepassing op YAML-bestanden voor Azure Container Instances REST API versie `2019-12-01` .

## <a name="schema"></a>Schema 

Het schema voor het YAML-bestand volgt, inclusief opmerkingen om belangrijke eigenschappen te markeren. Zie de sectie Eigenschapswaarden voor een beschrijving van de eigenschappen in [dit](#property-values) schema.

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

## <a name="property-values"></a>Eigenschapswaarden

In de volgende tabellen worden de waarden beschreven die u in het schema moet instellen.



### <a name="microsoftcontainerinstancecontainergroups-object"></a>Microsoft.ContainerInstance/containerGroups-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  naam | tekenreeks | Yes | De naam van de containergroep. |
|  apiVersion | Enum | Yes | 2018-10-01 |
|  location | tekenreeks | No | De resourcelocatie. |
|  tags | object | No | De resourcetags. |
|  identity | object | No | De identiteit van de containergroep, indien geconfigureerd. - [Object ContainerGroupIdentity](#containergroupidentity-object) |
|  properties | object | Yes | [Object ContainerGroupProperties](#containergroupproperties-object) |




### <a name="containergroupidentity-object"></a>Object ContainerGroupIdentity

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  type | Enum | No | Het type identiteit dat wordt gebruikt voor de containergroep. Het type 'SystemAssigned, UserAssigned' bevat zowel een impliciet gemaakte identiteit als een set door de gebruiker toegewezen identiteiten. Met het type Geen worden alle identiteiten uit de containergroep verwijderd. - SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, None |
|  userAssignedIdentities | object | No | De lijst met gebruikersidentiteiten die zijn gekoppeld aan de containergroep. De sleutelverwijzingen voor de gebruikersidentiteitswoordenlijst zijn Azure Resource Manager resource-id's in de vorm: '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'. |




### <a name="containergroupproperties-object"></a>Object ContainerGroupProperties

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  containers | matrix | Yes | De containers in de containergroep. - [Containerobject](#container-object) |
|  imageRegistryCredentials | matrix | No | De registerreferenties van de afbeelding waarmee de containergroep wordt gemaakt. - [ImageRegistryCredential-object](#imageregistrycredential-object) |
|  restartPolicy | Enum | No | Beleid voor opnieuw opstarten voor alle containers in de containergroep. - `Always` Altijd opnieuw `OnFailure` opstarten- Opnieuw opstarten bij fout: `Never` nooit opnieuw opstarten. - Always, OnFailure, Never |
|  ipAddress | object | No | Het IP-adrestype van de containergroep. - [IpAddress-object](#ipaddress-object) |
|  osType | Enum | Yes | Het besturingssysteemtype dat is vereist voor de containers in de containergroep. - Windows of Linux |
|  volumes | matrix | No | De lijst met volumes die kunnen worden bevestigd door containers in deze containergroep. - [Volumeobject](#volume-object) |
|  diagnostische gegevens | object | No | De diagnostische gegevens voor een containergroep. - [Object ContainerGroupDiagnostics](#containergroupdiagnostics-object) |
|  networkProfile | object | No | De netwerkprofielgegevens voor een containergroep. - [Object ContainerGroupNetworkProfile](#containergroupnetworkprofile-object) |
|  dnsConfig | object | No | De DNS-configuratiegegevens voor een containergroep. - [DnsConfiguration-object](#dnsconfiguration-object) |
| sku | Enum | No | De SKU voor een containergroep- Standard of Dedicated |
| encryptionProperties | object | No | De versleutelingseigenschappen voor een containergroep. - [Object EncryptionProperties](#encryptionproperties-object) | 
| initContainers | matrix | No | De init-containers voor een containergroep. - [InitContainerDefinition-object](#initcontainerdefinition-object) |




### <a name="container-object"></a>Containerobject

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  naam | tekenreeks | Yes | De door de gebruiker opgegeven naam van de container-instantie. |
|  properties | object | Yes | De eigenschappen van de container-instantie. - [Object ContainerProperties](#containerproperties-object) |




### <a name="imageregistrycredential-object"></a>ImageRegistryCredential-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  server | tekenreeks | Yes | De Registerserver voor Docker-afbeeldingen zonder protocol, zoals 'http' en 'https'. |
|  gebruikersnaam | tekenreeks | Yes | De gebruikersnaam voor het privéregister. |
|  wachtwoord | tekenreeks | No | Het wachtwoord voor het privéregister. |




### <a name="ipaddress-object"></a>IpAddress-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  ports | matrix | Yes | De lijst met poorten die worden weergegeven in de containergroep. - [Poortobject](#port-object) |
|  type | Enum | Yes | Hiermee geeft u op of het IP-adres wordt blootgesteld aan het openbare internet of privé-VNET. - Openbaar of Privé |
|  IP | tekenreeks | No | Het IP-adres wordt blootgesteld aan het openbare internet. |
|  dnsNameLabel | tekenreeks | No | Het Dns-naamlabel voor het IP-adres. |




### <a name="volume-object"></a>Volumeobject

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  naam | tekenreeks | Yes | De naam van het volume. |
|  azureFile | object | No | Het Azure-bestandsvolume. - [AzureFileVolume-object](#azurefilevolume-object) |
|  emptyDir | object | No | Het lege mapvolume. |
|  geheim | object | No | Het geheime volume. |
|  gitRepo | object | No | Het git-repo-volume. - [GitRepoVolume-object](#gitrepovolume-object) |




### <a name="containergroupdiagnostics-object"></a>Object ContainerGroupDiagnostics

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | No | Logboekanalysegegevens voor containergroep. - [LogAnalytics-object](#loganalytics-object) |




### <a name="containergroupnetworkprofile-object"></a>Object ContainerGroupNetworkProfile

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  id | tekenreeks | Yes | De id voor een netwerkprofiel. |




### <a name="dnsconfiguration-object"></a>DnsConfiguration-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  Nameservers | matrix | Yes | De DNS-servers voor de containergroep. - tekenreeks |
|  searchDomains | tekenreeks | No | De DNS-zoekdomeinen voor het opzoeken van hostnamen in de containergroep. |
|  opties | tekenreeks | No | De DNS-opties voor de containergroep. |


### <a name="encryptionproperties-object"></a>Object EncryptionProperties

| Naam  | Type  | Vereist  | Waarde |
|  ---- | ---- | ---- | ---- |
| vaultBaseUrl  | tekenreeks    | Yes   | De basis-URL van de sleutelkault. |
| keyName   | tekenreeks    | Yes   | De naam van de versleutelingssleutel. |
| keyVersion    | tekenreeks    | Yes   | De versie van de versleutelingssleutel. |

### <a name="initcontainerdefinition-object"></a>InitContainerDefinition-object

| Naam  | Type  | Vereist  | Waarde |
|  ---- | ---- | ---- | ---- |
| naam  | tekenreeks |  Yes | De naam voor de init-container. |
| properties    | object    | Yes   | De eigenschappen voor de init-container. - [Object InitContainerPropertiesDefinition](#initcontainerpropertiesdefinition-object)


### <a name="containerproperties-object"></a>Object ContainerProperties

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  image | tekenreeks | Yes | De naam van de afbeelding die wordt gebruikt voor het maken van de container-instantie. |
|  command | matrix | No | De opdrachten die moeten worden uitgevoerd in de container-instantie in exec-vorm. - tekenreeks |
|  ports | matrix | No | De open zichtbare poorten op de container-instantie. - [ContainerPort-object](#containerport-object) |
|  environmentVariables | matrix | No | De omgevingsvariabelen die moeten worden ingesteld in de container-instantie. - [EnvironmentVariable-object](#environmentvariable-object) |
|  resources | object | Yes | De resourcevereisten van de container-instantie. - [ResourceRequirements-object](#resourcerequirements-object) |
|  volumeMounts | matrix | No | De volume-mounts die beschikbaar zijn voor de container-instantie. - [VolumeMount-object](#volumemount-object) |
|  livenessProbe | object | No | De test voor de liveheid. - [ContainerProbe-object](#containerprobe-object) |
|  readinessProbe | object | No | De gereedheidstest. - [ContainerProbe-object](#containerprobe-object) |




### <a name="port-object"></a>Poortobject

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  protocol | Enum | No | Het protocol dat is gekoppeld aan de poort. - TCP of UDP |
|  poort | geheel getal | Yes | het poortnummer. |




### <a name="azurefilevolume-object"></a>AzureFileVolume-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  Sharenaam | tekenreeks | Yes | De naam van de Azure-bestands share die als een volume moet worden bevestigd. |
|  Readonly | booleaans | No | De vlag die aangeeft of het gedeelde Azure-bestand als een volume alleen-lezen is. |
|  storageAccountName | tekenreeks | Yes | De naam van het opslagaccount dat de Azure-bestands share bevat. |
|  storageAccountKey | tekenreeks | No | De toegangssleutel voor het opslagaccount die wordt gebruikt voor toegang tot de Azure-bestands share. |




### <a name="gitrepovolume-object"></a>GitRepoVolume-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  map | tekenreeks | No | Naam van doelmap. Mag niet bevatten of beginnen met '..'.  Als '.' is opgegeven, wordt de volumemap de git-opslagplaats.  Anders bevat het volume, indien opgegeven, de Git-opslagplaats in de subdirectory met de opgegeven naam. |
|  repository | tekenreeks | Yes | OPSLAGPLAATS-URL |
|  revision | tekenreeks | No | Hash voor de opgegeven revisie commit. |



### <a name="loganalytics-object"></a>LogAnalytics-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  workspaceId | tekenreeks | Yes | De werkruimte-id voor Log Analytics |
|  workspaceKey | tekenreeks | Yes | De werkruimtesleutel voor Log Analytics |
|  logType | Enum | No | Het logboektype dat moet worden gebruikt. - ContainerInsights of ContainerInstanceLogs |
|  metagegevens | object | No | Metagegevens voor logboekanalyse. |


### <a name="initcontainerpropertiesdefinition-object"></a>InitContainerPropertiesDefinition-object

| Naam  | Type  | Vereist  | Waarde |
|  ---- | ---- | ---- | ---- |
| image | tekenreeks    | No    | De afbeelding van de init-container. |
| command   | matrix | No    | De opdracht die moet worden uitgevoerd in de init-container in exec-vorm. - tekenreeks |
| environmentVariables | matrix  | No |De omgevingsvariabelen die moeten worden ingesteld in de init-container. - [EnvironmentVariable-object](#environmentvariable-object)
| volumeMounts |matrix   | No    | De volume-mounts die beschikbaar zijn voor de init-container. - [VolumeMount-object](#volumemount-object)

### <a name="containerport-object"></a>ContainerPort-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  protocol | Enum | No | Het protocol dat is gekoppeld aan de poort. - TCP of UDP |
|  poort | geheel getal | Yes | Het poortnummer dat zichtbaar is in de containergroep. |




### <a name="environmentvariable-object"></a>EnvironmentVariable-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  naam | tekenreeks | Yes | De naam van de omgevingsvariabele. |
|  waarde | tekenreeks | No | De waarde van de omgevingsvariabele. |
|  secureValue | tekenreeks | No | De waarde van de beveiligde omgevingsvariabele. |




### <a name="resourcerequirements-object"></a>Object ResourceRequirements

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  requests | object | Yes | De resourceaanvragen van deze container-instantie. - [ResourceRequests-object](#resourcerequests-object) |
|  Grenzen | object | No | De resourcelimieten van deze container-instantie. - [ResourceLimits-object](#resourcelimits-object) |




### <a name="volumemount-object"></a>VolumeMount-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  naam | tekenreeks | Yes | De naam van de volume-mount. |
|  mountPath | tekenreeks | Yes | Het pad binnen de container waar het volume moet worden bevestigd. Mag geen dubbele punt (:). |
|  Readonly | booleaans | No | De vlag die aangeeft of de volume-mount alleen-lezen is. |




### <a name="containerprobe-object"></a>ContainerProbe-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  Uitvoerend | object | No | De uitvoeringsopdracht voor test - [ContainerExec-object](#containerexec-object) |
|  httpGet | object | No | De http get-instellingen om te controleren - [ContainerHttpGet-object](#containerhttpget-object) |
|  initialDelaySeconds | geheel getal | No | De eerste vertragingsseconden. |
|  periodSeconds | geheel getal | No | De periode seconden. |
|  failureThreshold | geheel getal | No | De drempelwaarde voor fouten. |
|  successThreshold | geheel getal | No | De drempelwaarde voor succes. |
|  time-outseconden | geheel getal | No | De time-out seconden. |




### <a name="resourcerequests-object"></a>ResourceRequests-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | getal | Yes | De geheugenaanvraag in GB van deze container-instantie. |
|  Cpu | getal | Yes | De CPU-aanvraag van deze container-instantie. |
|  Gpu | object | No | De GPU-aanvraag van deze container-instantie. - [GpuResource-object](#gpuresource-object) |




### <a name="resourcelimits-object"></a>ResourceLimits-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | getal | No | De geheugenlimiet in GB van deze container-instantie. |
|  Cpu | getal | No | De CPU-limiet van deze container-instantie. |
|  Gpu | object | No | De GPU-limiet van deze container-instantie. - [GpuResource-object](#gpuresource-object) |




### <a name="containerexec-object"></a>ContainerExec-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  command | matrix | No | De opdrachten die in de container moeten worden uitgevoerd. - tekenreeks |




### <a name="containerhttpget-object"></a>ContainerHttpGet-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  leertraject | tekenreeks | No | Het pad dat moet worden onderzocht. |
|  poort | geheel getal | Yes | Het poortnummer dat moet worden onderzocht. |
|  Regeling | Enum | No | Het schema. - http of https |




### <a name="gpuresource-object"></a>GpuResource-object

|  Naam | Type | Vereist | Waarde |
|  ---- | ---- | ---- | ---- |
|  count | geheel getal | Yes | Het aantal GPU-resources. |
|  sku | Enum | Yes | De SKU van de GPU-resource. - K80, P100, V100 |


## <a name="next-steps"></a>Volgende stappen

Zie de zelfstudie [Een groep met meerdere containers implementeren met behulp van een YAML-bestand.](container-instances-multi-container-yaml.md)

Zie voorbeelden van het gebruik van een YAML-bestand voor het implementeren van containergroepen in een virtueel [netwerk](container-instances-vnet.md) of het [aan een extern volume toevoegen.](container-instances-volume-azure-files.md)

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
