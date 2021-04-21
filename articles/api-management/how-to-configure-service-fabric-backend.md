---
title: Een back-Service Fabric in Azure API Management | Microsoft Docs
description: Een back-Service Fabric voor een API Management-service maken in Azure Azure Portal
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3dda6f18c2bf92b537c2f4be1c6a0a3b70cdc28a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815878"
---
# <a name="set-up-a-service-fabric-backend-in-api-management-using-the-azure-portal"></a>Een back-Service Fabric in de API Management met behulp van de Azure Portal

In dit artikel wordt beschreven hoe u een [Service Fabric-service](../service-fabric/service-fabric-api-management-overview.md) configureert als een aangepaste API-back-end met behulp van de Azure Portal. Voor demonstratiedoeleinden ziet u hoe u een eenvoudige stateless ASP.NET Core Reliable Service als back-Service Fabric instellen.

Zie [Back-API Management voor achtergrondinformatie.](backends.md)

## <a name="prerequisites"></a>Vereisten

Vereisten voor het configureren van een voorbeeldservice in Service Fabric cluster met Windows als een aangepaste back-end:

* **Windows-ontwikkelomgeving:** installeer [Visual Studio 2019](https://www.visualstudio.com) en de **workloads** Azure-ontwikkeling, **ASP.NET-** en webontwikkeling en **.NET Core platformoverschrijdende** ontwikkeling. Richt vervolgens een [.NET-ontwikkelomgeving in](../service-fabric/service-fabric-get-started.md).

* **Service Fabric cluster -** Zie [Zelfstudie: Een Service Fabric cluster met Windows implementeren in een virtueel Azure-netwerk.](../service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster.md) U kunt een cluster maken met een bestaand X.509-certificaat of voor testdoeleinden een nieuw, zelf-ondertekend certificaat maken. Het cluster wordt gemaakt in een virtueel netwerk.

* **Voorbeeld van Service Fabric-app:** maak een web-API-app en implementeer deze in het Service Fabric-cluster, zoals beschreven in Integratie van API Management [met Service Fabric in Azure.](../service-fabric/service-fabric-tutorial-deploy-api-management.md)

    Met deze stappen maakt u een eenvoudige stateless ASP.NET Core Reliable Service met behulp van de standaard web-API-projectsjabloon. Later maakt u het HTTP-eindpunt voor deze service beschikbaar via Azure API Management.

    Noteer de naam van de toepassing, bijvoorbeeld `fabric:/myApplication/myService` . 

* **API Management-exemplaar:** een bestaand of nieuw API Management-exemplaar in de **Premium-** of  **Developer-laag** en in dezelfde regio als het Service Fabric cluster. Als u er een nodig hebt, [maakt u een API Management-exemplaar](get-started-create-service-instance.md).

* **Virtueel netwerk:** voeg uw API Management-exemplaar toe aan het virtuele netwerk dat u hebt gemaakt voor Service Fabric cluster. API Management vereist een toegewezen subnet in het virtuele netwerk.

  Zie How to use Azure API Management with virtual networks (Azure API Management gebruiken met virtuele netwerken) voor stappen voor het inschakelen van virtuele [netwerkconnectiviteit voor het API Management-exemplaar.](api-management-using-with-vnet.md)

## <a name="create-backend---portal"></a>Back-end maken - portal

### <a name="add-service-fabric-cluster-certificate-to-api-management"></a>Een Service Fabric toevoegen aan API Management

Het Service Fabric clustercertificaat wordt opgeslagen en beheerd in een Azure-sleutelkluis die aan het cluster is gekoppeld. Voeg dit certificaat toe aan API Management-exemplaar als een clientcertificaat.

Zie How [to secure backend services using client certificate authentication in Azure](api-management-howto-mutual-certificates.md)API Management (Back-API Management services beveiligen met clientcertificaatverificatie in Azure API Management) voor stappen voor het toevoegen van een API Management. 

> [!NOTE]   
> U kunt het certificaat het beste API Management door te verwijzen naar het sleutelkluiscertificaat. 

### <a name="add-service-fabric-backend"></a>Back-Service Fabric toevoegen

1. Blader in [Azure Portal](https://portal.azure.com) naar uw API Management-exemplaar.
1. Selecteer **onder API's** **de optie Back-enden**  >  **+ Toevoegen.**
1. Voer een back-naam en een optionele beschrijving in
1. Selecteer **in Type** de optie **Service Fabric**.
1. Voer **in Runtime-URL** de naam in van Service Fabric back-API Management aanvragen doorsturen. Bijvoorbeeld: `fabric:/myApplication/myService`. 
1. Voer **bij Maximum aantal nieuwe partitieresoluties** een getal tussen 0 en 10 in.
1. Voer het beheer-eindpunt van het Service Fabric in. Dit eindpunt is de URL van het cluster op poort `19080` , bijvoorbeeld `https://mysfcluster.eastus.cloudapp.azure.com:19080` .
1. Selecteer **in Clientcertificaat** het Service Fabric clustercertificaat dat u hebt toegevoegd aan API Management-exemplaar in de vorige sectie.
1. Voer **in Autorisatiemethode voor** beheer-eindpunt een vingerafdruk of X509-servernaam in van een certificaat dat wordt gebruikt door de Service Fabric-clusterbeheerservice voor TLS-communicatie.
1. Schakel de instellingen **Certificaatketen valideren** en **Certificaatnaam valideren** in.
1. Geef **in Autorisatiereferenties** indien nodig referenties op om de geconfigureerde back-Service Fabric. Voor de voorbeeld-app die in dit scenario wordt gebruikt, zijn geen autorisatiereferenties nodig.
1. Selecteer **Maken**.

:::image type="content" source="media/backends/create-service-fabric-backend.png" alt-text="Een Service Fabric-back-end maken":::

## <a name="use-the-backend"></a>De back-end gebruiken

Als u een aangepaste back-end wilt gebruiken, verwijst u er naar met behulp van het [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) beleid. Met dit beleid wordt de standaard basis-URL van de back-endservice van een binnenkomende API-aanvraag omgezet in een opgegeven back-Service Fabric back-end. 

Het beleid kan handig zijn met een bestaande API om een binnenkomende aanvraag te transformeren naar een andere back-end dan de aanvraag die is opgegeven `set-backend-service` in de API-instellingen. Voor demonstratiedoeleinden in dit artikel maakt u een test-API en stelt u het beleid in om API-aanvragen naar de back-Service Fabric te sturen. 

### <a name="create-api"></a>API maken

Volg de stappen in [Een API handmatig toevoegen om](add-api-manually.md) een lege API te maken.

* Laat in de API-instellingen de **URL van de webservice** leeg.
* Voeg een **API-URL-achtervoegsel** toe, zoals *fabric*.

  :::image type="content" source="media/backends/create-blank-api.png" alt-text="Een lege API maken":::

### <a name="add-get-operation-to-the-api"></a>GET-bewerking toevoegen aan de API

Zoals weergegeven in Deploy a Service Fabric back-end service (Een [Service Fabric-back-endservice](../service-fabric/service-fabric-tutorial-deploy-api-management.md#deploy-a-service-fabric-back-end-service)implementeren) ondersteunt het voorbeeld van de ASP.NET Core-service die is geïmplementeerd op het Service Fabric-cluster, één HTTP GET-bewerking op het URL-pad `/api/values` .

Het standaardreactie op dat pad is een JSON-matrix van twee tekenreeksen:

```json
["value1", "value2"]
```

Als u de integratie van API Management met het cluster wilt testen, voegt u de bijbehorende GET-bewerking toe aan de API op het pad `/api/values` :

1. Selecteer de API die u in de vorige stap hebt gemaakt.
1. Klik op **+ Bewerking toevoegen**.
1. Voer in **het venster Front-end** de volgende waarden in en selecteer **Opslaan.**

     | Instelling             | Waarde                             | 
    |---------------------|-----------------------------------|
    | **Weergavenaam**    | *Back-end testen*                       |  
    | **URL** | GET                               | 
    | **URL**             | `/api/values`                           | 
    
    :::image type="content" source="media/backends/configure-get-operation.png" alt-text="GET-bewerking toevoegen aan API":::

### <a name="configure-set-backend-policy"></a>Beleid `set-backend` configureren

Voeg het [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) beleid toe aan de test-API.

1. Selecteer op **het** tabblad Ontwerpen in **de sectie Binnenkomende verwerking** het pictogram code-editor ( **</>** ). 
1. Plaats de cursor in het **&lt; binnenkomende &gt;** element
1. Voeg de volgende beleidsverklaring toe. Vervang `backend-id` in de naam van uw Service Fabric back-end.

   De `sf-resolve-condition` is een voorwaarde voor opnieuw proberen als de clusterpartitie niet is opgelost. Het aantal nieuwe keren dat is ingesteld bij het configureren van de back-end.

    ```xml
    <set-backend-service backend-id="mysfbackend" sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")"  />
    ```
1. Selecteer **Opslaan**.

    :::image type="content" source="media/backends/set-backend-service.png" alt-text="Set-backend-servicebeleid configureren":::

### <a name="test-backend-api"></a>Back-end-API testen

1. Selecteer op **het tabblad** Testen de **GET-bewerking** die u in een vorige sectie hebt gemaakt.
1. Selecteer **Verzenden**.

Wanneer deze correct is geconfigureerd, toont het HTTP-antwoord een HTTP-succescode en wordt de JSON weergegeven die is geretourneerd door de back-Service Fabric service.

:::image type="content" source="media/backends/test-backend-service.png" alt-text="Back-Service Fabric testen":::

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [configureren van beleid](api-management-advanced-policies.md) voor het doorsturen van aanvragen naar een back-end
* Back-enden kunnen ook worden geconfigureerd met behulp van de API Management [REST API,](/rest/api/apimanagement/2020-06-01-preview/backend) [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)of [Azure Resource Manager sjablonen](../service-fabric/service-fabric-tutorial-deploy-api-management.md)

