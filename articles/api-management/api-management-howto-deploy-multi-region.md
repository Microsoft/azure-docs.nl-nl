---
title: Azure API Management-services implementeren in meerdere Azure-regio's
titleSuffix: Azure API Management
description: Meer informatie over het implementeren van een Azure API Management service-exemplaar in meerdere Azure-regio's.
author: mikebudzynski
ms.service: api-management
ms.topic: how-to
ms.date: 04/13/2021
ms.author: apimpm
ms.openlocfilehash: 9546813173e72b1f264c3668ee889bbeea07ce7f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534088"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Exemplaar van Azure API Management-service implementeren in meerdere Azure-regio's

Azure API Management ondersteuning voor implementatie in meerdere regio's, waardoor API-uitgevers één Azure API Management-service kunnen distribueren over een aantal ondersteunde Azure-regio's. De functie voor meerdere regio's vermindert de latentie van aanvragen die worden waargenomen door geografisch gedistribueerde API-consumenten en verbetert de beschikbaarheid van de service als één regio offline gaat.

Een nieuwe Azure API Management-service bevat [in][unit] eerste instantie slechts één eenheid in één Azure-regio, de primaire regio. Extra eenheden kunnen worden toegevoegd aan de primaire of secundaire regio's. Een API Management gatewayonderdeel wordt geïmplementeerd in elke geselecteerde primaire en secundaire regio. Binnenkomende API-aanvragen worden automatisch omgeleid naar de dichtstbijzijnde regio. Als een regio offline gaat, worden de API-aanvragen automatisch om de mislukte regio gerouteerd naar de dichtstbijzijnde gateway.

> [!NOTE]
> Alleen het gatewayonderdeel van API Management wordt geïmplementeerd in alle regio's. Het servicebeheeronderdeel en de ontwikkelaarsportal worden alleen gehost in de primaire regio. In het geval van een storing in de primaire regio is de toegang tot de ontwikkelaarsportal en de mogelijkheid om de configuratie te wijzigen (bijvoorbeeld het toevoegen van API's en het toepassen van beleid) beperkt totdat de primaire regio weer online komt. Terwijl de primaire regio offline is, blijven beschikbare secundaire regio's het API-verkeer bedienen met behulp van de meest recente configuratie die voor hen beschikbaar is. Schakel zone [redcy eventueel in om](zone-redundancy.md) de beschikbaarheid en tolerantie van de primaire of secundaire regio's te verbeteren.

>[!IMPORTANT]
> De functie voor het opslaan van klantgegevens in één regio is momenteel alleen beschikbaar in de regio Azië - zuidoost (Singapore) van de Azië en Stille Oceaan Geo. Voor alle andere regio's worden klantgegevens opgeslagen in Geo.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]


## <a name="prerequisites"></a>Vereisten

* Zie Create an API Management service instance (Een service API Management service-exemplaar maken) als u [nog API Management service-exemplaar hebt gemaakt.](get-started-create-service-instance.md) Selecteer de Premium-servicelaag.
* Als uw API Management-exemplaar is geïmplementeerd in een virtueel [netwerk,](api-management-using-with-vnet.md)moet u ervoor zorgen dat u een virtueel netwerk, subnet en openbaar IP-adres in de locatie in stelt die u wilt toevoegen.

## <a name="deploy-api-management-service-to-an-additional-location"></a><a name="add-region"> </a>Een API Management implementeren op een extra locatie

1. Navigeer Azure Portal naar uw API Management service en selecteer **Locaties** in het menu.
1. Selecteer **+ Toevoegen** in de bovenste balk.
1. Selecteer de locatie in de vervolgkeuzelijst.
1. Selecteer het aantal **[schaaleenheden](upgrade-and-scale.md)** op de locatie.
1. Schakel [**beschikbaarheidszones (optioneel) in.**](zone-redundancy.md)
1. Als het API Management is geïmplementeerd in een virtueel [netwerk,](api-management-using-with-vnet.md)configureert u de instellingen voor het virtuele netwerk op de locatie. Selecteer een bestaand virtueel netwerk, subnet en openbaar IP-adres dat beschikbaar is op de locatie.
1. Selecteer **Toevoegen om** te bevestigen.
1. Herhaal dit proces totdat u alle locaties configureert.
1. Selecteer **Opslaan** in de bovenste balk om het implementatieproces te starten.

