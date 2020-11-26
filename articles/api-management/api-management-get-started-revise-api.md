---
title: 'Zelfstudie: revisies gebruiken in API Management om veilig vaste wijzigingen aan te brengen'
titleSuffix: Azure API Management
description: Volg de stappen van deze zelfstudie voor meer informatie over hoe u vaste wijzigingen aanbrengt met revisies in API Management.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: apimpm
ms.openlocfilehash: 3804bfb2a269c431b1a00947f5c7613566a78f49
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377484"
---
# <a name="tutorial-use-revisions-to-make-non-breaking-api-changes-safely"></a>Zelfstudie: Revisies gebruiken om vaste API-wijzigingen veilig aan te brengen
Wanneer uw API klaar is voor gebruik en daadwerkelijk wordt ingezet door ontwikkelaars, moet u op een bepaald moment wijzigingen aanbrengen aan die API, zonder dat dit gevolgen heeft voor clients die de API aanroepen. Het is ook handig om ontwikkelaars te informeren over de aangebrachte wijzigingen. 

Gebruik in Azure API Management *revisies* om vaste API-wijzigingen aan te brengen, zodat u wijzigingen veilig kunt modelleren en testen. Wanneer u klaar bent, kunt u een revisie actueel maken en uw huidige API vervangen. 

Zie [Versies en revisies](https://azure.microsoft.com/blog/versions-revisions/) en [API-versies met Azure API Management](https://azure.microsoft.com/blog/api-versioning-with-azure-api-management/) voor meer achtergrondinformatie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een nieuwe revisie toevoegen
> * Vaste wijzigingen aanbrengen in uw revisie
> * Uw revisie actualiseren en een logboekvermelding over de wijziging toevoegen
> * Door de portal voor ontwikkelaars browsen om de wijzigingen en het logboek in te zien

:::image type="content" source="media/api-management-getstarted-revise-api/azure-portal.png" alt-text="API-revisies in Azure Portal":::

## <a name="prerequisites"></a>Vereisten

+ Informatie over de [terminologie van Azure API Management](api-management-terminology.md).
+ Voltooi de volgende snelstartgids: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md).
+ Voltooi ook de volgende zelfstudie: [Uw eerste API importeren en publiceren](import-and-publish.md).

## <a name="add-a-new-revision"></a>Een nieuwe revisie toevoegen

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en ga naar uw API Management-exemplaar.
1. Selecteer **API's**.
2. Selecteer **Demo Conference API** in de lijst met API's (of een andere API die u wilt wijzigen).
3. Selecteer het tabblad **Revisies**.
4. Selecteer **+ Revisie toevoegen**.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-01-add-new-revision.png" alt-text="API-revisie toevoegen":::

    > [!TIP]
    > U kunt ook **Revisie toevoegen** selecteren in het contextmenu ( **...** ) van de API.

5. Geef een beschrijving voor de nieuwe revisie, om te onthouden waar deze voor wordt gebruikt.
6. Selecteer **Maken**.
7. Uw nieuwe revisie wordt nu gemaakt.

    > [!NOTE]
    > Uw oorspronkelijke API blijft in **Revisie 1**. Dit is de revisie die uw gebruikers blijven aanroepen, totdat u ervoor kiest om een andere revisie de huidige te maken.

## <a name="make-non-breaking-changes-to-your-revision"></a>Vaste wijzigingen aanbrengen in uw revisie

1. Selecteer **Demo Conference API** in de lijst met API's.
1. Selecteer boven in het scherm het tabblad **Ontwerp**.
1. U ziet dat de **revisieselector** (direct boven het tabblad Ontwerp) **Revisie 2** aangeeft als de huidige selectie.

    > [!TIP]
    > Gebruik de revisieselector om te schakelen tussen de wijzigingen waaraan u wilt werken.
1. Klik op **+ Bewerking toevoegen**.
1. Stel uw nieuwe bewerking in als **POST**, en de naam, weergavenaam en URL van de bewerking als **test**
1. **Sla** uw nieuwe bewerking op.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-02-make-changes.png" alt-text="Revisie wijzigen":::
1. U hebt nu een wijziging aangebracht in **Revisie 2**. Gebruik de **revisieselector** bovenaan de pagina om over te schakelen naar **Revisie 1**.
1. Merk op dat uw nieuwe bewerking niet wordt weergegeven in **Revisie 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Uw revisie actualiseren en een logboekvermelding over de wijziging toevoegen

1. Klik op het tabblad **Revisies** in het menu aan de bovenkant van de pagina.
1. Open het contextmenu ( **...** ) voor **Revisie 2**.
1. Selecteer **Instellen als huidige**.
1. Schakel het selectievakje **Op het openbare wijzigingenlogboek voor deze API plaatsen** in als u opmerkingen over deze wijziging wilt plaatsen. Geef een beschrijving op voor de wijziging die aan ontwikkelaars wordt weergegeven, bijvoorbeeld: **Revisies testen. Nieuwe 'test'-bewerking toegevoegd.**
1. **Revisie 2** is nu actueel.

    :::image type="content" source="media/api-management-getstarted-revise-api/revisions-menu.png" alt-text="Het menu Revisie in het venster Revisies":::


## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Door de portal voor ontwikkelaars browsen om de wijzigingen en het logboek in te zien

Als u de [ontwikkelaarsportal](api-management-howto-developer-portal-customize.md) hebt geprobeerd, kunt u de wijzigingen in de API en het wijzigingslogboek bekijken.

1. Selecteer **API's** in de Azure-portal.
1. Selecteer **Ontwikkelaarsportal** in het menu bovenaan.
1. Selecteer **API's** in de ontwikkelaarsportal en selecteer vervolgens **Demo Conference API**.
1. U ziet dat uw nieuwe **test** bewerking nu beschikbaar is.
1. Selecteer **Changelog** bij de naam van de API.
1. U ziet dat de vermelding van het wijzigingslogboek in deze lijst wordt weergegeven.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een nieuwe revisie toevoegen
> * Vaste wijzigingen aanbrengen in uw revisie
> * Uw revisie actualiseren en een logboekvermelding over de wijziging toevoegen
> * Door de portal voor ontwikkelaars browsen om de wijzigingen en het logboek in te zien

Ga door naar de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Meerdere versies van uw API publiceren](api-management-get-started-publish-versions.md)
