---
title: Azure API Management Services implementeren in meerdere Azure-regio's
titleSuffix: Azure API Management
description: Meer informatie over het implementeren van een Azure API Management service-exemplaar naar meerdere Azure-regio's.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 427ebfe865002612be2f9aeb9db416f5c2f41e52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88065451"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Exemplaar van Azure API Management-service implementeren in meerdere Azure-regio's

Azure API Management ondersteunt implementatie met meerdere regio's, waarmee API-uitgevers één Azure API Management-service kunnen distribueren over een wille keurig aantal ondersteunde Azure-regio's. Met de functie voor meerdere regio's kunt u de latentie van aanvragen beperken die wordt waargenomen door geografisch gedistribueerde API-consumenten en de beschik baarheid van de service verbeteren als de ene regio offline gaat.

Een nieuwe Azure API Management-service bevat in eerste instantie slechts één [eenheid][unit] in één Azure-regio, de primaire regio. Extra eenheden kunnen worden toegevoegd aan de primaire of secundaire regio's. Een API Management Gateway onderdeel wordt geïmplementeerd op elke geselecteerde primaire en secundaire regio. Binnenkomende API-aanvragen worden automatisch omgeleid naar de dichtstbijzijnde regio. Als een regio offline gaat, worden de API-aanvragen automatisch omgeleid naar de defecte regio naar de dichtstbijzijnde gateway.

> [!NOTE]
> Alleen het gateway onderdeel van API Management wordt geïmplementeerd in alle regio's. Het Service Management-onderdeel en de ontwikkelaars Portal worden alleen gehost in de primaire regio. In het geval van de onderbreking van de primaire regio, wordt de toegang tot de ontwikkelaars Portal en de mogelijkheid om de configuratie te wijzigen (bijvoorbeeld het toevoegen van Api's, het Toep assen van beleid) verstoord tot de primaire regio weer online is. Terwijl de primaire regio offline beschikbaar is, blijven secundaire regio's het API-verkeer verwerken met de meest recente configuratie die voor hen beschikbaar is.

>[!IMPORTANT]
> De functie voor het opslaan van klant gegevens in één regio is momenteel alleen beschikbaar in de regio Zuidoost-Azië (Singapore) van de Azië en Stille Oceaan geo. Voor alle andere regio's worden klantgegevens opgeslagen in Geo.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="deploy-api-management-service-to-a-new-region"></a><a name="add-region"> </a>API Management-service implementeren in een nieuwe regio

> [!NOTE]
> Als u nog geen API Management service-exemplaar hebt gemaakt, raadpleegt u [een API Management service-exemplaar maken][create an api management service instance].

1. Ga in het Azure Portal naar de API Management-service en klik op de vermelding **locaties** in het menu.
2. Klik op de bovenste balk op **+ toevoegen** .
3. Selecteer de locatie in de vervolg keuzelijst en stel het aantal eenheden in met de schuif regelaar.
4. Klik op de knop **toevoegen** om te bevestigen.
5. Herhaal dit proces totdat u alle locaties hebt geconfigureerd.
6. Klik op **Opslaan** in de bovenste balk om het implementatie proces te starten.

## <a name="delete-an-api-management-service-location"></a><a name="remove-region"> </a>Een API Management service locatie verwijderen

1. Ga in het Azure Portal naar de API Management-service en klik op de vermelding **locaties** in het menu.
2. Voor de locatie die u wilt verwijderen, opent u het context menu met behulp van de knop **...** aan de rechter kant van de tabel. Selecteer de optie **verwijderen** .
3. Bevestig de verwijdering en klik op **Opslaan** om de wijzigingen toe te passen.

## <a name="route-api-calls-to-regional-backend-services"></a><a name="route-backend"> </a>Route-API-aanroepen naar regionale back-end-services

Azure API Management bevat slechts één back-end-service-URL. Hoewel er Azure API Management instanties in verschillende regio's zijn, zal de API-gateway nog steeds aanvragen door sturen naar dezelfde back-end-service, die slechts in één regio is geïmplementeerd. In dit geval wordt de prestatie verbetering alleen opgehaald van antwoorden in de cache van Azure API Management in een specifieke regio voor de aanvraag, maar neemt het contact op met de back-end over de hele wereld mogelijk toch een hoge latentie.

Als u de geografische distributie van uw systeem volledig wilt benutten, moet u de back-upservices hebben geïmplementeerd in dezelfde regio's als Azure API Management-exemplaren. Vervolgens kunt u met beleid en `@(context.Deployment.Region)` eigenschap het verkeer door sturen naar lokale exemplaren van uw back-end.

1. Navigeer naar uw Azure API Management-exemplaar en klik op **api's** in het menu links.
2. Selecteer de gewenste API.
3. Klik op **code-editor** van de vervolg keuzelijst pijl in de verwerking van **binnenkomende verbindingen**.

    ![API-code-editor](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Gebruik de `set-backend` combi natie met voorwaardelijke `choose` beleids regels voor het maken van een juist routerings beleid in de `<inbound> </inbound>` sectie van het bestand.

    Het onderstaande XML-bestand zou bijvoorbeeld werken voor de regio's vs-West en Azië-oost:

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
> U kunt ook de voor-en achterstanden van uw back-end met [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/), de API-aanroepen naar de Traffic manager sturen en de route ring automatisch laten oplossen.

## <a name="use-custom-routing-to-api-management-regional-gateways"></a><a name="custom-routing"> </a>Aangepaste route ring gebruiken om regionale gateways te API Management

API Management stuurt de aanvragen naar een regionale _Gateway_ op basis van [de laagste latentie](../traffic-manager/traffic-manager-routing-methods.md#performance). Hoewel het niet mogelijk is om deze instelling te negeren in API Management, kunt u uw eigen Traffic Manager gebruiken met aangepaste routerings regels.

1. Maak uw eigen [Azure-Traffic Manager](https://azure.microsoft.com/services/traffic-manager/).
1. Als u een aangepast domein gebruikt, [gebruikt u dit met de Traffic Manager](../traffic-manager/traffic-manager-point-internet-domain.md) in plaats van de API Management-service.
1. [Configureer de API Management regionale eind punten in Traffic Manager](../traffic-manager/traffic-manager-manage-endpoints.md). De regionale eind punten volgen bijvoorbeeld het URL-patroon van `https://<service-name>-<region>-01.regional.azure-api.net` `https://contoso-westus2-01.regional.azure-api.net` .
1. [Configureer de API Management regionale status eindpunten in Traffic Manager](../traffic-manager/traffic-manager-monitoring.md). De regionale status-eind punten volgen het URL-patroon van `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef` , bijvoorbeeld `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef` .
1. Geef [de routerings methode](../traffic-manager/traffic-manager-routing-methods.md) van de Traffic Manager op.

[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
