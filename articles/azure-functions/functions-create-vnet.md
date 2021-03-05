---
title: Privé-eind punten gebruiken om Azure Functions te integreren met een virtueel netwerk
description: Een stapsgewijze zelf studie waarin wordt uitgelegd hoe u een functie verbindt met een virtueel Azure-netwerk en deze kunt vergren delen met persoonlijke eind punten
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: a7bad58167009b4089724165813eb061996f1e6b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200203"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-using-private-endpoints"></a>Zelf studie: Azure Functions integreren met een virtueel Azure-netwerk met behulp van privé-eind punten

Deze zelf studie laat zien hoe u Azure Functions kunt gebruiken om verbinding te maken met resources in een virtueel Azure-netwerk met persoonlijke eind punten. U maakt een functie met een opslag account dat is vergrendeld achter een virtueel netwerk dat gebruikmaakt van een service bus-wachtrij trigger.

> [!div class="checklist"]
> * Een functie-app maken in het Premium-abonnement
> * Azure-resources maken (Service Bus, opslag account, Virtual Network)
> * Uw opslag account achter een persoonlijk eind punt vergren delen
> * Uw Service Bus achter een persoonlijk eind punt vergren delen
> * Implementeer een functie-app met zowel Service Bus als HTTP-triggers.
> * De functie-app achter een persoonlijk eind punt vergren delen
> * Testen om te zien of uw functie-app achter het virtuele netwerk is beveiligd
> * Resources opschonen

## <a name="create-a-function-app-in-a-premium-plan"></a>Een functie-app maken in een Premium-abonnement

Eerst maakt u een .NET-functie-app in het [Premium-abonnement] , aangezien deze zelf studie C# zal gebruiken. Andere talen worden ook ondersteund in Windows. Dit abonnement biedt serverloze schalen en ondersteunt de integratie van virtuele netwerken.

1. Selecteer vanuit het menu van Azure Portal of op de **startpagina** de optie **Een resource maken**.

1. Selecteer op de pagina **Nieuw** **Reken** > **functie-app**.

