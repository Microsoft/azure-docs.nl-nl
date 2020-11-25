---
title: API Management integreren met Service Fabric in azure
description: Meer informatie over hoe u snel aan de slag kunt gaan met Azure API Management en verkeer naar een back-end-service in Service Fabric routeren.
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 61a13d80fd67751d77bc77199fa433143ad92048
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007100"
---
# <a name="integrate-api-management-with-service-fabric-in-azure"></a>API Management integreren met Service Fabric in azure

De implementatie van Azure API Management met Service Fabric is een geavanceerd scenario.  API Management is handig als u API's met een geavanceerde set regels voor doorsturen moet publiceren voor uw Service Fabric-services in de back-end. Cloudtoepassingen hebben meestal een gateway in de front-end nodig om een centraal ingangspunt te bieden voor gebruikers, apparaten of andere toepassingen. In Service Fabric kan een gateway elke stateless service zijn die is ontworpen voor inkomend verkeer, zoals een ASP.NET Core-toepassing, Event Hubs, IoT-Hub of Azure API Management.

In dit artikel wordt beschreven hoe u [Azure API Management](../api-management/api-management-key-concepts.md) instelt met Service Fabric om verkeer door te sturen naar een back-end-Service in service Fabric.  Aan het einde van de zelfstudie hebt u API Management geïmplementeerd in een VNET en een API-bewerking geconfigureerd voor het verzenden van verkeer naar -stateless services in de back-end. Zie het [overzichtsartikel](service-fabric-api-management-overview.md) voor meer informatie over Azure API Management-scenario's met Service Fabric.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability"></a>Beschikbaarheid

> [!IMPORTANT]
> Deze functie is beschikbaar in de **Premium** -en **ontwikkelaars** lagen van API Management wegens de vereiste ondersteuning voor het virtuele netwerk.

## <a name="prerequisites"></a>Vereisten

