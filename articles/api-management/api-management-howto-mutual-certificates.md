---
title: API Management back-end beveiligen met verificatie op basis van client certificaten
titleSuffix: Azure API Management
description: Meer informatie over het beheren van client certificaten en het beveiligen van back-end-services met verificatie op basis van client certificaten in azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 01/26/2021
ms.author: apimpm
ms.openlocfilehash: 2e4a398ab71878134887fb8fba025cd8aa6122ad
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492819"
---
# <a name="secure-backend-services-using-client-certificate-authentication-in-azure-api-management"></a>Back-end-services beveiligen met verificatie op basis van client certificaten in azure API Management

Met API Management kunt u de toegang tot de back-end-service van een API beveiligen met behulp van client certificaten. In deze hand leiding wordt beschreven hoe u certificaten in een Azure API Management service-exemplaar beheert met behulp van de Azure Portal. Ook wordt uitgelegd hoe u een API configureert voor het gebruik van een certificaat voor toegang tot een back-end-service.

U kunt API Management certificaten ook beheren met behulp van de [API Management rest API](/rest/api/apimanagement/2020-06-01-preview/certificate).

## <a name="certificate-options"></a>Certificaat opties

API Management biedt twee opties voor het beheren van certificaten die worden gebruikt voor het beveiligen van de toegang tot back-end-services:

* Verwijzen naar een certificaat dat wordt beheerd in [Azure Key Vault](../key-vault/general/overview.md) 
* Een certificaat bestand rechtstreeks in API Management toevoegen

Het gebruik van sleutel kluis certificaten wordt aanbevolen omdat deze helpt bij het verbeteren van API Management beveiliging:

* Certificaten die zijn opgeslagen in sleutel kluizen, kunnen opnieuw worden gebruikt in verschillende services
* Gedetailleerd [toegangs beleid](../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) kan worden toegepast op certificaten die zijn opgeslagen in sleutel kluizen
* Certificaten die in de sleutel kluis zijn bijgewerkt, worden automatisch geroteerd in API Management. Na de update in de sleutel kluis wordt een certificaat in API Management bijgewerkt binnen vier uur. U kunt het certificaat ook hand matig vernieuwen met behulp van de Azure Portal of via de beheer REST API.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Als u nog geen API Management service-exemplaar hebt gemaakt, raadpleegt u [een API Management service-exemplaar maken][Create an API Management service instance].
* U moet uw back-end-service configureren voor verificatie van client certificaten. Raadpleeg [dit artikel][to configure certificate authentication in Azure WebSites refer to this article]voor het configureren van certificaat verificatie in de Azure app service. 
* U hebt toegang tot het certificaat en het wacht woord voor beheer in een Azure-sleutel kluis nodig of u kunt het uploaden naar de API Management-service. Het certificaat moet een **PFX** -indeling hebben. Zelfondertekende certificaten zijn toegestaan.

### <a name="prerequisites-for-key-vault-integration"></a>Vereisten voor sleutel kluis integratie

1. Voor stappen voor het maken van een sleutel kluis raadpleegt u [Quick Start: een sleutel kluis maken met behulp van de Azure Portal](../key-vault/general/quick-create-portal.md).
1. Een door het systeem toegewezen of door de gebruiker toegewezen [beheerde identiteit](api-management-howto-use-managed-service-identity.md) inschakelen in het API Management-exemplaar.
1. Wijs een [sleutel kluis toegangs beleid](../key-vault/general/assign-access-policy-portal.md) toe aan de beheerde identiteit met machtigingen om certificaten van de kluis op te halen en weer te geven. Het beleid toevoegen:
    1. Navigeer in de portal naar uw sleutel kluis.
    1. Selecteer **instellingen > toegangs beleid > + toegangs beleid toe te voegen**.
    1. Selecteer **certificaat machtigingen** en selecteer **ophalen** en **lijst**.
    1. Selecteer in **Principal selecteren** de resource naam van uw beheerde identiteit. Als u een door het systeem toegewezen identiteit gebruikt, is de principal de naam van uw API Management-exemplaar.
1. Een certificaat maken of importeren in de sleutel kluis. Zie [Quick Start: een certificaat instellen en ophalen van Azure Key Vault met behulp van de Azure Portal](../key-vault/certificates/quick-create-portal.md).

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-a-key-vault-certificate"></a>Een sleutel kluis certificaat toevoegen

