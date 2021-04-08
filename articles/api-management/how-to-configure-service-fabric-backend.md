---
title: Service Fabric back-end instellen in azure API Management | Microsoft Docs
description: Een Service Fabric service-back-end maken in azure API Management met behulp van de Azure Portal
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.openlocfilehash: f6474dbd02c501612b951ddae490385a5d843fbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99500628"
---
# <a name="set-up-a-service-fabric-backend-in-api-management-using-the-azure-portal"></a>Een Service Fabric back-end instellen in API Management met behulp van de Azure Portal

In dit artikel wordt beschreven hoe u een [service Fabric](../service-fabric/service-fabric-api-management-overview.md) -service configureert als een aangepaste API-back-end met behulp van de Azure Portal. Voor demonstratie doeleinden ziet u hoe u een eenvoudige, stateless ASP.NET Core betrouw bare service kunt instellen als de Service Fabric back-end.

Zie voor achtergrond back-ends [in API Management](backends.md).

## <a name="prerequisites"></a>Vereisten

Vereisten voor het configureren van een voorbeeld service in een Service Fabric cluster met Windows als een aangepaste back-end:

* **Windows-ontwikkel omgeving** : Installeer [Visual Studio 2019](https://www.visualstudio.com) en de ontwikkelings-, **ASP.net-en Web**-ontwikkeling van **Azure** en het ontwikkelen van **.net core-** werk belastingen. Richt vervolgens een [.NET-ontwikkelomgeving in](../service-fabric/service-fabric-get-started.md).

* **Service Fabric cluster** -Zie [zelf studie: een service Fabric cluster met Windows implementeren in een virtueel Azure-netwerk](../service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster.md). U kunt een cluster maken met een bestaand X. 509-certificaat of voor test doeleinden een nieuw, zelfondertekend certificaat maken. Het cluster wordt gemaakt in een virtueel netwerk.

* Voor **beeld service Fabric app** : Maak een web-API-app en implementeer deze op het service Fabric cluster zoals beschreven in [API Management integreren met Service fabric in azure](../service-fabric/service-fabric-tutorial-deploy-api-management.md).

    Met deze stappen wordt een eenvoudige, stateless ASP.NET Core betrouw bare service gemaakt met behulp van de standaard web-API-project sjabloon. Later maakt u het HTTP-eind punt voor deze service beschikbaar via Azure API Management.

    Noteer de naam van de toepassing, bijvoorbeeld `fabric:/myApplication/myService` . 

* **API Management exemplaar** : een bestaande of nieuwe API Management instantie in de laag **Premium** of  **Developer** en in dezelfde regio als het service Fabric cluster. Als u er één nodig hebt, moet u [een API Management-exemplaar maken](get-started-create-service-instance.md).

* **Virtueel netwerk** : voeg uw API Management-exemplaar toe aan het virtuele netwerk dat u voor uw service Fabric cluster hebt gemaakt. API Management vereist een toegewezen subnet in het virtuele netwerk.

  Zie [Azure API Management gebruiken met virtuele netwerken](api-management-using-with-vnet.md)voor stappen voor het inschakelen van virtuele netwerk connectiviteit voor het API Management-exemplaar.

## <a name="create-backend---portal"></a>Back-end maken-Portal

### <a name="add-service-fabric-cluster-certificate-to-api-management"></a>Service Fabric cluster certificaat toevoegen aan API Management

Het Service Fabric cluster certificaat wordt opgeslagen en beheerd in een Azure-sleutel kluis die is gekoppeld aan het cluster. Voeg dit certificaat toe aan uw API Management-exemplaar als een client certificaat.

Zie [back-end-services beveiligen met verificatie op basis van client certificaten in Azure API Management](api-management-howto-mutual-certificates.md)voor stappen om een certificaat toe te voegen aan uw API Management-exemplaar. 

> [!NOTE]   
> Het is raadzaam om het certificaat toe te voegen aan API Management door te verwijzen naar het sleutel kluis certificaat. 

### <a name="add-service-fabric-backend"></a>Service Fabric back-end toevoegen

1. Blader in [Azure Portal](https://portal.azure.com) naar uw API Management-exemplaar.
1. Onder **api's** selecteert u **back**-ends  >  **+ toevoegen**.
1. Voer een back-end-naam en een optionele beschrijving in
1. Selecteer in **Type** **service Fabric**.
1. Voer in **runtime-URL** de naam in van de service Fabric back-end-service die door API Management aanvragen worden doorgestuurd naar. Bijvoorbeeld: `fabric:/myApplication/myService`. 
1. Voer een getal tussen 0 en 10 in voor het **maximum aantal nieuwe pogingen voor het omzetten van partities**.
1. Voer het beheer eindpunt van het Service Fabric cluster in. Dit eind punt is de URL van het cluster op poort `19080` , bijvoorbeeld `https://mysfcluster.eastus.cloudapp.azure.com:19080` .
1. Selecteer in **client certificaat** het service Fabric cluster certificaat dat u in de vorige sectie aan uw API Management-exemplaar hebt toegevoegd.
1. Voer bij **beheer eindpunt autorisatie methode** een vinger afdruk of een server x509-naam in van een certificaat dat wordt gebruikt door de service Fabric Cluster beheer service voor TLS-communicatie.
1. Schakel de instellingen **certificaat keten valideren** en **certificaat naam valideren** in.
1. Geef, indien nodig, referenties op bij **autorisatie referenties** om de geconfigureerde back-end-service in service Fabric te bereiken. Voor de voor beeld-app die in dit scenario wordt gebruikt, zijn autorisatie referenties niet nodig.
1. Selecteer **Maken**.

:::image type="content" source="media/backends/create-service-fabric-backend.png" alt-text="Een service Fabric-back-end maken":::

## <a name="use-the-backend"></a>De back-end gebruiken

Als u een aangepaste back-end wilt gebruiken, moet u ernaar verwijzen met het [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) beleid. Met dit beleid wordt de standaard back-end-service basis-URL van een binnenkomende API-aanvraag naar een opgegeven back-end getransformeerd, in dit geval de Service Fabric back-end. 

Het `set-backend-service` beleid kan nuttig zijn met een bestaande API om een binnenkomende aanvraag te transformeren naar een andere backend dan de back-end die is opgegeven in de API-instellingen. Voor demonstratie doeleinden in dit artikel maakt u een test-API en stelt u het beleid in om API-aanvragen om te leiden naar de Service Fabric back-end. 

### <a name="create-api"></a>API maken

Volg de stappen in [een API hand matig toevoegen](add-api-manually.md) om een lege API te maken.

* Laat de URL van de **webservice** leeg in de API-instellingen.
* Voeg een **API-URL-achtervoegsel** toe, zoals *infra structuur*.

  :::image type="content" source="media/backends/create-blank-api.png" alt-text="Een lege API maken":::

### <a name="add-get-operation-to-the-api"></a>GET-bewerking toevoegen aan de API

Zoals wordt weer gegeven in [een service Fabric back-end implementeren](../service-fabric/service-fabric-tutorial-deploy-api-management.md#deploy-a-service-fabric-back-end-service), ondersteunt de voor beeld ASP.net core-service die is geïmplementeerd op het service Fabric cluster, een enkele HTTP Get-bewerking op het URL-pad `/api/values` .

De standaard reactie op dat pad is een JSON-matrix van twee teken reeksen:

```json
["value1", "value2"]
```

Als u de integratie van API Management met het cluster wilt testen, voegt u de bijbehorende GET-bewerking toe aan de API op het pad `/api/values` :

1. Selecteer de API die u in de vorige stap hebt gemaakt.
1. Klik op **+ Bewerking toevoegen**.
1. Voer in het venster **frontend** de volgende waarden in en selecteer **Opslaan**.

     | Instelling             | Waarde                             | 
    |---------------------|-----------------------------------|
    | **Weergavenaam**    | *Back-end testen*                       |  
    | **URL** | GET                               | 
    | **URL**             | `/api/values`                           | 
    
    :::image type="content" source="media/backends/configure-get-operation.png" alt-text="GET-bewerking toevoegen aan API":::

### <a name="configure-set-backend-policy"></a>`set-backend`Beleid configureren

Voeg het [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) beleid toe aan de test-API.

1. Op het tabblad **ontwerp** , in de sectie **binnenkomende verwerking** , selecteert u het pictogram code-editor ( **</>** ). 
1. De cursor in het **&lt; inkomende &gt;** element plaatsen
1. Voeg de volgende beleids instructie toe. `backend-id`Vervang in de naam van uw service Fabric back-end.

   Het `sf-resolve-condition` is een voor waarde voor opnieuw proberen als de cluster partitie niet wordt opgelost. Het aantal nieuwe pogingen is ingesteld bij het configureren van de back-end.

    ```xml
    <set-backend-service backend-id="mysfbackend" sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")"  />
    ```
1. Selecteer **Opslaan**.

    :::image type="content" source="media/backends/set-backend-service.png" alt-text="Set-back-end-service beleid configureren":::

### <a name="test-backend-api"></a>Back-end-API testen

1. Selecteer op het tabblad **testen** de **Get** -bewerking die u in een vorige sectie hebt gemaakt.
1. Selecteer **Verzenden**.

Wanneer het HTTP-antwoord correct is geconfigureerd, ziet u een HTTP-succes code en wordt de JSON weer gegeven die is geretourneerd door de back-end-Service Fabric service.

:::image type="content" source="media/backends/test-backend-service.png" alt-text="Service Fabric back-end testen":::

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [configureren van beleids regels](api-management-advanced-policies.md) voor het door sturen van aanvragen naar een back-end
* Back-ends kunnen ook worden geconfigureerd met behulp van de API Management [rest API](/rest/api/apimanagement/2020-06-01-preview/backend), [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)of [Azure Resource Manager sjablonen](../service-fabric/service-fabric-tutorial-deploy-api-management.md)

