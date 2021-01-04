---
title: 'Zelfstudie: versies van uw API met Azure API Management publiceren'
description: Volg de stappen in deze zelfstudie voor informatie over het publiceren van meerdere API-versies in Azure API Management.
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: apimpm
ms.openlocfilehash: 4a107b4cc0dbf0b0845211ca64691fb0e792a47c
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679097"
---
# <a name="tutorial-publish-multiple-versions-of-your-api"></a>Zelfstudie: Meerdere versies van uw API publiceren 

Er zijn tijden wanneer het niet praktisch is dat alle aanroepers voor uw API dezelfde versie gebruiken. Wanneer aanroepers willen upgraden naar een nieuwere versie willen ze een gemakkelijk te begrijpen benadering. Zoals u in deze zelfstudie kunt zien, is het mogelijk om meerdere *versies* op te geven in Azure API Management. 

Zie [Versies & revisies](https://azure.microsoft.com/blog/versions-revisions/) voor achtergrondinformatie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een nieuwe versie toevoegen aan een bestaande API
> * Een versieschema kiezen
> * Voeg de versie toe aan een product
> * Blader door de portal voor ontwikkelaars om de versie te zien

:::image type="content" source="media/api-management-getstarted-publish-versions/azure-portal.png" alt-text="Versies weergegeven in het Azure-portaal":::

## <a name="prerequisites"></a>Vereisten

+ Informatie over de [terminologie van Azure API Management](api-management-terminology.md).
+ Voltooi de volgende snelstartgids: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md).
+ Voltooi ook de volgende zelfstudie: [Uw eerste API importeren en publiceren](import-and-publish.md).

## <a name="add-a-new-version"></a>Een nieuwe versie toevoegen

1. Blader in het [Azure-portaal](https://portal.azure.com) naar uw API Management-exemplaar.
1. Selecteer **API's**.
1. Selecteer **Demo Conference API** in de lijst met API's. 
1. Selecteer het contextmenu ( **...** ) naast **Demo Conference API**.
1. Selecteer **Versie toevoegen**.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version-menu.png" alt-text="Contextmenu van API - versie toevoegen":::


> [!TIP]
> Versies kunnen ook ingeschakeld worden wanneer u een nieuwe API maakt. Selecteer **Versie van deze API?** in het scherm **API toevoegen**.

## <a name="choose-a-versioning-scheme"></a>Kies een versiebeheerschema

In Azure API Management kiest u hoe oproepende functies de API-versie opgeven door een *schema voor versiebeheer* te selecteren: **pad, header** of **querytekenreeks**. In het volgende voorbeeld wordt *pad* gebruikt als schema voor versiebeheer.

Voer de waarden in uit de volgende tabel. Selecteer vervolgens **Maken** om uw versie te maken.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version.png" alt-text="Versievenster toevoegen":::



|Instelling   |Waarde  |Beschrijving  |
|---------|---------|---------|
|**Naam**     |  *demo-conference-api-v1*       |  Unieke naam in uw API Management-exemplaar.<br/><br/>Omdat een versie een nieuwe API is, gebaseerd op de [revisie](api-management-get-started-revise-api.md) van een API, is deze instelling de naam van de nieuwe API.   |
|**Schema voor versiebeheer**     |  **Pad**       |  De manier waarop oproepende functie de API-versie opgeven.     |
|**Versie-id**     |  *v1*       |  Schema-specifieke indicator van de versie. Voor **Pad**, het achtervoegsel voor het URL-pad van de API. <br/><br/> Als u **Header** of **Querytekenreeks** selecteert, voer dan een extra waarde in: de naam van de parameter voor de header of querytekenreeks.<br/><br/> Er wordt een gebruiksvoorbeeld weergegeven.        |
|**Producten**     |  **Onbeperkt**       |  Optioneel een of meer producten waaraan de API-versie gekoppeld is. Als u de API wilt publiceren, moet u deze koppelen aan een product. U kunt de versie later ook [toevoegen aan een product](#add-the-version-to-a-product).      |

Nadat u de versie hebt gemaakt, wordt deze weergegeven onder **Demo Conference API** in de API-lijst. U ziet nu twee API's: **Origineel** en **v1**.

![Versies vermeld in een API in Azure Portal](media/api-management-getstarted-publish-versions/version-list.png)

U kunt nu **v1** bewerken en configureren als een API die verschilt van het **Origineel**. Wijzigingen in één versie hebben geen invloed op een andere.

> [!Note]
> Als u een versie aan een niet-samengestelde API toevoegt, wordt automatisch ook een **Origineel** gemaakt. Deze versie reageert op de standaard-URL. Een Originele versie maken zorgt ervoor dat eventuele bestaande aanroepfuncties niet worden onderbroken door het proces van het toevoegen van een versie. Als u een nieuwe API met versies maakt die aan het begin zijn ingeschakeld, wordt geen Origineel gemaakt.

## <a name="add-the-version-to-a-product"></a>Voeg de versie toe aan een product

Als aanroepers de nieuwe versie willen zien, moet deze worden toegevoegd aan een *product*. Als u de versie nog niet aan een product hebt toegevoegd, dan kunt u deze op elk gewenst moment toevoegen aan een product.

Bijvoorbeeld om de versie toe te voegen aan het **Onbeperkt** product:
1. Blader in het Azure-portaal naar uw API Management-exemplaar.
1. Selecteer **Producten** > **Onbeperkt** > **API's** >  **+ Toevoegen**.
1. Selecteer **Demo Conference API**, versie **v1**.
1. Klik op **Selecteren**.

:::image type="content" source="media/api-management-getstarted-publish-versions/08-add-multiple-versions-03-add-version-product.png" alt-text="Versie toevoegen aan het product":::

## <a name="browse-the-developer-portal-to-see-the-version"></a>Blader door de portal voor ontwikkelaars om de versie te zien

Als u het [ontwikkelaarsportaal](api-management-howto-developer-portal-customize.md) heeft uitgeprobeerd, kunt u daar API-versies terugvinden.

1. Selecteer **ontwikkelaarsportal** in het menu bovenaan.
2. Selecteer **API's** en selecteer vervolgens **Demo Conference API**.
3. U ziet een vervolgkeuzelijst met meerdere versies naast de naam van de API.
4. Selecteer **v1**.
5. U ziet de **Verzoek-URL** van de eerste bewerking in de lijst. Het laat zien dat het API URL-pad **v1** bevat.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een nieuwe versie toevoegen aan een bestaande API
> * Een versieschema kiezen 
> * Voeg de versie toe aan een product
> * Blader door de portal voor ontwikkelaars om de versie te zien

Ga door naar de volgende zelfstudie:

> [!div class="nextstepaction"]
> [De stijl van de pagina's van de ontwikkelaarsportal aanpassen](api-management-howto-developer-portal-customize.md)
