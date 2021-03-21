---
title: Privé-eind punten gebruiken om Azure Functions te integreren met een virtueel netwerk
description: Deze zelf studie laat zien hoe u een functie verbindt met een virtueel Azure-netwerk en deze kunt vergren delen met behulp van privé-eind punten.
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: 3dd5e700b3081f1c1ef8e4601385c707a5738321
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102630466"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-by-using-private-endpoints"></a>Zelf studie: Azure Functions integreren met een virtueel Azure-netwerk met behulp van privé-eind punten

Deze zelf studie laat zien hoe u Azure Functions kunt gebruiken om verbinding te maken met resources in een virtueel Azure-netwerk met behulp van privé-eind punten. U maakt een functie met behulp van een opslag account dat is vergrendeld achter een virtueel netwerk. Het virtuele netwerk maakt gebruik van een service bus-wachtrij trigger.

In deze zelfstudie gaat u:

> [!div class="checklist"]
> * Een functie-app maken in het Premium-abonnement.
> * Maak Azure-resources, zoals de service bus, het opslag account en het virtuele netwerk.
> * Vergrendel uw opslag account achter een persoonlijk eind punt.
> * Vergrendel uw service bus achter een persoonlijk eind punt.
> * Implementeer een functie-app die gebruikmaakt van de service bus en HTTP-triggers.
> * Vergrendel uw functie-app achter een persoonlijk eind punt.
> * Test om te zien of uw functie-app is beveiligd in het virtuele netwerk.
> * Resources opschonen.

## <a name="create-a-function-app-in-a-premium-plan"></a>Een functie-app maken in een Premium-abonnement

U maakt een .NET-functie-app in het Premium-abonnement, omdat deze zelf studie C# gebruikt. Andere talen worden ook ondersteund in Windows. Het Premium-abonnement biedt een serverloze schaal en ondersteunt de integratie van virtuele netwerken.

1. Selecteer in het menu Azure Portal of de **Start** pagina de optie **een resource maken**.

1. Selecteer op de pagina **Nieuw** de optie **reken**  >  **functie-app**.

