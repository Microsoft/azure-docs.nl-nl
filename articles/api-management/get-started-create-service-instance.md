---
title: 'Quickstart: een Azure API Management-exemplaar maken'
description: Maak een nieuw exemplaar van de Azure API Management-service in Azure Portal.
author: vladvino
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/08/2020
ms.author: apimpm
ms.openlocfilehash: 66d0874536b358b720b43780d6ecce879ac866ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90708203"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-portal"></a>Quickstart: Een nieuw exemplaar van het API Management-service-exemplaar maken in Azure Portal

APIM (API Management) helpt organisaties bij het publiceren van API's naar externe en interne ontwikkelaars, en naar partnerontwikkelaars om alle mogelijkheden van hun gegevens en services te ontsluiten. API Management beschikt over de competenties die belangrijk zijn voor een geslaagd API-programma via ontwikkelaarsbetrokkenheid, zakelijke inzichten, analytische gegevens, beveiliging en bescherming. Met APIM kunt u moderne API-gateways maken en beheren voor bestaande back-endservices die op elke willekeurige locatie worden gehost. Zie het [overzicht](api-management-key-concepts.md) voor meer informatie.

In deze quickstart worden de stappen beschreven voor het maken van een nieuw API Management-exemplaar met behulp van Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

API Management-exemplaar

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-a-new-service"></a>Een nieuwe service maken

1. Selecteer **Een resource maken** in het menu van de Azure-portal. U kunt ook **Een resource maken** selecteren op de **Start** pagina van Azure. 
   
   Selecteer :::image type="content" source="media/get-started-create-service-instance/00-CreateResource-01.png" alt-text="Een resource maken":::

   
1. Selecteer op de pagina **Nieuw** de opties **Integratie** > **API Management**.

   Nieuw Azure API Management-exemplaar maken
   
1. Voer op de pagina **API Management-service** de instellingen in.

   :::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-create-new.png" alt-text="Nieuw exemplaar":::
   
   | Instelling                 | Beschrijving   |                                                                     
   |-------------------------|-----------------------------------------------|
   | **Naam.**                | Een unieke naam voor de API Management-service. De naam kan later niet meer worden gewijzigd. De servicenaam verwijst naar de service en de bijbehorende Azure-resource. <br/> De servicenaam wordt gebruikt om een standaarddomeinnaam te genereren: *\<name\>.azure-api.net.* Zie [Een aangepast domein configureren](configure-custom-domain.md) als u een aangepaste domeinnaam wilt gebruiken. |
   | **Abonnement**          | Het abonnement waarin dit nieuwe service-exemplaar wordt gemaakt.   |
   | **Resourcegroep**      |  Selecteer een nieuwe of bestaande resourcegroep. Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. |
   | **location**:          | Selecteer uit de beschikbare API Management Service-locaties een geografische regio bij u in de buurt. | 
   | **Naam van de organisatie**   | De naam van uw organisatie. Deze naam wordt op een aantal plekken gebruikt, onder andere in de titel van de ontwikkelaarsportal en als afzender bij e-mailmeldingen. |                                                         
   | **E-mailadres van de beheerder** | Het e-mailadres waarnaar alle meldingen afkomstig van **API Management** worden verzonden.   |  
   | **Prijscategorie**        | Selecteer **Developer**-laag om de service te evalueren. Deze laag is niet bedoeld voor productie. Zie [bijwerken en schalen](upgrade-and-scale.md) voor meer informatie over het schalen van API Management-lagen. |

3. Selecteer **Maken**.

    > [!TIP]
    > Het kan tussen de dertig en veertig minuten duren om een API Management-service in deze laag te maken en activeren. Als u **Vastmaken aan dashboard** selecteert, kunt u een nieuwe service gemakkelijker terugvinden.

[!INCLUDE [api-management-navigate-to-instance](../../includes/api-management-navigate-to-instance.md)]

Controleer de eigenschappen van uw service op de pagina **Overzicht**.

   API Management-exemplaar

Als het API Management-service-exemplaar online is, kunt u het gebruiken. Begin met de zelfstudie om [Uw eerste API importeren en publiceren](import-and-publish.md).

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer de resourcegroep niet meer nodig is, kunt u deze stappen volgen om de resourcegroep en alle gerelateerde resources te verwijderen:

1. Zoek en selecteer **Resourcegroepen** in de Azure-portal. U kunt ook **Resourcegroepen** selecteren op de **Start** pagina. 

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-01.png" alt-text="Resourcegroepnavigatie":::

1. Selecteer uw resourcegroep op de pagina **Resourcegroepen**.

   Selecteer :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-02.png" alt-text="Resourcegroep":::.

1. Selecteer **Resourcegroep verwijderen** op de pagina van de resourcegroep. 
   
1. Typ de naam van uw resourcegroep en selecteer **Verwijderen**.

   Resourcegroep verwijderen

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uw eerste API importeren en publiceren](import-and-publish.md)
