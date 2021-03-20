---
title: Zelfstudie - Uw eerste API importeren en publiceren in Azure API Management
description: In deze zelfstudie importeert u een API met OpenAPI-specificatie in Azure API Management en test u uw API vervolgens in de Azure Portal.
author: mikebudzynski
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: apimpm
ms.openlocfilehash: 9ff64f57e61002101b4e2c560bdcd91863cc461e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91626947"
---
# <a name="tutorial-import-and-publish-your-first-api"></a>Zelfstudie: Uw eerste API importeren en publiceren

In deze zelfstudie ziet u hoe u een back-end-API met OpenAPI-specificatie in JSON-indeling kunt importeren in Azure API Management. Microsoft biedt de back-end-API die in dit voorbeeld wordt gebruikt en host deze op Azure op [https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json).

Zodra u de back-end-API hebt geïmporteerd in API Management, wordt uw API Management API een facade voor de back-end-API. In API Management kunt u de facade naar wens aanpassen zonder de back-end-API te wijzigen. Zie [Transform and protect your API](transform-api.md) (Uw API transformeren en beveiligen) voor meer informatie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een API importeren in API Management
> * De API testen in Azure Portal

Na het importeren kunt u de API beheren in de Azure Portal.

:::image type="content" source="media/import-and-publish/created-api.png" alt-text="Nieuwe API in API beheren":::

## <a name="prerequisites"></a>Vereisten

- Kennis van de [terminologie van Azure API Management](api-management-terminology.md).
- [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a>Een back-end-API importeren en publiceren

In deze sectie ziet u hoe u een back-end-API met OpenAPI-specificatie kunt importeren en publiceren.

1. Selecteer **API’s** in de linkernavigatie van uw API Management-exemplaar.
1. Selecteer de tegel **OpenAPI**.
1. Selecteer **Volledig** in het venster **Maken op basis van OpenAPI-specificatie**.
1. Voer de waarden in uit de volgende tabel. Selecteer vervolgens **Maken** om uw API te maken.

   U kunt API-waarden instellen tijdens het maken of later door naar het tabblad **Instellingen** te gaan.

   :::image type="content" source="media/import-and-publish/create-api.png" alt-text="Een API maken":::


   |Instelling|Waarde|Beschrijving|
   |-------|-----|-----------|
   |**OpenAPI-specificatie**|*https:\//conferenceapi.azurewebsites.net?format=json*|De service waarmee de API wordt geïmplementeerd. API Management stuurt aanvragen door naar dit adres.|
   |**Weergavenaam**|Nadat u de voorgaande service-URL hebt opgegeven, vult API Management dit veld in op basis van de JSON.|De naam wordt weergegeven in de [ontwikkelaarsportal](api-management-howto-developer-portal.md).|
   |**Naam**|Nadat u de voorgaande service-URL hebt opgegeven, vult API Management dit veld in op basis van de JSON.|Een unieke naam voor de API.|
   |**Beschrijving**|Nadat u de voorgaande service-URL hebt opgegeven, vult API Management dit veld in op basis van de JSON.|Een optionele beschrijving van de API.|
   |**URL-schema**|**HTTPS**|De protocollen die toegang hebben tot de API.|
   |**API-URL-achtervoegsel**|*conference*|Het achtervoegsel dat is toegevoegd aan de basis-URL voor de API Management-service. In API Management worden API's herkend aan hun achtervoegsel. Daarom moet het achtervoegsel uniek zijn voor elke API voor een bepaalde uitgever.|
   |**Tags**| |Tags voor het organiseren van API's om de API's te zoeken, te groeperen of te filteren.|
   |**Producten**|**Onbeperkt**|Koppeling van een of meer API's. Elk API Management-exemplaar wordt geleverd met twee voorbeeldproducten: **Starter** en **Onbeperkt**. U publiceert een API door de API te koppelen aan een product, in dit voorbeeld **Onbeperkt**.<br/><br/> U kunt meerdere API's opnemen in een product en deze beschikbaar stellen voor ontwikkelaars via de ontwikkelaarsportal. Als u deze API aan een ander product wilt toevoegen, typt of selecteert u de productnaam. Herhaal deze stap om de API aan meerdere producten toe te voegen. U kunt API's ook later toevoegen aan producten op de pagina **Instellingen**.<br/><br/>  Zie [Een product maken en publiceren](api-management-howto-add-products.md) voor meer informatie over producten.|
   |**Gateways**|**Beheerd**|API-gateway(s) die de API beschikbaar stellen. Dit veld is alleen beschikbaar in services in de categorie **Developer** en **Premium**.<br/><br/>**Beheerd** geeft aan dat de gateway is ingebouwd in de API Management-service en wordt gehost door Microsoft in Azure. [Zelfhostende gateways](self-hosted-gateway-overview.md) zijn alleen beschikbaar in de servicecategorieën Premium en Developer. U kunt deze on-premises of in andere clouds implementeren.<br/><br/> Als er geen gateways zijn geselecteerd, is de API niet beschikbaar en worden uw API-aanvragen niet uitgevoerd.|
   |**Versie van deze API?**|Selecteren of selectie opheffen|Zie [Meerdere versies van uw API publiceren](api-management-get-started-publish-versions.md) voor meer informatie.|

   > [!NOTE]
   > Als u de API wilt publiceren naar API-consumenten, moet u deze koppelen aan een product.

2. Selecteer **Maken**.

Zie de [lijst met bekende problemen en beperkingen](api-management-api-import-restrictions.md) als u problemen hebt met het importeren van een API-definitie.

## <a name="test-the-new-api-in-the-azure-portal"></a>De nieuwe API testen in de Azure-portal

U kunt API-bewerkingen rechtstreeks vanuit de Azure-portal aanroepen. Dit is een handige manier om de bewerkingen te bekijken en te testen.

1. Selecteer **API’s** > **API’s van demovergadering** in de linkernavigatie van uw API Management-exemplaar.
1. Selecteer het tabblad **Test** en selecteer vervolgens **GetSpeakers**. De pagina bevat **queryparameters** en **kopteksten**, indien van toepassing. De **OCP-APIM-Subscription-Key** wordt automatisch ingevuld voor de abonnementssleutel die is gekoppeld aan deze API.
1. Selecteer **Verzenden**.

   :::image type="content" source="media/import-and-publish/01-import-first-api-01.png" alt-text="API testen in Azure Portal":::

   De back-end reageert met **200 OK** en enkele gegevens.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Uw eerste API importeren
> * De API testen in Azure Portal

Ga verder met de volgende zelfstudie voor informatie over het maken en publiceren van een product:

> [!div class="nextstepaction"]
> [Een product maken en publiceren](api-management-howto-add-products.md)