1. Op de pagina **Basics** gebruikt u de instellingen voor de functie-app zoals in de volgende tabel wordt vermeld:

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe functie-app is gemaakt. |
    | **[Resourcegroep](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Naam voor de nieuwe resourcegroep waarin uw functie-app moet worden gemaakt. |
    | **Naam van de functie-app** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app wordt aangeduid. Geldige tekens zijn `a-z` (hoofdlettergevoelig), `0-9` en `-`.  |
    |**Publiceren**| Code | Optie voor het publiceren van codebestanden of een Docker-container. |
    | **Runtimestack** | .NET | In deze zelf studie wordt .NET gebruikt |
    |**Regio**| Voorkeursregio | Kies een [regio](https://azure.microsoft.com/regions/) in de buurt of in de buurt van andere services die door uw functie worden gebruikt. |

1. Selecteer **Volgende: Hosting**. Voer op de pagina **Hosting** de volgende instellingen in:

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **[Opslagaccount](../storage/common/storage-account-create.md)** |  Wereldwijd unieke naam |  Maak een opslagaccount die wordt gebruikt door uw functie-app. Namen van opslagaccounts moeten tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten. U kunt ook een bestaand account gebruiken dat voldoet aan de [vereisten voor een opslagaccount](./storage-considerations.md#storage-account-requirements). |
    |**Besturingssysteem**| Windows | In deze zelf studie wordt gebruikgemaakt van Windows |
    | **[Plannen](./functions-scale.md)** | Premium | Hostingabonnement dat definieert hoe resources worden toegewezen aan uw functie-app. Selecteer **Premium**. Er wordt standaard een nieuw App Service-plan gemaakt. De standaard **SKU en grootte** is **EP1**, waarbij EP staat voor _Elastisch Premium_. Zie voor meer informatie de [lijst met Premium-SKU's](./functions-premium-plan.md#available-instance-skus).<br/>Wanneer u JavaScript-functies uitvoert in een Premium-abonnement, kunt u het beste een instantie kiezen met minder vCPU's. Zie voor meer informatie het artikel over [Premium-abonnementen met één core kiezen](./functions-reference-node.md#considerations-for-javascript-functions).  |

1. Selecteer **Volgende: Bewaking**. Voer op de pagina **Bewaking** de volgende instellingen in:

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | Standaard | Hiermee maakt u een Application Insights-resource van dezelfde *app-naam* in de dichtstbijzijnde ondersteunde regio. Door deze instelling uit te breiden, kunt u de **Nieuwe resourcenaam** wijzigen of een andere **Locatie** in een [Azure-geografie](https://azure.microsoft.com/global-infrastructure/geographies/) kiezen om uw gegevens op te slaan. |

1. Selecteer **Beoordelen + maken** om de selecties van appconfiguratie te controleren.

1. Controleer uw instellingen op de pagina **Beoordelen en maken** en selecteer vervolgens **Maken** om de functie-app in te richten en te implementeren.

1. Selecteer het **Meldingspictogram** in de rechterbovenhoek van de portal en zoek het bericht **Implementatie voltooid**.

1. Selecteer **Naar de resource gaan** om uw nieuwe functie-app te bekijken. U kunt ook **Vastmaken aan dashboard** selecteren. Vastmaken maakt het gemakkelijker om terug te gaan naar deze functie-app-resource vanuit uw dashboard.

1. Gefeliciteerd U hebt uw Premium-functie-app gemaakt.

## <a name="create-azure-resources"></a>Azure-resources maken

### <a name="create-a-storage-account"></a>Een opslagaccount maken

Een afzonderlijk opslag account dat is gemaakt tijdens het maken van de eerste keer dat u de functie-app maakt, is vereist voor virtuele netwerken.

1. Selecteer vanuit het menu van Azure Portal of op de **startpagina** de optie **Een resource maken**.

1. Zoek op de pagina nieuw naar **Storage-account** en selecteer **maken** .

1. Stel op het tabblad **basis beginselen** de instellingen in zoals aangegeven in de onderstaande tabel. De rest kan als standaard waarde blijven:

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe resources zijn gemaakt. | 
    | **[Resourcegroep](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Kies de resource groep die u hebt gemaakt met uw functie-app. |
    | **Naam** | mysecurestorage| De naam van uw opslag account waarop het persoonlijke eind punt wordt toegepast. |
    | **[Regio](https://azure.microsoft.com/regions/)** | myFunctionRegion | Kies de regio waarin u de functie-app hebt gemaakt. |

1. Selecteer **Controleren + maken**. Nadat de validatie is voltooid, selecteert u **Maken**.

### <a name="create-a-service-bus"></a>Een Service Bus maken

1. Selecteer vanuit het menu van Azure Portal of op de **startpagina** de optie **Een resource maken**.

1. Zoek op de pagina nieuw naar **Service Bus** en selecteer **maken**.

1. Stel op het tabblad **basis beginselen** de instellingen in zoals aangegeven in de onderstaande tabel. De rest kan als standaard waarde blijven:

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe resources zijn gemaakt. |
    | **[Resourcegroep](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Kies de resource groep die u hebt gemaakt met uw functie-app. |
    | **Naam** | myServiceBus| De naam van de service bus waarop het persoonlijke eind punt wordt toegepast. |
    | **[Regio](https://azure.microsoft.com/regions/)** | myFunctionRegion | Kies de regio waarin u de functie-app hebt gemaakt. |
    | **Prijscategorie** | Premium | Kies deze laag om privé-eind punten te gebruiken met Service Bus. |

1. Selecteer **Controleren + maken**. Nadat de validatie is voltooid, selecteert u **Maken**.

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Azure-resources in deze zelf studie kunnen worden geïntegreerd met of worden opgenomen in een virtueel netwerk. U gebruikt privé-eind punten om netwerk verkeer in het virtuele netwerk te blijven gebruiken.

In de zelf studie worden twee subnetten gemaakt:
- **default**: subnet voor privé-eind punten. Privé-IP-adressen worden via dit subnet verstrekt.
- **functions**: Subnet voor Azure functions integratie van virtuele netwerken. Dit subnet wordt overgedragen aan de functie-app.

Maak nu het virtuele netwerk waarmee de functie-app wordt geïntegreerd.

1. Selecteer vanuit het menu van Azure Portal of op de startpagina de optie **Een resource maken**.

1. Zoek op de pagina nieuw naar **Virtual Network** en selecteer **maken**.

1. Op het tabblad **basis beginselen** gebruikt u de instellingen van het virtuele netwerk zoals hieronder is opgegeven:

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe resources zijn gemaakt. | 
    | **[Resourcegroep](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Kies de resource groep die u hebt gemaakt met uw functie-app. |
    | **Naam** | myVirtualNet| De naam van het virtuele netwerk waarmee de functie-app verbinding maakt. |
    | **[Regio](https://azure.microsoft.com/regions/)** | myFunctionRegion | Kies de regio waarin u de functie-app hebt gemaakt. |

1. Selecteer **subnet toevoegen** op het tabblad **IP-adressen** . Gebruik de instellingen die hieronder zijn opgegeven bij het toevoegen van een subnet:

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="Scherm opname van de configuratie weergave virtuele netwerk maken.":::

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Subnetnaam** | vervullen | De naam van het subnet waarmee uw functie-app verbinding maakt. | 
    | **Subnetadresbereik** | 10.0.1.0/24 | U ziet dat de IPv4-adres ruimte in de bovenstaande afbeelding 10.0.0.0/16 is. Als bovenstaande 10.1.0.0/16 is, zou het aanbevolen *adres bereik* 10.1.1.0/24 zijn. |

1. Selecteer **Controleren + maken**. Nadat de validatie is voltooid, selecteert u **Maken**.

## <a name="lock-down-your-storage-account-with-private-endpoints"></a>Uw opslag account vergren delen met privé-eind punten

Persoonlijke Azure-eind punten worden gebruikt om verbinding te maken met specifieke Azure-resources met behulp van een privé-IP-adres. Deze verbinding zorgt ervoor dat het netwerk verkeer binnen het gekozen virtuele netwerk blijft en dat toegang alleen beschikbaar is voor specifieke bronnen. Maak nu de persoonlijke eind punten voor Azure file storage en Azure Blob-opslag met uw opslag account.

1. Selecteer in het nieuwe opslag account de optie **netwerken** in het menu links.

1. Selecteer het tabblad **verbindingen met privé-eind punten** en selecteer **persoonlijk eind punt**.

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="Scherm opname van hoe u kunt navigeren om persoonlijke eind punten voor het opslag account te maken.":::

1. Op het tabblad **basis beginselen** gebruikt u de instellingen van het persoonlijke eind punt zoals hieronder is opgegeven:

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe resources zijn gemaakt. | 
    | **[Resourcegroep](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Kies de resource groep die u hebt gemaakt met uw functie-app. | |
    | **Naam** | bestand-eind punt | De naam van het persoonlijke eind punt voor bestanden uit uw opslag account. |
    | **[Regio](https://azure.microsoft.com/regions/)** | myFunctionRegion | Kies de regio waarin u uw opslag account hebt gemaakt. |

1. Gebruik op het tabblad **resource** de instellingen voor het persoonlijke eind punt zoals hieronder is opgegeven:

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe resources zijn gemaakt. | 
    | **Resourcetype**  | Microsoft.Storage/storageAccounts | Dit is het resource type voor opslag accounts. |
    | **Resource** | mysecurestorage | Het opslag account dat u zojuist hebt gemaakt |
    | **Stel subresource in** | file | Dit persoonlijke eind punt wordt gebruikt voor bestanden van het opslag account. |

1. Kies op het tabblad **configuratie** de optie **standaard** voor de instelling subnet.

1. Selecteer **Controleren + maken**. Nadat de validatie is voltooid, selecteert u **Maken**. Resources in het virtuele netwerk kunnen nu worden gecommuniceerd met opslag bestanden.

1. Maak een ander persoonlijk eind punt voor blobs. Gebruik de onderstaande instellingen voor het tabblad **resources** . Voor alle andere instellingen gebruikt u dezelfde instellingen van de stappen voor het maken van een persoonlijk eind punt dat u zojuist hebt gevolgd.

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe resources zijn gemaakt. | 
    | **Resourcetype**  | Microsoft.Storage/storageAccounts | Dit is het resource type voor opslag accounts. |
    | **Resource** | mysecurestorage | Het opslag account dat u zojuist hebt gemaakt |
    | **Stel subresource in** | blob | Dit persoonlijke eind punt wordt gebruikt voor blobs uit het opslag account. |

## <a name="lock-down-your-service-bus-with-a-private-endpoint"></a>Uw service bus vergren delen met een persoonlijk eind punt

Maak nu het persoonlijke eind punt voor uw Azure Service Bus.

1. Selecteer in de nieuwe service bus de optie **netwerken** in het menu links.

1. Selecteer het tabblad **verbindingen met privé-eind punten** en selecteer **persoonlijk eind punt**.

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="Scherm opname van het navigeren naar persoonlijke eind punten voor service bus.":::

1. Op het tabblad **basis beginselen** gebruikt u de instellingen van het persoonlijke eind punt zoals hieronder is opgegeven:

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe resources zijn gemaakt. | 
    | **[Resourcegroep](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Kies de resource groep die u hebt gemaakt met uw functie-app. |
    | **Naam** | SB-eind punt | De naam van het persoonlijke eind punt voor bestanden uit uw opslag account. |
    | **[Regio](https://azure.microsoft.com/regions/)** | myFunctionRegion | Kies de regio waarin u uw opslag account hebt gemaakt. |

1. Gebruik op het tabblad **resource** de instellingen voor het persoonlijke eind punt zoals hieronder is opgegeven:

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe resources zijn gemaakt. | 
    | **Resourcetype**  | Microsoft.ServiceBus/namespaces | Dit is het resource type voor Service Bus. |
    | **Resource** | myServiceBus | De Service Bus die u eerder hebt gemaakt in de zelf studie. |
    | **Doel-subresource** | naamruimte | Dit persoonlijke eind punt wordt gebruikt voor de naam ruimte vanuit de service bus. |

1. Kies op het tabblad **configuratie** de optie **standaard** voor de instelling subnet.

1. Selecteer **Controleren + maken**. Nadat de validatie is voltooid, selecteert u **Maken**. Resources in het virtuele netwerk kunnen nu worden gecommuniceerd met Service Bus.

## <a name="create-a-file-share"></a>Een bestandsshare maken

1. Selecteer in het opslag account dat u hebt gemaakt **Bestands shares** in het menu links.

1. Selecteer **+ Bestands shares**. Geef **bestanden** op als naam voor de bestands share voor deze zelf studie.

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="Scherm afbeelding van het maken van een bestands share in het opslag account.":::

## <a name="get-storage-account-connection-string"></a>connection string van opslag account ophalen

1. Selecteer in het opslag account dat u hebt gemaakt de optie **toegangs sleutels** in het menu links.

1. Selecteer **sleutels weer geven**. Kopieer het connection string van Key1 en sla het op. Deze connection string is later vereist bij het configureren van de app-instellingen.

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="Scherm opname van het ophalen van een opslag account connection string.":::

## <a name="create-a-queue"></a>Een wachtrij maken

Dit is de wachtrij waarvan de trigger van de Azure Functions Service Bus gebeurtenissen ontvangt.

1. In uw service bus selecteert u **wacht rijen** in het linkermenu.

1. Selecteer **Beleid voor gedeelde toegang**. Geef een **wachtrij** op als naam voor de wachtrij voor de doel einden van deze zelf studie.

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="Scherm afbeelding van het maken van een service bus-wachtrij.":::

## <a name="get-service-bus-connection-string"></a>Service bus-connection string ophalen

1. Selecteer in uw service bus **Shared Access policies** in het menu links.

1. selecteer **RootManageSharedAccessKey**. Kopieer de **primaire verbindings reeks** en sla deze op. Deze connection string is later vereist bij het configureren van de app-instellingen.

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="Scherm afbeelding van het ophalen van een service bus-connection string.":::

## <a name="integrate-function-app-with-your-virtual-network"></a>Functie-app integreren met uw virtuele netwerk

Als u uw functie-app met virtuele netwerken wilt gebruiken, moet u deze toevoegen aan een subnet. We gebruiken een specifiek subnet voor de Azure Functions virtuele netwerk integratie en het standaard subnetwerk voor alle andere privé-eind punten die in deze zelf studie zijn gemaakt.

1. Selecteer in de functie-app **netwerken** in het menu links.

1. Selecteer **Klik hier om te configureren** onder VNet-integratie.

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="Scherm opname van het navigeren naar virtuele netwerk integratie.":::

1. Selecteer **VNet toevoegen**

1. Selecteer op de Blade die wordt geopend onder **Virtual Network** het virtuele netwerk dat u eerder hebt gemaakt.

1. Selecteer het **subnet** dat u eerder hebt gemaakt met de naam **functions**. De functie-app is nu geïntegreerd met uw virtuele netwerk.

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="Scherm opname van hoe een functie-app moet worden verbonden met een subnet.":::

## <a name="configure-your-function-app-settings-for-private-endpoints"></a>De instellingen van de functie-app voor privé-eind punten configureren

1. Selecteer in de functie-app **configuratie** in het menu links.

1. Als u uw functie-app met virtuele netwerken wilt gebruiken, moeten de volgende app-instellingen worden bijgewerkt. Selecteer **+ nieuwe toepassings instelling** of het potlood door **bewerken** in de kolom uiterst rechts van de tabel app-instellingen. Selecteer **Opslaan** wanneer u klaar bent.

    :::image type="content" source="./media/functions-create-vnet/10-configure-app-settings.png" alt-text="Scherm afbeelding van het configureren van de instellingen van de functie-app voor privé-eind punten.":::

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | De connection string van het opslag account dat u hebt gemaakt. Dit is de opslag connection string van het [Connection String ophalen van het opslag account](#get-storage-account-connection-string). Als u deze instelling wijzigt, gebruikt uw functie-app nu het beveiligde-opslag account voor normale bewerkingen tijdens runtime. | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | De connection string van het opslag account dat u hebt gemaakt. Als u deze instelling wijzigt, gebruikt uw functie-app nu het beveiligde-opslag account voor Azure Files, dat wordt gebruikt bij de implementatie. |
    | **WEBSITE_CONTENTSHARE** | bestanden | De naam van de bestands share die u hebt gemaakt in het opslag account. Deze app-instelling wordt gebruikt in combi natie met WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | Maak een app-instelling voor de connection string van uw service bus. Dit is de opslag connection string van [Service Bus-Connection String ophalen](#get-service-bus-connection-string).|
    | **WEBSITE_CONTENTOVERVNET** | 1 | Deze app-instelling maken. Met de waarde 1 kan de functie-app worden geschaald wanneer uw opslag account is beperkt tot een virtueel netwerk. U moet deze instelling inschakelen wanneer u uw opslag account beperkt tot een virtueel netwerk. |
    | **WEBSITE_DNS_SERVER** | 168.63.129.16 | Deze app-instelling maken. Als uw app is geïntegreerd met een virtueel netwerk, gebruikt deze dezelfde DNS-server als het virtuele netwerk. Dit is een van de twee instellingen die nodig zijn om uw functie-app te laten werken met Azure DNS persoonlijke zones en die vereist zijn bij het gebruik van privé-eind punten. Met deze instellingen worden alle uitgaande oproepen van uw app naar het virtuele netwerk verzonden. |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | Deze app-instelling maken. Als uw app is geïntegreerd met een virtueel netwerk, gebruikt deze dezelfde DNS-server als het virtuele netwerk. Dit is een van de twee instellingen die nodig zijn om uw functie-app te laten werken met Azure DNS persoonlijke zones en die vereist zijn bij het gebruik van privé-eind punten. Met deze instellingen worden alle uitgaande oproepen van uw app naar het virtuele netwerk verzonden. |

1. Open de **configuratie** weergave en selecteer het tabblad **runtime-instellingen** voor de functie.

1. Stel **runtime Scale-bewaking** in **op** aan en selecteer **Opslaan**. Met door runtime gestuurde schaling kunt u niet-HTTP-trigger functies verbinden met services die worden uitgevoerd in uw virtuele netwerk.

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="Scherm opname van het inschakelen van op runtime gestuurde schaling voor Azure Functions.":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger-to-your-function-app"></a>Een service bus-trigger en http-trigger implementeren voor uw functie-app

1. In GitHub gaat u naar de volgende voorbeeld opslagplaats, die een functie-app bevat met twee functies, een HTTP-trigger en een Service Bus wachtrij trigger.

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. Selecteer boven aan de pagina de **Fork** -knop om een Fork van deze opslag plaats in uw eigen github-account of-organisatie te maken.

1. Selecteer in de functie-app **Deployment Center** in het menu links. Selecteer vervolgens **instellingen**.

1. Gebruik de implementatie-instellingen die hieronder zijn opgegeven op het tabblad **instellingen** :

    | Instelling      | Voorgestelde waarde  | Beschrijving      |
    | ------------ | ---------------- | ---------------- |
    | **Bron** | GitHub | U moet een GitHub-opslag plaats met de voorbeeld code in stap 2 hebben gemaakt. | 
    | **Organisatie**  | myOrganization | Dit is de organisatie waar uw opslag plaats is ingecheckt, meestal uw account. |
    | **Opslagplaats** | myRepo | De opslag plaats die u hebt gemaakt met behulp van de voorbeeld code. |
    | **Vertakking** | main | Dit is de opslag plaats die u zojuist hebt gemaakt. Gebruik daarom de hoofd vertakking. |
    | **Runtimestack** | .NET | De voorbeeld code bevindt zich in C#. |

1. Selecteer **Opslaan**. 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="Scherm opname van de implementatie van Azure Functions code via de portal.":::

1. De initiële implementatie kan enkele minuten duren. U ziet een geslaagd status bericht **(actief)** op het tabblad **Logboeken** wanneer de app is geïmplementeerd. Als dat nodig is, kunt u de pagina vernieuwen. 

1. Gefeliciteerd Uw voorbeeld functie-app is geïmplementeerd.

## <a name="lock-down-your-function-app-with-a-private-endpoint"></a>De functie-app met een persoonlijk eind punt vergren delen

Maak nu het persoonlijke eind punt voor uw functie-app. Dit persoonlijke eind punt verbindt uw functie-app privé en veilig met uw virtuele netwerk met behulp van een privé-IP-adres. Ga naar de [documentatie over privé-eind punten](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)voor meer informatie over privé-eind punten.

1. Selecteer in de functie-app **netwerken** in het menu links.

1. Selecteer **hier Klik hier om te configureren** onder verbindingen met een privé-eind punt.

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="Scherm opname van het navigeren naar een functie-app persoonlijk eind punt.":::

1. Selecteer **Toevoegen**.

1. Gebruik in het menu dat wordt geopend de instellingen voor het persoonlijke eind punt zoals hieronder is opgegeven:

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="Scherm afbeelding van het maken van een functie-app persoonlijk eind punt.":::

1. Selecteer **OK** om het persoonlijke eind punt toe te voegen. Gefeliciteerd U hebt uw functie-app, service bus en opslag account beveiligd met persoonlijke eind punten.

### <a name="test-your-locked-down-function-app"></a>De vergrendelde functie-app testen

1. Selecteer in de functie-app **functies** in het menu links.

1. Selecteer de **servicebusqueuetrigger: koppel**.

1. Selecteer in het menu links de optie **monitor**. u ziet dat u uw app niet kunt bewaken. Dit komt doordat uw browser geen toegang heeft tot het virtuele netwerk, zodat er geen directe toegang tot resources is in het virtuele netwerk. We tonen nu een andere methode waarmee u uw functie nog steeds kunt controleren Application Insights.

1. Selecteer in de functie-app **Application Insights** in het menu links en selecteer **Application Insights gegevens weer geven**.

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="Scherm opname van het weer geven van Application Insights voor een functie-app.":::

1. Selecteer **Live Metrics** in het menu links.

1. Open een nieuw tabblad. Selecteer in uw Service Bus **wacht rijen** in het menu links.

1. Selecteer uw wachtrij.

1. Selecteer **Service Bus Verkenner** in het menu links. Kies onder **verzenden** de optie **tekst/zonder** **inhouds type** en voer een bericht in. 

1. Selecteer **verzenden** om het bericht te verzenden.

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="Scherm opname van het verzenden van Service Bus berichten met behulp van de portal.":::

1. Op het tabblad met **Live metrieken** opent u de trigger voor de service bus-wachtrij. Als dat niet het geval is, verzendt u het bericht opnieuw vanuit **Service Bus Explorer**

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="Scherm afbeelding van het weer geven van berichten met Live metrische gegevens voor functie-apps.":::

1. Gefeliciteerd U hebt de functie-app die is ingesteld met persoonlijke eind punten getest.

### <a name="private-dns-zones"></a>Privé-DNS-zones
Als u een persoonlijk eind punt gebruikt om verbinding te maken met Azure-resources, wordt verbinding gemaakt met een privé-IP-adres in plaats van het open bare eind punt Bestaande Azure-Services zijn geconfigureerd voor het gebruik van bestaande DNS om verbinding te maken met het open bare eind punt. De DNS-configuratie moet worden overschreven om verbinding te maken met het persoonlijke eind punt.

Er is een privé-DNS-zone gemaakt voor elke Azure-resource die is geconfigureerd met een persoonlijk eind punt. Er wordt een DNS A-record gemaakt voor elk privé-IP-adres dat is gekoppeld aan het persoonlijke eind punt.

In deze zelf studie zijn de volgende DNS-zones gemaakt:

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een Premium-functie-app, een opslag account en een Service Bus gemaakt en hebt u deze allemaal achter de persoonlijke eind punten beveiligd. Meer informatie over de verschillende beschik bare netwerk functies:

> [!div class="nextstepaction"]
> [Meer informatie over de netwerkopties in Functions](./functions-networking-options.md)

[Premium-abonnement]: functions-premium-plan.md