Zie [vereisten voor de integratie van sleutel kluis](#prerequisites-for-key-vault-integration).

> [!CAUTION]
> Wanneer u een sleutel kluis certificaat in API Management gebruikt, moet u ervoor zorgen dat u het certificaat, de sleutel kluis of de beheerde identiteit die wordt gebruikt voor toegang tot de sleutel kluis niet verwijdert.

Een sleutel kluis certificaat toevoegen aan API Management:

1. Blader in [Azure Portal](https://portal.azure.com) naar uw API Management-exemplaar.
1. Selecteer onder **beveiliging** de optie **certificaten**.
1. Selecteer **certificaten**  >  **+ toevoegen**.
1. Voer in **id** de naam van uw keuze in.
1. Selecteer in **certificaat** **sleutel kluis**.
1. Voer de id van een sleutel kluis certificaat in of kies **selecteren** om een certificaat te selecteren in een sleutel kluis.
    > [!IMPORTANT]
    > Als u zelf een certificaat-id voor een sleutel kluis invoert, moet u ervoor zorgen dat deze geen versie-informatie bevat. Anders wordt het certificaat niet automatisch gedraaid in API Management na een update in de sleutel kluis.
1. Selecteer in **client identiteit** een door het systeem toegewezen of een bestaande door de gebruiker toegewezen beheerde identiteit. Meer informatie over het [toevoegen of wijzigen van beheerde identiteiten in uw API Management-service](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > De identiteit heeft machtigingen nodig voor het verkrijgen en vermelden van certificaten uit de sleutel kluis. Als u de toegang tot de sleutel kluis nog niet hebt geconfigureerd, API Management u gevraagd om de identiteit automatisch te configureren met de benodigde machtigingen.
1. Selecteer **Toevoegen**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-kv.png" alt-text="Sleutel kluis certificaat toevoegen":::

## <a name="upload-a-certificate"></a>Een certificaat uploaden

Een client certificaat uploaden naar API Management: 

1. Blader in [Azure Portal](https://portal.azure.com) naar uw API Management-exemplaar.
1. Selecteer onder **beveiliging** de optie **certificaten**.
1. Selecteer **certificaten**  >  **+ toevoegen**.
1. Voer in **id** de naam van uw keuze in.
1. Selecteer in **certificaat** de optie **aangepast**.
1. Blader om het pfx-bestand van het certificaat te selecteren en voer het wacht woord in.
1. Selecteer **Toevoegen**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-add.png" alt-text="Client certificaat uploaden":::

Nadat het certificaat is geüpload, wordt het weer gegeven in het venster **certificaten** . Als u veel certificaten hebt, noteert u de vinger afdruk van het gewenste certificaat om een API te configureren voor het gebruik van een client certificaat voor [Gateway verificatie](#configure-an-api-to-use-client-certificate-for-gateway-authentication).

> [!NOTE]
> Als u de validatie van certificaat ketens wilt uitschakelen wanneer u bijvoorbeeld een zelfondertekend certificaat gebruikt, volgt u de stappen die worden beschreven in [zelfondertekende certificaten](#self-signed-certificates), verderop in dit artikel.

## <a name="configure-an-api-to-use-client-certificate-for-gateway-authentication"></a>Een API configureren voor het gebruik van het client certificaat voor gateway verificatie

1. Blader in [Azure Portal](https://portal.azure.com) naar uw API Management-exemplaar.
1. Onder **api's** selecteert u **api's**.
1. Selecteer een API in de lijst. 
2. Op het tabblad **ontwerpen** selecteert u het pictogram editor in het gedeelte **back-end** .
3. In **Gateway referenties** selecteert u **client certificaat** en selecteert u uw certificaat in de vervolg keuzelijst.
1. Selecteer **Opslaan**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png" alt-text="Client certificaat voor gateway verificatie gebruiken":::

> [!CAUTION]
> Deze wijziging is onmiddellijk van kracht en aanroepen naar bewerkingen van die API gebruiken het certificaat om te verifiëren op de back-endserver.

> [!TIP]
> Wanneer een certificaat wordt opgegeven voor gateway verificatie voor de back-end-service van een API, wordt het een onderdeel van het beleid voor die API en kan het worden weer gegeven in de beleids editor.

## <a name="self-signed-certificates"></a>Zelfondertekende certificaten

Als u zelfondertekende certificaten gebruikt, moet u validatie van de certificaat keten uitschakelen voor API Management om te communiceren met het back-end-systeem. Anders wordt er een fout code van 500 geretourneerd. Als u dit wilt configureren, kunt u de [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) (voor nieuwe back-end) of [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (voor bestaande back-end) Power shell-cmdlets gebruiken en de `-SkipCertificateChainValidation` para meter instellen op `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="delete-a-client-certificate"></a>Een client certificaat verwijderen

Als u een certificaat wilt verwijderen, selecteert u dit en selecteert u vervolgens **verwijderen** in het context menu (**...**).

:::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png" alt-text="Een certificaat verwijderen":::

> [!IMPORTANT]
> Als er door een beleid wordt verwezen naar het certificaat, wordt er een waarschuwings scherm weer gegeven. Als u het certificaat wilt verwijderen, moet u eerst het certificaat verwijderen uit elk beleid dat is geconfigureerd om het te gebruiken.

## <a name="next-steps"></a>Volgende stappen

* [API's beveiligen met behulp van verificatie via clientcertificaten in API Management](api-management-howto-mutual-certificates-for-clients.md)
* Meer informatie over [beleid in API Management](api-management-howto-policies.md)


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: ./api-management-caching-policies.md
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