## <a name="delete-an-api-management-service-location"></a><a name="remove-region"> </a>Een API Management servicelocatie verwijderen

1. Navigeer Azure Portal naar uw API Management service en klik op de vermelding **Locaties** in het menu.
2. Voor de locatie die u wilt verwijderen, opent u het contextmenu met behulp van de **knop ...** aan de rechterkant van de tabel. Selecteer de **optie** Verwijderen.
3. Bevestig de verwijdering en klik op **Opslaan om** de wijzigingen toe te passen.

## <a name="route-api-calls-to-regional-backend-services"></a><a name="route-backend"> </a>API-aanroepen door sturen naar regionale back-endservices

Azure API Management slechts één URL voor de back-service. Hoewel er Azure API Management-exemplaren in verschillende regio's zijn, worden via de API-gateway nog steeds aanvragen doorgestuurd naar dezelfde back-endservice, die in slechts één regio is geïmplementeerd. In dit geval zijn de prestatieverbeteringen alleen afkomstig van antwoorden die in de cache van Azure API Management zijn opgeslagen in een regio die specifiek is voor de aanvraag, maar het contact opnemen met de back-end over de hele wereld kan nog steeds een hoge latentie veroorzaken.

Als u de geografische distributie van uw systeem volledig wilt benutten, moet u back-endservices hebben geïmplementeerd in dezelfde regio's als Azure API Management instanties. Vervolgens kunt u met behulp van beleidsregels en eigenschappen het verkeer naar lokale `@(context.Deployment.Region)` exemplaren van uw back-end doorseen.

1. Navigeer naar uw Azure API Management  en klik in het linkermenu op API's.
2. Selecteer de gewenste API.
3. Klik in de vervolgkeuzepijl in binnenkomende **verwerking op Code-editor.** 

    ![API-code-editor](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Gebruik de `set-backend` in combinatie met `choose` voorwaardelijk beleid om een correct routeringsbeleid te maken in de `<inbound> </inbound>` sectie van het bestand.

    Het onderstaande XML-bestand werkt bijvoorbeeld voor VS - west en Azië - oost regio's:

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> U kunt uw back-Azure Traffic Manager ook fronten met [Azure Traffic Manager,](https://azure.microsoft.com/services/traffic-manager/)de API-aanroepen naar de Traffic Manager en de routering automatisch laten oplossen.

## <a name="use-custom-routing-to-api-management-regional-gateways"></a><a name="custom-routing"> </a>Aangepaste routering gebruiken om API Management gateways te maken

API Management aanvragen worden gerouteerd naar een regionale _gateway_ op basis [van de laagste latentie.](../traffic-manager/traffic-manager-routing-methods.md#performance) Hoewel het niet mogelijk is om deze instelling te overschrijven in API Management, kunt u uw eigen Traffic Manager met aangepaste regels voor doorsturen.

1. Maak uw eigen [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/).
1. Als u een aangepast domein gebruikt, gebruikt u dit [met de Traffic Manager](../traffic-manager/traffic-manager-point-internet-domain.md) in plaats van de API Management service.
1. [Configureer API Management regionale eindpunten in Traffic Manager](../traffic-manager/traffic-manager-manage-endpoints.md). De regionale eindpunten volgen het URL-patroon van `https://<service-name>-<region>-01.regional.azure-api.net` , bijvoorbeeld `https://contoso-westus2-01.regional.azure-api.net` .
1. [Configureer API Management regionale status-eindpunten in Traffic Manager](../traffic-manager/traffic-manager-monitoring.md). De regionale status-eindpunten volgen het URL-patroon `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef` van , bijvoorbeeld `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef` .
1. Geef [de routeringsmethode](../traffic-manager/traffic-manager-routing-methods.md) van de Traffic Manager.

[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