Voordat u begint:

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installeer [Azure Powershell](/powershell/azure/install-az-ps) of [Azure CLI](/cli/azure/install-azure-cli).
* Maak een beveiligd [Windows-cluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) in een netwerk beveiligings groep.
* Als u een Windows-cluster implementeert, richt u een Windows-ontwikkelomgeving in. Installeer [Visual Studio 2019](https://www.visualstudio.com) en de workloads voor **Azure-ontwikkeling**, **ASP.NET-ontwikkeling en webontwikkeling** en **.NET Core platformoverschrijdende ontwikkeling**.  Richt vervolgens een [.NET-ontwikkelomgeving in](service-fabric-get-started.md).

## <a name="network-topology"></a>Netwerktopologie

Nu u een beveiligd Windows- [cluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) op Azure hebt, implementeert u API Management naar het virtuele netwerk (VNET) in het SUBNET en NSG die zijn aangewezen voor API management. Voor dit artikel is de API Management Resource Manager-sjabloon vooraf geconfigureerd voor het gebruik van de namen van het VNET, het subnet en de NSG die u in de [zelf studie Windows-cluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) hebt ingesteld in dit artikel wordt de volgende topologie geïmplementeerd in Azure waarin API Management en service Fabric zich in subnetten van hetzelfde Virtual Network bevinden:

 ![Afbeelding van topologie][sf-apim-topology-overview]

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Aanmelden bij Azure en uw abonnement selecteren

Meld u aan bij uw Azure-account en selecteer uw abonnement voordat u Azure-opdrachten gaat uitvoeren.

```powershell
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Een service implementeren in de back-end van Service Fabric

Voordat u API Management configureert voor het routeren van verkeer naar een service in de back-end van Service Fabric moet u eerst een actieve service maken die aanvragen kan accepteren.  

Maak een elementaire stateless ASP.NET Core betrouw bare service met behulp van de standaard web-API-project sjabloon. Hiermee maakt u een HTTP-eindpunt voor uw service, die u beschikbaar maakt via Azure API Management.

Start Visual Studio als beheerder en maak een ASP.NET Core-service:

 1. Selecteer in Visual Studio File -> New Project.
 2. Selecteer de sjabloon Service Fabric Application onder Cloud en geef deze de naam **'ApiApplication'**.
 3. Selecteer de sjabloon voor een stateless ASP.NET Core-service en geef het project de naam **'WebApiService'**.
 4. Selecteer de sjabloon Web-API ASP.NET Core 2,1-project.
 5. Als het project is gemaakt, opent u `PackageRoot\ServiceManifest.xml` en verwijdert u het `Port` kenmerk uit de configuratie van het eindpunt voor de resource:

    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    Als u de poort verwijdert, kan Service Fabric dynamisch een poort opgeven op basis van het toepassings poort bereik, die wordt geopend via de netwerk beveiligings groep in de cluster resource manager-sjabloon, waardoor verkeer vanaf API Management kan stromen.

 6. Druk in Visual Studio op F5 om te controleren of de web-API lokaal beschikbaar is.

    Open Service Fabric Explorer en zoom in op een specifiek exemplaar van de ASP.NET Core-service om te zien op welk basisadres de service luistert. Voeg `/api/values` toe aan het basisadres en open dit adres in een browser om zo de Get-methode aan te roepen voor de ValuesController in de sjabloon voor de web-API. Het resultaat van de aanroep is de standaardreactie die door de sjabloon wordt verstuurd, te weten een JSON-matrix die twee tekenreeksen bevat:

    ```json
    ["value1", "value2"]`
    ```

    Dit is het eindpunt dat u via API Management in Azure beschikbaar maakt.

 7. Ten slotte implementeer u de toepassing in het cluster in Azure. Klik in Visual Studio met de rechtermuisknop op het toepassingsproject en selecteer **Publish**. Geef het eindpunt van het cluster op (bijvoorbeeld `mycluster.southcentralus.cloudapp.azure.com:19000`) om de toepassing te implementeren in uw Service Fabric-cluster in Azure.

Als het goed is, wordt er nu een stateless ASP.NET Core-service met de naam `fabric:/ApiApplication/WebApiService` uitgevoerd in het Service Fabric-cluster in Azure.

## <a name="download-and-understand-the-resource-manager-templates"></a>Resource Manager-sjablonen downloaden en begrijpen

Download de volgende Resource Manager-sjablonen en parameterbestanden en sla ze op:

* [network-apim.json][network-arm]
* [network-apim.parameters.json][network-parameters-arm]
* [apim.json][apim-arm]
* [apim.parameters.json][apim-parameters-arm]

Met de sjabloon *netwerk apim.json* implementeert u een nieuw subnet en een nieuwe netwerkbeveiligingsgroep in het virtuele netwerk, waarop het Service Fabric-cluster wordt geïmplementeerd.

In de volgende secties worden de resources beschreven die worden gedefinieerd met de sjabloon *apim.json*. Voor meer informatie kunt u de koppelingen naar de naslagdocumentatie voor sjablonen volgen die aan elke sectie zijn toegevoegd. De configureerbare parameters die zijn gedefinieerde in het parametersbestand *apim.parameters.json* worden verderop in dit artikel besproken.

### <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service) beschrijft het exemplaar van de API Management-service: naam, SKU of laag, locatie voor resourcegroep, informatie over de uitgever en het virtuele netwerk.

### <a name="microsoftapimanagementservicecertificates"></a>Microsoft.ApiManagement/service/certificates

[Microsoft.ApiManagement/service/certificates](/azure/templates/microsoft.apimanagement/service/certificates) zorgt voor de configuratie van de beveiliging van API Management. API Management moet voor de detectie van services worden geverifieerd bij uw Service Fabric-cluster. Dit gebeurt met behulp van een clientcertificaat dat toegang tot het cluster heeft. In dit artikel wordt hetzelfde certificaat gebruikt dat u eerder hebt opgegeven bij het maken van het [Windows-cluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor), dat standaard kan worden gebruikt om toegang te krijgen tot uw cluster.

In dit artikel wordt hetzelfde certificaat gebruikt voor client verificatie en cluster knooppunt-naar-knoop punt beveiliging. U kunt een afzonderlijk clientcertificaat gebruiken als u een certificaat hebt geconfigureerd voor toegang tot uw Service Fabric-cluster. Geef de waarden voor **name**, **password** en **data** (met base-64 gecodeerde tekenreeks) op uit het bestand met de persoonlijke sleutel (.pfx) van het clustercertificaat dat u hebt opgegeven tijdens het maken van uw Service Fabric-cluster.

### <a name="microsoftapimanagementservicebackends"></a>Microsoft.ApiManagement/service/backends

[Microsoft.ApiManagement/service/backends](/azure/templates/microsoft.apimanagement/service/backends) beschrijft de back-endservice waarnaar verkeer wordt doorgestuurd.

Voor back-ends van Service Fabric is het Service Fabric-cluster de back-end in plaats van een specifieke Service Fabric-service. Hierdoor kan met één beleid verkeer worden omgeleid naar meer dan één service in het cluster. Het veld **url** hier is een volledig gekwalificeerde servicenaam van een service in het cluster waarnaar alle aanvragen standaard worden doorgestuurd als er geen servicenaam is opgegeven in een back-endbeleid. U kunt een niet-bestaande naam gebruiken voor de service, zoals 'fabric:/fake/service' als u geen behoefte hebt aan een fallback-service. **resourceId** verwijst naar het eindpunt voor clusterbeheer.  **clientCertificateThumbprint** en **serverCertificateThumbprints** zijn de certificaten die worden gebruikt voor verificatie met het cluster.

### <a name="microsoftapimanagementserviceproducts"></a>Microsoft.ApiManagement/service/products

Met [Microsoft.ApiManagement/service/products](/azure/templates/microsoft.apimanagement/service/products) wordt een product gemaakt. In Azure API Management bevat een product een of meer API's, evenals een gebruiksquotum en de gebruiksvoorwaarden. Zodra een product is gepubliceerd, kunnen ontwikkelaars zich abonneren op het product en de API's van het product gaan gebruiken.

Geef beschrijvende waarden op voor het product bij **displayName** en **description**. Voor dit artikel is een abonnement vereist, maar het goed keuren van een abonnement door een beheerder is niet.  De **state** van dit product is 'published' en het product is dus zichtbaar voor abonnees.

### <a name="microsoftapimanagementserviceapis"></a>Microsoft.ApiManagement/service/apis

Met [Microsoft.ApiManagement/service/apis](/azure/templates/microsoft.apimanagement/service/apis) wordt een API gemaakt. Een API in API Management vertegenwoordigt een reeks bewerkingen die kunnen worden aangeroepen door clienttoepassingen. Nadat de bewerkingen zijn toegevoegd, wordt de API toegevoegd aan een product en is deze klaar voor publicatie. Als een API is gepubliceerd, kunnen ontwikkelaars zich abonneren op de API en deze gebruiken.

* **displayName** kan elke naam zijn voor uw API. Gebruik voor dit artikel ' Service Fabric-app '.
* **name** is een unieke en beschrijvende naam voor de API, zoals 'service-fabric-app'. Deze naam wordt weergegeven in de portals ontwikkelaars en de uitgever.
* **serviceUrl** verwijst naar de HTTP-service die de API implementeert. API Management stuurt aanvragen door naar dit adres. De waarde van deze URL wordt niet gebruikt voor Service Fabric-back-ends. U kunt hier elke waarde invoeren. Voor dit artikel, bijvoorbeeld ' http: \/ /servicefabric '.
* De waarde voor **path** wordt toegevoegd aan de basis-URL voor de API Management-service. De basis-URL is gemeenschappelijk voor alle API's die worden gehost door een exemplaar van API Management-service. In API Management worden API's herkend aan hun achtervoegsel en daarom moet het achtervoegsel uniek zijn voor elke API voor een bepaalde uitgever.
* **protocols** bepaalt welke protocollen kunnen worden gebruikt om toegang te krijgen tot de API. Voor dit artikel, lijst **http** en **https**.
* **path** is een achtervoegsel voor de API. Gebruik voor dit artikel ' Mijntoep '.

### <a name="microsoftapimanagementserviceapisoperations"></a>Microsoft.ApiManagement/service/apis/operations

[Microsoft.ApiManagement/service/apis/operations](/azure/templates/microsoft.apimanagement/service/apis/operations) Voordat een API in API Management kan worden gebruikt, moeten er bewerkingen worden toegevoegd aan de API.  Externe clients gebruiken een bewerking om te communiceren met de stateless service van ASP.NET Core staatloze die wordt uitgevoerd in het Service Fabric-cluster.

Geef deze waarden op als u een API-bewerking voor de front-end wilt toevoegen:

* **displayName** en **description** beschrijven de bewerking. Gebruik voor dit artikel ' Values '.
* **method** is het HTTP-woord.  Geef voor dit artikel **Get** op.
* **urlTemplate** wordt toegevoegd aan de basis-URL van de API en identificeert één HTTP-bewerking.  Voor dit artikel gebruikt `/api/values` u als u de .net-back-end-service hebt toegevoegd of `getMessage` Als u de Java-back-end-service hebt toegevoegd.  De standaardinstelling is dat het URL-pad dat hier wordt opgegeven, het URL-pad is dat naar de service van Service Fabric in de back-end wordt verzonden. Als u hier het URL-pad opgeeft dat ook door de service wordt gebruikt, zoals '/api/values', werkt de bewerking zonder verdere aanpassingen. U kunt hier ook een URL-pad opgeven dat verschilt van het URL-pad dat wordt gebruikt door de service van Service Fabric in de back-end. In dat geval moet u later ook een opdracht voor wijziging van het pad opgeven in het beleid voor de bewerking.

### <a name="microsoftapimanagementserviceapispolicies"></a>Microsoft.ApiManagement/service/apis/policies

Met [Microsoft.ApiManagement/service/apis/policies](/azure/templates/microsoft.apimanagement/service/apis/policies) wordt een back-endbeleid gemaakt, waarmee alles met elkaar wordt verbonden. Dit is de plek waar u de service van Service Fabric in de back-end configureert waarnaar aanvragen worden doorgestuurd. U kunt dit beleid toepassen op elke API-bewerking.  Zie het [beleidsoverzicht](../api-management/api-management-howto-policies.md) voor meer informatie.

De [back-endconfiguratie voor Service Fabric](../api-management/api-management-transformation-policies.md#SetBackendService) biedt de volgende onderdelen voor het doorsturen van aanvragen:

* Selectie van service-exemplaar door de naam van een exemplaar van een Service Fabric-service op te geven. Deze naam kan programmatisch worden vastgelegd (bijvoorbeeld `"fabric:/myapp/myservice"`) of worden gegenereerd vanuit de HTTP-aanvraag (bijvoorbeeld `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
* Partitie-omzetting door het genereren van een partitiesleutel met behulp van een partitieschema van Service Fabric.
* Replicaselectie voor stateful services.
* Voorwaarden voor opnieuw uitvoeren van omzetting waarmee u de voorwaarden kunt opgeven voor het opnieuw omzetten van een servicelocatie en het opnieuw verzenden van een aanvraag.

**policyContent** bevat de XML-inhoud van het beleid, met Json-escape.  Voor dit artikel maakt u een back-end-beleid om aanvragen rechtstreeks naar de .NET-of Java-stateless service te routeren die eerder zijn geïmplementeerd. Voeg een beleid `set-backend-service` onder inbound policies.  Vervang de waarde voor *sf-service-instance-name* door `fabric:/ApiApplication/WebApiService` als u eerder de .NET back-endservice hebt geïmplementeerd of door `fabric:/EchoServerApplication/EchoServerService` als u de Java-service hebt geïmplementeerd.  *backend-id* verwijst naar een resource in de back-end, in dit geval de resource `Microsoft.ApiManagement/service/backends` die is gedefinieerd in de sjabloon *apim.json*. *backend-id* kan ook verwijzen naar een andere back-endresource die is gemaakt met behulp van de API's van API Management. Voor dit artikel stelt u de *back-end-id* in op de waarde van de para meter *service_fabric_backend_name* .

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Raadpleeg de [documentatie over back-ends voor API Management](../api-management/api-management-transformation-policies.md#SetBackendService) voor een overzicht van alle beleidskenmerken voor Service Fabric-back-ends.

## <a name="set-parameters-and-deploy-api-management"></a>Parameters instellen en API Management implementeren

Vul in *apim.parameters.json* de volgende lege parameters in voor uw implementatie.

|Parameter|Waarde|
|---|---|
|apimInstanceName|sf-apim|
|apimPublisherEmail|myemail@contosos.com|
|apimSku|Ontwikkelaar|
|serviceFabricCertificateName|sfclustertutorialgroup320171031144217|
|certificatePassword|q6D7nN%6ck@6|
|serviceFabricCertificateThumbprint|C4C1E541AD512B8065280292A8BA6079C3F26F10 |
|serviceFabricCertificate|&lt;base-64 encoded string&gt;|
|url_path|/api/values|
|clusterHttpManagementEndpoint|`https://mysfcluster.southcentralus.cloudapp.azure.com:19080`|
|inbound_policy|&lt;XML-tekenreeks&gt;|

De waarden voor *certificatePassword* en *serviceFabricCertificateThumbprint* moeten overeenkomen met het clustercertificaat dat is gebruikt voor het instellen van het cluster.

*serviceFabricCertificate* is het certificaat als een met base64 gecodeerde tekenreeks, die kan worden gegenereerd met het volgende script:

```powershell
$bytes = [System.IO.File]::ReadAllBytes("C:\mycertificates\sfclustertutorialgroup220171109113527.pfx");
$b64 = [System.Convert]::ToBase64String($bytes);
[System.Io.File]::WriteAllText("C:\mycertificates\sfclustertutorialgroup220171109113527.txt", $b64);
```

Vervang bij *inbound_policy* de waarde voor *sf-service-instance-name* door `fabric:/ApiApplication/WebApiService` als u eerder de .NET back-endservice hebt geïmplementeerd of door `fabric:/EchoServerApplication/EchoServerService` als u de Java-service hebt geïmplementeerd. *backend-id* verwijst naar een resource in de back-end, in dit geval de resource `Microsoft.ApiManagement/service/backends` die is gedefinieerd in de sjabloon *apim.json*. *backend-id* kan ook verwijzen naar een andere back-endresource die is gemaakt met behulp van de API's van API Management. Voor dit artikel stelt u de *back-end-id* in op de waarde van de para meter *service_fabric_backend_name* .

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Gebruik het volgende script voor het implementeren van de Resource Manager-sjabloon en de parameterbestanden voor API Management:

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
$templatepath="C:\clustertemplates"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\network-apim.json" -TemplateParameterFile "$templatepath\network-apim.parameters.json" -Verbose

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\apim.json" -TemplateParameterFile "$templatepath\apim.parameters.json" -Verbose
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group deployment create --name ApiMgmtNetworkDeployment --resource-group $ResourceGroupName --template-file network-apim.json --parameters @network-apim.parameters.json

az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json
```

## <a name="test-it"></a>Testen

U kunt nu proberen om rechtstreeks vanuit [Azure Portal](https://portal.azure.com) via API Management een aanvraag te verzenden naar uw back-endservice in Service Fabric.

 1. Selecteer **API** in de API Management-service.
 2. Selecteer in de **Service Fabric App**-API die u hebt gemaakt in de vorige stappen het tabblad **Test** en vervolgens de bewerking **Waarden**.
 3. Klik op de knop **Verzenden** om een testaanvraag te verzenden naar de back-endservice.  U ziet een HTTP-antwoord van deze strekking:

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Ocp-Apim-Trace-Location: https://apimgmtstodhwklpry2xgkdj.blob.core.windows.net/apiinspectorcontainer/PWSQOq_FCDjGcaI1rdMn8w2-2?sv=2015-07-08&sr=b&sig=MhQhzk%2FEKzE5odlLXRjyVsgzltWGF8OkNzAKaf0B1P0%3D&se=2018-01-28T01%3A04%3A44Z&sp=r&traceId=9f8f1892121e445ea1ae4d2bc8449ce4

    Date: Sat, 27 Jan 2018 01:04:44 GMT


    ["value1", "value2"]
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Een cluster bevat de clusterresource zelf én andere Azure-resources. De eenvoudigste manier om het cluster en alle resources te verwijderen, is om de resourcegroep te verwijderen.

Meld u aan bij Azure en selecteer de abonnements-id waarmee u het cluster wilt verwijderen.  U kunt uw abonnements-id vinden door u aan te melden bij [Azure Portal](https://portal.azure.com). Verwijder de resource groep en alle cluster bronnen met behulp van de [cmdlet Remove-AzResourceGroup](/en-us/powershell/module/az.resources/remove-azresourcegroup).

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van [API Management](../api-management/import-and-publish.md).

[azure-powershell]: /powershell/azure/

[apim-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.json
[apim-parameters-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.parameters.json

[network-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.json
[network-parameters-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.parameters.json

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
Vice-Fabric-scripts-en-sjablonen/BLOB/Master/templates/Service-integratie/network-apim.parameters.jsopn

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
