---
title: 'Zelfstudie: een product maken en publiceren in Azure API Management'
description: In deze zelfstudie gaat u een product maken en publiceren in Azure API Management. Zodra het is gepubliceerd, kunnen ontwikkelaars de API's van het product gaan gebruiken.
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: d0420b92fc94e0a1a9c8a4057f419a57a9909223
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "100545153"
---
# <a name="tutorial-create-and-publish-a-product"></a>Zelfstudie: Een product maken en publiceren  

In Azure API Management bevat een [*product*](api-management-terminology.md#term-definitions) een of meer API's, evenals een gebruiksquotum en de gebruiksvoorwaarden. Zodra een product is gepubliceerd, kunnen ontwikkelaars zich abonneren op het product en de API's van het product gaan gebruiken.  

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een product maken en publiceren
> * Een API toevoegen aan het product

:::image type="content" source="media/api-management-howto-add-products/added-product.png" alt-text="API Management-producten in portal":::


## <a name="prerequisites"></a>Vereisten

+ Informatie over de [terminologie van Azure API Management](api-management-terminology.md).
+ Voltooi de volgende snelstartgids: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md).
+ Voltooi ook de volgende zelfstudie: [Uw eerste API importeren en publiceren](import-and-publish.md).

## <a name="create-and-publish-a-product"></a>Een product maken en publiceren

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Meld u aan bij de Azure-portal en ga naar uw API Management-exemplaar.
1. Selecteer in het linkernavigatievenster **Products** (Producten) >  **+ Add** (Toevoegen).
1.  Voer in het venster **Add product** (Product toevoegen) de waarden in die in de volgende tabel worden beschreven om uw product te maken.

    :::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-01.png" alt-text="Product toevoegen in de portal":::

    | Naam                     | Beschrijving                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Weergavenaam             | De naam zoals u wilt dat deze wordt weergegeven in de [ontwikkelaarsportal](api-management-howto-developer-portal.md).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Beschrijving              | Geef informatie op over het product, zoals het doel, de API's waartoe het toegang geeft, en andere details.                                                                                                                                               |
    | Status                    | Selecteer **Published** (Gepubliceerd) als u het product wilt publiceren. Voordat de API's in een product kunnen worden aangeroepen, moet het product worden gepubliceerd. Nieuwe producten worden standaard niet-gepubliceerd, en zijn alleen zichtbaar voor gebruikers in de groep **Beheerders**.                                                                                      |
    | Abonnement is vereist    | Selecteer dit als een gebruiker een abonnement moet hebben om het product te kunnen gebruiken.                                                                                                                                                                                                                                   |
    | Goedkeuring vereist        | Selecteer dit als u wilt dat een beheerder abonnementspogingen voor dit product beoordeelt en accepteert of weigert. Als dit niet geselecteerd is, worden abonnementspogingen automatisch goedgekeurd.                                                                                                                         |
    | Limiet voor het aantal abonnementen | Het aantal meerdere gelijktijdige abonnementen optioneel beperken.                                                                                                                                                                                                                                |
    | Juridische voorwaarden              | U kunt ook de gebruiksvoorwaarden voor het product opnemen, die abonnees moeten accepteren om het product te kunnen gebruiken.                                                                                                                                                                                                             |
    | API's                     | Selecteer een of meer API's. U kunt ook API's toevoegen na het maken van het product. Zie [API's toevoegen aan een product](#add-apis-to-a-product) verderop in dit artikel voor meer informatie. |

3. Klik op **Create** (Maken) om een nieuw product te maken.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u Azure CLI wilt gaan gebruiken:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Voer de opdracht [AZ APIM product Create](/cli/azure/apim/product#az_apim_product_create) uit om een product te maken:

```azurecli
az apim product create --resource-group apim-hello-word-resource-group \
    --product-name "Contoso product" --product-id contoso-product \
    --service-name apim-hello-world --subscription-required true \
    --state published --description "This is a test."
```

U kunt verschillende waarden opgeven voor uw product:

   | Parameter | Beschrijving |
   |-----------|-------------|
   | `--product-name` | De naam zoals u wilt dat deze wordt weergegeven in de [ontwikkelaarsportal](api-management-howto-developer-portal.md). |
   | `--description`  | Geef informatie op over het product, zoals het doel, de API's waartoe het toegang geeft, en andere details. |
   | `--state`        | Selecteer **gepubliceerd** als u het product wilt publiceren. Voordat de API's in een product kunnen worden aangeroepen, moet het product worden gepubliceerd. Nieuwe producten worden standaard niet-gepubliceerd, en zijn alleen zichtbaar voor gebruikers in de groep **Beheerders**. |
   | `--subscription-required` | Selecteer dit als een gebruiker een abonnement moet hebben om het product te kunnen gebruiken. |
   | `--approval-required` | Selecteer dit als u wilt dat een beheerder abonnementspogingen voor dit product beoordeelt en accepteert of weigert. Als dit niet geselecteerd is, worden abonnementspogingen automatisch goedgekeurd. |
   | `--subscriptions-limit` | Het aantal meerdere gelijktijdige abonnementen optioneel beperken.|
   | `--legal-terms`         | U kunt ook de gebruiksvoorwaarden voor het product opnemen, die abonnees moeten accepteren om het product te kunnen gebruiken. |

Als u uw huidige producten wilt zien, gebruikt u de opdracht [AZ APIM product List](/cli/azure/apim/product#az_apim_product_list) :

```azurecli
az apim product list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

U kunt een product verwijderen met behulp van de opdracht [AZ APIM product delete](/cli/azure/apim/product#az_apim_product_delete) :

```azurecli
az apim product delete --product-id contoso-product \
    --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --delete-subscriptions true
```

---

### <a name="add-more-configurations"></a>Meer configuraties toevoegen

Ga door met het configureren van het product nadat u het hebt opgeslagen. Selecteer in uw API Management-exemplaar het product in het venster **Products** (Producten). Toevoegen of bijwerken:

|Item   |Beschrijving  |
|---------|---------|
|Instellingen     |    Metagegevens en status van product     |
|API's     |  API's die zijn gekoppeld aan het product       |
|[Beleidsregels](api-management-howto-policies.md)     |  Op product-API's toegepast beleid      |
|Toegangsbeheer     |  Zichtbaarheid van het product voor ontwikkelaars of gasten       |
|[Abonnementen](api-management-subscriptions.md)    |    Productabonnees     |

## <a name="add-apis-to-a-product"></a>API's toevoegen aan een product

Producten zijn koppelingen van een of meer API's. U kunt een aantal API's opnemen en deze beschikbaar stellen voor ontwikkelaars via de ontwikkelaarsportal. Tijdens het maken van het product kunt u een of meer bestaande API's toevoegen. U kunt ook later API's aan het product toevoegen, via de pagina met **instellingen** van het product of tijdens het maken van een API.

Ontwikkelaars moeten zich eerst abonneren op een product om toegang tot de API te krijgen. Wanneer ontwikkelaars zich abonneren, ontvangen ze een abonnementssleutel die toegang biedt tot elke API in het betreffende product. Als u de APIM-abonnementssleutel hebt gemaakt, bent u al een beheerder en bent u standaard geabonneerd op elk product.

### <a name="add-an-api-to-an-existing-product"></a>Een API toevoegen aan een bestaand product

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer **Products** (Producten) in de linkernavigatie van uw API Management-exemplaar.
1. Selecteer een product en selecteer vervolgens **API's**.
1. Selecteer **+ Toevoegen**.
1. Selecteer een of meer API's en vervolgens **Select** (Selecteren).

:::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-02.png" alt-text="API toevoegen aan bestaand product":::

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Als u uw beheerde Api's wilt zien, gebruikt u de opdracht [AZ APIM API List](/cli/azure/apim/api#az_apim_api_list) :

   ```azurecli
   az apim api list --resource-group apim-hello-word-resource-group \
       --service-name apim-hello-world --output table
   ```

1. Als u een API wilt toevoegen aan uw product, voert u de opdracht [AZ APIM product API add](/cli/azure/apim/product/api#az_apim_product_api_add) uit:

   ```azurecli
   az apim product api add --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --product-id contoso-product \
       --service-name apim-hello-world
   ```

1. Controleer de toevoeging met behulp van de opdracht [AZ APIM product API List](/cli/azure/apim/product/api#az_apim_product_api_list) :

   ```azurecli
   az apim product api list --resource-group apim-hello-word-resource-group \
       --product-id contoso-product --service-name apim-hello-world --output table
   ```

U kunt een API verwijderen van een product met behulp van de opdracht [AZ APIM product API delete](/cli/azure/apim/product/api#az_apim_product_api_delete) :

```azurecli
az apim product api delete --resource-group apim-hello-word-resource-group \
    --api-id demo-conference-api --product-id contoso-product \
    --service-name apim-hello-world
```

---

> [!TIP]
> U kunt het abonnement van een gebruiker op een product maken of bijwerken met aangepaste abonnementssleutels via een [REST API](/rest/api/apimanagement/2019-12-01/subscription/createorupdate) of een PowerShell-opdracht.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een product maken en publiceren
> * Een API toevoegen aan het product

Ga door naar de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Create blank API and mock API responses](mock-api-responses.md) (Lege API en mock-API-reacties maken)