1. Gebruik op de pagina **basis beginselen** de volgende tabel om de instellingen van de functie-app te configureren.

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe functie-app wordt gemaakt. |
    | **[Resourcegroep](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Naam voor de nieuwe resource groep waar u de functie-app gaat maken. |
    | **Naam van de functie-app** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app wordt aangeduid. Geldige tekens zijn `a-z` (hoofdlettergevoelig), `0-9` en `-`.  |
    |**Publiceren**| Code | Kies voor het publiceren van code bestanden of een docker-container. |
    | **Runtimestack** | .NET | In deze zelf studie wordt .NET gebruikt. |
    |**Regio**| Voorkeursregio | Kies een [regio](https://azure.microsoft.com/regions/) in de buurt of in de buurt van andere services die door uw functies worden geopend. |

1. Selecteer **Volgende: Hosting**. Voer op de pagina **Hosting** de volgende instellingen in.

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **[Opslagaccount](../storage/common/storage-account-create.md)** |  Wereldwijd unieke naam |  Maak een opslagaccount die wordt gebruikt door uw functie-app. Namen van opslag accounts moeten tussen de 3 en 24 tekens lang zijn. Ze mogen alleen cijfers en kleine letters bevatten. U kunt ook een bestaand account gebruiken dat voldoet aan de [vereisten voor een opslagaccount](./storage-considerations.md#storage-account-requirements). |
    |**Besturingssysteem**| Windows | In deze zelf studie wordt Windows gebruikt. |
    | **[Plannen](./functions-scale.md)** | Premium | Hostingabonnement dat definieert hoe resources worden toegewezen aan uw functie-app. Wanneer u **Premium** selecteert, wordt standaard een nieuw app service plan gemaakt. De standaard- **SKU en-grootte** is **EP1**, waarbij *EP* staat voor _elastisch Premium_. Zie de lijst met [Premium-sku's](./functions-premium-plan.md#available-instance-skus)voor meer informatie.<br/><br/>Wanneer u Java script-functies uitvoert op een Premium-abonnement, kiest u een instantie met minder Vcpu's. Zie voor meer informatie het artikel over [Premium-abonnementen met één core kiezen](./functions-reference-node.md#considerations-for-javascript-functions).  |

1. Selecteer **Volgende: Bewaking**. Voer op de pagina **Bewaking** de volgende instellingen in.

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | Standaard | Maak een Application Insights resource met dezelfde app-naam in de dichtstbijzijnde ondersteunde regio. Vouw deze instelling uit als u de **nieuwe resource naam** wilt wijzigen of uw gegevens op een andere **locatie** in een [Azure-geografie](https://azure.microsoft.com/global-infrastructure/geographies/)wilt opslaan. |

1. Selecteer **Beoordelen + maken** om de selecties van appconfiguratie te controleren.

1. Controleer uw instellingen op de pagina **controleren en maken** . Selecteer vervolgens **maken** om de functie-app in te richten en te implementeren.

1. Selecteer in de rechter bovenhoek van de portal het pictogram **meldingen** en Bekijk het bericht **implementatie voltooid** .

1. Selecteer **Naar de resource gaan** om uw nieuwe functie-app te bekijken. U kunt ook **Vastmaken aan dashboard** selecteren. Vastmaken maakt het gemakkelijker om terug te gaan naar deze functie-app-resource vanuit uw dashboard.

Gefeliciteerd U hebt uw Premium-functie-app gemaakt.

## <a name="create-azure-resources"></a>Azure-resources maken

Vervolgens maakt u een opslag account, een service bus en een virtueel netwerk. 
### <a name="create-a-storage-account"></a>Een opslagaccount maken

Voor uw virtuele netwerken is een opslag account vereist dat is gescheiden van de-app die u hebt gemaakt met uw functie-apps.

1. Selecteer in het menu Azure Portal of de **Start** pagina de optie **een resource maken**.

1. Zoek op de pagina **Nieuw** naar het *opslag account*. Selecteer vervolgens **Maken**.

1. Gebruik op het tabblad **basis beginselen** de volgende tabel om de instellingen voor het opslag account te configureren. Alle andere instellingen kunnen de standaard waarden gebruiken.

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe resources zijn gemaakt. | 
    | **[Resourcegroep](../azure-resource-manager/management/overview.md)**  | myResourceGroup | De resource groep die u hebt gemaakt met uw functie-app. |
    | **Naam** | mysecurestorage| De naam van het opslag account waarop het persoonlijke eind punt wordt toegepast. |
    | **[Regio](https://azure.microsoft.com/regions/)** | myFunctionRegion | De regio waar u de functie-app hebt gemaakt. |

1. Selecteer **Controleren + maken**. Nadat de validatie is voltooid, selecteert u **maken**.

### <a name="create-a-service-bus"></a>Een service bus maken

1. Selecteer in het menu Azure Portal of de **Start** pagina de optie **een resource maken**.

1. Zoek op de pagina **Nieuw** naar *Service Bus*. Selecteer vervolgens **Maken**.

1. Gebruik op het tabblad **basis beginselen** de volgende tabel om de service bus-instellingen te configureren. Alle andere instellingen kunnen de standaard waarden gebruiken.

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe resources zijn gemaakt. |
    | **[Resourcegroep](../azure-resource-manager/management/overview.md)**  | myResourceGroup | De resource groep die u hebt gemaakt met uw functie-app. |
    | **Naam** | myServiceBus| De naam van de service bus waarop het persoonlijke eind punt wordt toegepast. |
    | **[Regio](https://azure.microsoft.com/regions/)** | myFunctionRegion | De regio waar u de functie-app hebt gemaakt. |
    | **Prijscategorie** | Premium | Kies deze laag om privé-eind punten te gebruiken met Azure Service Bus. |

1. Selecteer **Controleren + maken**. Nadat de validatie is voltooid, selecteert u **maken**.

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

De Azure-resources in deze zelf studie kunnen worden geïntegreerd met of worden opgenomen in een virtueel netwerk. U gebruikt privé-eind punten om netwerk verkeer binnen het virtuele netwerk te bevatten.

In de zelf studie worden twee subnetten gemaakt:
- **default**: subnet voor privé-eind punten. Privé-IP-adressen worden via dit subnet verstrekt.
- **functions**: Subnet voor Azure functions integratie van virtuele netwerken. Dit subnet wordt overgedragen aan de functie-app.

Maak het virtuele netwerk waarmee de functie-app wordt geïntegreerd:

1. Selecteer in het menu Azure Portal of de **Start** pagina de optie **een resource maken**.

1. Zoek op de pagina **Nieuw** naar *virtueel netwerk*. Selecteer vervolgens **Maken**.

1. Gebruik op het tabblad **basis beginselen** de volgende tabel om de instellingen van het virtuele netwerk te configureren.

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe resources zijn gemaakt. | 
    | **[Resourcegroep](../azure-resource-manager/management/overview.md)**  | myResourceGroup | De resource groep die u hebt gemaakt met uw functie-app. |
    | **Naam** | myVirtualNet| De naam van het virtuele netwerk waarmee de functie-app verbinding maakt. |
    | **[Regio](https://azure.microsoft.com/regions/)** | myFunctionRegion | De regio waar u de functie-app hebt gemaakt. |

1. Selecteer **subnet toevoegen** op het tabblad **IP-adressen** . Gebruik de volgende tabel om de instellingen van het subnet te configureren.

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="Scherm opname van de configuratie weergave virtuele netwerk maken.":::

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Subnetnaam** | vervullen | De naam van het subnet waarmee uw functie-app verbinding maakt. | 
    | **Subnetadresbereik** | 10.0.1.0/24 | Het adres bereik van het subnet. In de vorige afbeelding ziet u dat de IPv4-adres ruimte 10.0.0.0/16 is. Als de waarde 10.1.0.0/16 is, zou het aanbevolen adres bereik van het subnet 10.1.1.0/24 zijn. |

1. Selecteer **Controleren + maken**. Nadat de validatie is voltooid, selecteert u **maken**.

## <a name="lock-down-your-storage-account"></a>Uw opslag account vergren delen

Persoonlijke Azure-eind punten worden gebruikt om verbinding te maken met specifieke Azure-resources met behulp van een privé-IP-adres. Deze verbinding zorgt ervoor dat het netwerk verkeer binnen het gekozen virtuele netwerk blijft en dat toegang alleen beschikbaar is voor specifieke bronnen. 

Maak de privé-eind punten voor Azure Files opslag en Azure Blob Storage met behulp van uw opslag account:

1. Selecteer in het nieuwe opslag account in het menu aan de linkerkant de optie **netwerken**.

1. Selecteer **privé-eind punt** op het tabblad **verbindingen van privé-eind punten** .

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="Scherm opname van het maken van persoonlijke eind punten voor het opslag account.":::

1. Gebruik op het tabblad **basis beginselen** de instellingen voor het persoonlijke eind punt die in de volgende tabel worden weer gegeven.

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe resources zijn gemaakt. | 
    | **[Resourcegroep](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Kies de resource groep die u hebt gemaakt met uw functie-app. | |
    | **Naam** | bestand-eind punt | De naam van het persoonlijke eind punt voor bestanden uit uw opslag account. |
    | **[Regio](https://azure.microsoft.com/regions/)** | myFunctionRegion | Kies de regio waar u uw opslag account hebt gemaakt. |

1. Gebruik op het tabblad **resource** de instellingen voor het persoonlijke eind punt die in de volgende tabel worden weer gegeven.

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe resources zijn gemaakt. | 
    | **Resourcetype**  | Microsoft.Storage/storageAccounts | Het resource type voor opslag accounts. |
    | **Resource** | mysecurestorage | Het opslag account dat u hebt gemaakt. |
    | **Stel subresource in** | file | Het persoonlijke eind punt dat wordt gebruikt voor bestanden van het opslag account. |

1. Kies op het tabblad **configuratie** voor de instelling **subnet** de optie **standaard**.

1. Selecteer **Controleren + maken**. Nadat de validatie is voltooid, selecteert u **maken**. Resources in het virtuele netwerk kunnen nu communiceren met opslag bestanden.

1. Maak een ander persoonlijk eind punt voor blobs. Gebruik op het tabblad **resources** de instellingen die in de volgende tabel worden weer gegeven. Gebruik voor alle andere instellingen dezelfde waarden die u hebt gebruikt om het persoonlijke eind punt voor bestanden te maken.

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe resources zijn gemaakt. | 
    | **Resourcetype**  | Microsoft.Storage/storageAccounts | Het resource type voor opslag accounts. |
    | **Resource** | mysecurestorage | Het opslag account dat u hebt gemaakt. |
    | **Stel subresource in** | blob | Het persoonlijke eind punt dat wordt gebruikt voor blobs uit het opslag account. |

## <a name="lock-down-your-service-bus"></a>Uw service bus vergren delen

Maak het persoonlijke eind punt om uw service bus te vergren delen:

1. Selecteer in de nieuwe service bus in het menu aan de linkerkant de optie **netwerken**.

1. Selecteer **privé-eind punt** op het tabblad **verbindingen van privé-eind punten** .

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="Scherm opname van het naar privé-eind punten voor de service bus.":::

1. Gebruik op het tabblad **basis beginselen** de instellingen voor het persoonlijke eind punt die in de volgende tabel worden weer gegeven.

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe resources zijn gemaakt. | 
    | **[Resourcegroep](../azure-resource-manager/management/overview.md)**  | myResourceGroup | De resource groep die u hebt gemaakt met uw functie-app. |
    | **Naam** | SB-eind punt | De naam van het persoonlijke eind punt voor bestanden uit uw opslag account. |
    | **[Regio](https://azure.microsoft.com/regions/)** | myFunctionRegion | De regio waar u uw opslag account hebt gemaakt. |

1. Gebruik op het tabblad **resource** de instellingen voor het persoonlijke eind punt die in de volgende tabel worden weer gegeven.

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe resources zijn gemaakt. | 
    | **Resourcetype**  | Microsoft.ServiceBus/namespaces | Het resource type voor de service bus. |
    | **Resource** | myServiceBus | De service bus die u eerder in de zelf studie hebt gemaakt. |
    | **Doel-subresource** | naamruimte | Het persoonlijke eind punt dat wordt gebruikt voor de naam ruimte van de service bus. |

1. Kies op het tabblad **configuratie** voor de instelling **subnet** de optie **standaard**.

1. Selecteer **Controleren + maken**. Nadat de validatie is voltooid, selecteert u **maken**. 

Resources in het virtuele netwerk kunnen nu communiceren met de service bus.

## <a name="create-a-file-share"></a>Een bestandsshare maken

1. Selecteer op het opslag account dat u hebt gemaakt, in het menu aan de linkerkant **Bestands shares**.

1. Selecteer **+ Bestands shares**. Voor de doel einden van deze zelf studie moet u de bestands share *bestanden* een naam.

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="Scherm afbeelding van het maken van een bestands share in het opslag account.":::

1. Selecteer **Maken**.

## <a name="get-the-storage-account-connection-string"></a>De connection string van het opslag account ophalen

1. Selecteer **toegangs sleutels** in het menu aan de linkerkant van het opslag account dat u hebt gemaakt.

1. Selecteer **sleutels weer geven**. Kopieer de connection string van **key1** en sla deze op. U hebt deze connection string nodig wanneer u de app-instellingen configureert.

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="Scherm opname van het ophalen van een opslag account connection string.":::

## <a name="create-a-queue"></a>Een wachtrij maken

Maak de wachtrij waarin uw Azure Functions service bus-trigger gebeurtenissen ontvangt:

1. Selecteer in uw service bus in het menu aan de linkerkant de optie **wacht rijen**.

1. Selecteer **Beleid voor gedeelde toegang**. Voor de doel einden van deze zelf studie moet u de lijst *wachtrij* een naam geven.

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="Scherm afbeelding van het maken van een service bus-wachtrij.":::

1. Selecteer **Maken**.

## <a name="get-a-service-bus-connection-string"></a>Een service bus-connection string ophalen

1. Selecteer in uw service bus, in het menu aan de linkerkant, de optie **beleid voor gedeelde toegang**.

1. selecteer **RootManageSharedAccessKey**. Kopieer de **primaire verbindings reeks** en sla deze op. U hebt deze connection string nodig wanneer u de app-instellingen configureert.

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="Scherm afbeelding van het ophalen van een service bus-connection string.":::

## <a name="integrate-the-function-app"></a>De functie-app integreren

Als u uw functie-app met virtuele netwerken wilt gebruiken, moet u deze toevoegen aan een subnet. U gebruikt een specifiek subnet voor de integratie van het Azure Functions virtuele netwerk. U gebruikt het standaard subnet voor andere privé-eind punten die u in deze zelf studie maakt.

1. Selecteer in de functie-app in het menu aan de linkerkant de optie **netwerken**.

1. Selecteer onder **VNet-integratie** de optie **Klik hier om te configureren**.

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="Scherm opname van hoe u naar de integratie van het virtuele netwerk gaat.":::

1. Selecteer **VNet toevoegen**.

1. Selecteer onder **Virtual Network** het virtuele netwerk dat u eerder hebt gemaakt.

1. Selecteer het subnet met **functies** dat u eerder hebt gemaakt. De functie-app is nu geïntegreerd met uw virtuele netwerk.

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="Scherm opname van hoe een functie-app moet worden verbonden met een subnet.":::

## <a name="configure-your-function-app-settings"></a>Instellingen voor de functie-app configureren

1. Selecteer in de functie-app in het menu aan de linkerkant **configuratie**.

1. Als u de functie-app met virtuele netwerken wilt gebruiken, moet u de app-instellingen bijwerken die in de volgende tabel worden weer gegeven. Als u een instelling wilt toevoegen of bewerken, selecteert u **+ nieuwe toepassings instelling** of het **bewerkings** pictogram in de kolom uiterst rechts van de tabel app-instellingen. Selecteer **Opslaan** wanneer u klaar bent.

    :::image type="content" source="./media/functions-create-vnet/10-configure-app-settings.png" alt-text="Scherm afbeelding van het configureren van de instellingen van de functie-app voor privé-eind punten.":::

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | De connection string van het opslag account dat u hebt gemaakt. Deze opslag connection string is afkomstig uit de sectie [Connection String ophalen van het opslag account](#get-the-storage-account-connection-string) . Met deze instelling kan uw functie-app het beveiligde-opslag account gebruiken voor normale bewerkingen tijdens runtime. | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | De connection string van het opslag account dat u hebt gemaakt. Met deze instelling kan uw functie-app gebruikmaken van het beveiligde-opslag account voor Azure Files, dat wordt gebruikt tijdens de implementatie. |
    | **WEBSITE_CONTENTSHARE** | bestanden | De naam van de bestands share die u hebt gemaakt in het opslag account. Gebruik deze instelling met WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | Maak deze app-instelling voor de connection string van uw service bus. Deze opslag connection string is afkomstig uit de sectie [een service bus ophalen Connection String](#get-a-service-bus-connection-string) .|
    | **WEBSITE_CONTENTOVERVNET** | 1 | Deze app-instelling maken. Met de waarde 1 kan de functie-app worden geschaald wanneer uw opslag account is beperkt tot een virtueel netwerk. |
    | **WEBSITE_DNS_SERVER** | 168.63.129.16 | Deze app-instelling maken. Als uw app met een virtueel netwerk is geïntegreerd, wordt dezelfde DNS-server gebruikt als het virtuele netwerk. De functie-app heeft deze instelling nodig zodat deze kan worden gebruikt met Azure DNS privé zones. Het is vereist wanneer u persoonlijke eind punten gebruikt. Met deze instelling en WEBSITE_VNET_ROUTE_ALL worden alle uitgaande oproepen vanuit uw app naar het virtuele netwerk verzonden. |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | Deze app-instelling maken. Als uw app met een virtueel netwerk is geïntegreerd, wordt dezelfde DNS-server gebruikt als het virtuele netwerk. De functie-app heeft deze instelling nodig zodat deze kan worden gebruikt met Azure DNS privé zones. Het is vereist wanneer u persoonlijke eind punten gebruikt. Met deze instelling en WEBSITE_DNS_SERVER worden alle uitgaande oproepen vanuit uw app naar het virtuele netwerk verzonden. |

1. Selecteer in de weer gave **configuratie** het tabblad **runtime-instellingen** voor de functie.

1. Stel **runtime Scale-bewaking** in **op aan**. Selecteer vervolgens **Opslaan**. Met door runtime gestuurde schaling kunt u niet-HTTP-trigger functies verbinden met services die worden uitgevoerd in uw virtuele netwerk.

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="Scherm opname van het inschakelen van op runtime gestuurde schaling voor Azure Functions.":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger"></a>Een service bus-trigger en een HTTP-trigger implementeren

1. In GitHub gaat u naar de volgende voorbeeld opslagplaats. Het bevat een functie-app en twee functies, een HTTP-trigger en een service bus-wachtrij trigger.

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. Selecteer boven aan de pagina **Fork** om een Fork van deze opslag plaats in uw eigen github-account of-organisatie te maken.

1. Selecteer in de functie-app in het menu aan de linkerkant het **implementatie centrum**. Selecteer vervolgens **instellingen**.

1. Gebruik op het tabblad **instellingen** de implementatie-instellingen die in de volgende tabel worden weer gegeven.

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Bron** | GitHub | U moet in stap 2 een GitHub-opslag plaats hebben gemaakt voor de voorbeeld code. | 
    | **Organisatie**  | myOrganization | De organisatie waarin uw opslag plaats is ingecheckt. Doorgaans is dit uw account. |
    | **Opslagplaats** | myRepo | De opslag plaats die u hebt gemaakt voor de voorbeeld code. |
    | **Vertakking** | main | De hoofd vertakking van de opslag plaats die u hebt gemaakt. |
    | **Runtimestack** | .NET | De voorbeeld code bevindt zich in C#. |

1. Selecteer **Opslaan**. 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="Scherm opname van de implementatie van Azure Functions code via de portal.":::

1. De eerste implementatie kan enkele minuten duren. Als uw app is geïmplementeerd, ziet u op het tabblad **Logboeken** het status bericht **geslaagd (actief)** . Vernieuw, indien nodig, de pagina.

Gefeliciteerd Uw voorbeeld functie-app is geïmplementeerd.

## <a name="lock-down-your-function-app"></a>De functie-app vergren delen

Maak nu het persoonlijke eind punt om uw functie-app te vergren delen. Dit persoonlijke eind punt verbindt uw functie-app privé en veilig met uw virtuele netwerk met behulp van een privé-IP-adres. 

Zie de documentatie van het [privé-eind punt](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)voor meer informatie.

1. Selecteer in de functie-app in het menu aan de linkerkant de optie **netwerken**.

1. Onder **verbindingen met een persoonlijk eind punt** selecteert u **uw particuliere endpoint-verbindingen configureren**.

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="Scherm opname van het navigeren naar een persoonlijk eind punt voor een functie-app.":::

1. Selecteer **Toevoegen**.

1. Gebruik de volgende instellingen voor het persoonlijke eind punt in het deel venster dat wordt geopend:

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="Scherm afbeelding van het maken van een persoonlijk eind punt voor een functie-app. De naam is functionapp-eind punt. Het abonnement is ' private test sub CACHHAI '. Het virtuele netwerk is MyVirtualNet-zelf studie. Het subnet is standaard.":::

1. Selecteer **OK** om het persoonlijke eind punt toe te voegen. 
 
Gefeliciteerd U hebt uw functie-app, service bus en opslag account beveiligd door persoonlijke eind punten toe te voegen.

### <a name="test-your-locked-down-function-app"></a>De vergrendelde functie-app testen

1. Selecteer in de functie-app in het menu aan de linkerkant de optie **functies**.

1. Selecteer **servicebusqueuetrigger: koppel**.

1. Selecteer in het menu aan de linkerkant de optie **monitor**. 
 
U ziet dat u uw app niet kunt bewaken. Uw browser heeft geen toegang tot het virtuele netwerk en heeft dus geen directe toegang tot resources in het virtuele netwerk. 
 
Hier volgt een alternatieve manier om uw functie te bewaken met behulp van Application Insights:

1. Selecteer in de functie-app in het menu aan de linkerkant **Application Insights**. Selecteer vervolgens **Application Insights gegevens weer geven**.

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="Scherm opname van het weer geven van Application Insights voor een functie-app.":::

1. Selecteer in het menu aan de linkerkant **Live Metrics**.

1. Open een nieuw tabblad. Selecteer in uw service bus in het menu aan de linkerkant de optie **wacht rijen**.

1. Selecteer uw wachtrij.

1. Selecteer in het menu aan de linkerkant **Service Bus Explorer**. Kies onder **verzenden**, voor **inhouds type**, **tekst/zonder opmaak**. Voer vervolgens een bericht in. 

1. Selecteer **verzenden** om het bericht te verzenden.

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="Scherm opname van het verzenden van Service Bus-berichten met behulp van de portal.":::

1. Op het tabblad **Live Metrics** ziet u dat de trigger van de service bus-wachtrij is geactiveerd. Als dat niet het geval is, verzendt u het bericht opnieuw vanuit **Service Bus Explorer**.

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="Scherm opname van het weer geven van berichten met behulp van dynamische metrische gegevens voor functie-apps.":::

Gefeliciteerd U hebt de installatie van uw functie-app met persoonlijke eind punten getest.

## <a name="understand-private-dns-zones"></a>Informatie over privé-DNS-zones
U hebt een persoonlijk eind punt gebruikt om verbinding te maken met Azure-resources. U maakt verbinding met een privé-IP-adres in plaats van het open bare eind punt. Bestaande Azure-Services zijn geconfigureerd voor het gebruik van een bestaande DNS om verbinding te maken met het open bare eind punt. U moet de DNS-configuratie overschrijven om verbinding te maken met het persoonlijke eind punt.

Er wordt een privé-DNS-zone gemaakt voor elke Azure-resource die is geconfigureerd met een persoonlijk eind punt. Er wordt een DNS-record gemaakt voor elk privé-IP-adres dat is gekoppeld aan het persoonlijke eind punt.

In deze zelf studie zijn de volgende DNS-zones gemaakt:

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een Premium-functie-app, opslag account en service bus gemaakt. U hebt al deze resources met een persoonlijke eind punt beveiligd. 

Gebruik de volgende koppelingen voor meer informatie over de beschik bare netwerk functies:

> [!div class="nextstepaction"]
> [Netwerk opties in Azure Functions](./functions-networking-options.md)


> [!div class="nextstepaction"]
> [Azure Functions Premium-abonnement](./functions-premium-plan.md)
