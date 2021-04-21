---
title: Back-API Management beveiligen met clientcertificaatverificatie
titleSuffix: Azure API Management
description: Meer informatie over het beheren van clientcertificaten en het beveiligen van back-endservices met behulp van clientcertificaatverificatie in Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 01/26/2021
ms.author: apimpm
ms.openlocfilehash: d5d261368260a1c9658ae0bef8bdf63a7ca6bafe
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750622"
---
# <a name="secure-backend-services-using-client-certificate-authentication-in-azure-api-management"></a>Back-endservices beveiligen met behulp van verificatie van clientcertificaten in Azure API Management

API Management kunt u de toegang tot de back-endservice van een API beveiligen met behulp van clientcertificaten. Deze handleiding laat zien hoe u certificaten in een Azure API Management service-exemplaar beheert met behulp van de Azure Portal. Ook wordt uitgelegd hoe u een API configureert voor het gebruik van een certificaat voor toegang tot een back-endservice.

U kunt ook API Management beheren met behulp van [API Management REST API](/rest/api/apimanagement/2020-06-01-preview/certificate).

## <a name="certificate-options"></a>Certificaatopties

API Management biedt twee opties voor het beheren van certificaten die worden gebruikt om de toegang tot back-endservices te beveiligen:

* Verwijzen naar een certificaat dat wordt beheerd in [Azure Key Vault](../key-vault/general/overview.md) 
* Voeg een certificaatbestand rechtstreeks in API Management

Het gebruik van Key Vault-certificaten wordt aanbevolen omdat dit helpt om de beveiliging API Management verbeteren:

* Certificaten die zijn opgeslagen in sleutelkluizen kunnen opnieuw worden gebruikt in verschillende services
* Gedetailleerd [toegangsbeleid kan](../key-vault/general/security-overview.md#privileged-access) worden toegepast op certificaten die zijn opgeslagen in sleutelkluizen
* Certificaten die zijn bijgewerkt in de sleutelkluis, worden automatisch geroteerd in API Management. Na de update in de sleutelkluis wordt een certificaat in API Management binnen vier uur bijgewerkt. U kunt het certificaat ook handmatig vernieuwen met behulp van de Azure Portal of via de REST API.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Als u nog geen service API Management service-exemplaar hebt gemaakt, zie Een API Management [service-exemplaar maken.][Create an API Management service instance]
* Uw back-endservice moet zijn geconfigureerd voor verificatie van clientcertificaten. Raadpleeg dit artikel om certificaatverificatie in Azure App Service te [configureren.][to configure certificate authentication in Azure WebSites refer to this article] 
* U moet toegang hebben tot het certificaat en het wachtwoord voor beheer in een Azure-sleutelkluis of uploaden naar de API Management service. Het certificaat moet de **PFX-indeling** hebben. Zelf-ondertekende certificaten zijn toegestaan.

### <a name="prerequisites-for-key-vault-integration"></a>Vereisten voor key vault-integratie

1. Zie Voor stappen voor het maken van een sleutelkluis [Quickstart: Een sleutelkluis maken met behulp van de Azure Portal.](../key-vault/general/quick-create-portal.md)
1. Schakel een door het systeem toegewezen of door de gebruiker toegewezen [beheerde](api-management-howto-use-managed-service-identity.md) identiteit in het API Management in.
1. Wijs een [toegangsbeleid voor een](../key-vault/general/assign-access-policy-portal.md) sleutelkluis toe aan de beheerde identiteit met machtigingen om certificaten uit de kluis op te halen en weer te geven. Het beleid toevoegen:
    1. Navigeer in de portal naar uw sleutelkluis.
    1. Selecteer **Instellingen > Toegangsbeleid > + Toegangsbeleid toevoegen.**
    1. Selecteer **Certificaatmachtigingen** en selecteer **vervolgens Get** en **List.**
    1. Selecteer **in Principal selecteren** de resourcenaam van uw beheerde identiteit. Als u een door het systeem toegewezen identiteit gebruikt, is de principal de naam van uw API Management-exemplaar.
1. Maak of importeer een certificaat in de sleutelkluis. Zie [Quickstart: Een certificaat instellen en ophalen van Azure Key Vault met behulp van Azure Portal](../key-vault/certificates/quick-create-portal.md).

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-a-key-vault-certificate"></a>Een sleutelkluiscertificaat toevoegen

Zie [Vereisten voor key vault-integratie.](#prerequisites-for-key-vault-integration)

> [!CAUTION]
> Wanneer u een sleutelkluiscertificaat gebruikt in API Management, moet u het certificaat, de sleutelkluis of de beheerde identiteit die wordt gebruikt voor toegang tot de sleutelkluis, niet verwijderen.

Een sleutelkluiscertificaat toevoegen aan API Management:

1. Blader in [Azure Portal](https://portal.azure.com) naar uw API Management-exemplaar.
1. Selecteer **onder** Beveiliging **de optie Certificaten.**
1. Selecteer **Certificaten**  >  **+ Toevoegen.**
1. Voer **in Id** een naam van uw keuze in.
1. Selecteer **in Certificaat** de optie **Sleutelkluis**.
1. Voer de id van een sleutelkluiscertificaat in of kies **Selecteren om** een certificaat uit een sleutelkluis te selecteren.
    > [!IMPORTANT]
    > Als u zelf een certificaat-id voor een sleutelkluis in typt, moet u ervoor zorgen dat deze geen versiegegevens heeft. Anders wordt het certificaat niet automatisch geroteerd in API Management na een update in de sleutelkluis.
1. Selecteer **in Clientidentiteit** een door het systeem toegewezen of een bestaande door de gebruiker toegewezen beheerde identiteit. Meer informatie over het [toevoegen of wijzigen van beheerde identiteiten in uw API Management service](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > De identiteit heeft machtigingen nodig om het certificaat uit de sleutelkluis op te halen en weer te geven. Als u de toegang tot de sleutelkluis nog niet hebt geconfigureerd, wordt API Management gevraagd zodat de identiteit automatisch kan worden geconfigureerd met de benodigde machtigingen.
1. Selecteer **Toevoegen**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-kv.png" alt-text="Sleutelkluiscertificaat toevoegen":::

## <a name="upload-a-certificate"></a>Een certificaat uploaden

Een clientcertificaat uploaden naar API Management: 

1. Blader in [Azure Portal](https://portal.azure.com) naar uw API Management-exemplaar.
1. Selecteer **onder Beveiliging** de optie **Certificaten.**
1. Selecteer **Certificaten**  >  **+ Toevoegen.**
1. Voer **in Id** een naam van uw keuze in.
1. Selecteer **in Certificaat** de optie **Aangepast.**
1. Blader om het PFX-certificaatbestand te selecteren en voer het wachtwoord in.
1. Selecteer **Toevoegen**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-add.png" alt-text="Clientcertificaat uploaden":::

Nadat het certificaat is geüpload, wordt het weergegeven in **het venster** Certificaten. Als u veel certificaten hebt, noteert u de vingerafdruk van het gewenste certificaat om een API te configureren voor het gebruik van een clientcertificaat [voor gatewayverificatie.](#configure-an-api-to-use-client-certificate-for-gateway-authentication)

> [!NOTE]
> Als u certificaatketenvalidatie wilt uitschakelen wanneer u bijvoorbeeld een zelf-ondertekend certificaat gebruikt, volgt u de stappen die worden beschreven in [Zelf-ondertekende](#self-signed-certificates)certificaten , verderop in dit artikel.

## <a name="configure-an-api-to-use-client-certificate-for-gateway-authentication"></a>Een API configureren voor het gebruik van een clientcertificaat voor gatewayverificatie

1. Blader in [Azure Portal](https://portal.azure.com) naar uw API Management-exemplaar.
1. Selecteer **API's** onder **API's.**
1. Selecteer een API in de lijst. 
2. Selecteer op **het** tabblad Ontwerpen het editorpictogram in de **sectie Back-end.**
3. In **Gatewayreferenties selecteert** u **Clientcertificaat** en selecteert u uw certificaat in de vervolgkeuzekeuze.
1. Selecteer **Opslaan**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png" alt-text="Clientcertificaat gebruiken voor gatewayverificatie":::

> [!CAUTION]
> Deze wijziging is onmiddellijk van kracht en aanroepen van bewerkingen van die API gebruiken het certificaat om te verifiëren op de back-endserver.

> [!TIP]
> Wanneer een certificaat is opgegeven voor gatewayverificatie voor de back-service van een API, wordt het onderdeel van het beleid voor die API en kan het worden bekeken in de beleidseditor.

## <a name="self-signed-certificates"></a>Zelfondertekende certificaten

Als u zelf-ondertekende certificaten gebruikt, moet u certificaatketenvalidatie uitschakelen voor API Management te communiceren met het back-endsysteem. Anders wordt een 500-foutcode weergegeven. Als u dit wilt configureren, kunt u de [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) PowerShell-cmdlets (voor nieuwe back-end) of (voor bestaande [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) back-end) gebruiken en de `-SkipCertificateChainValidation` parameter instellen op `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="delete-a-client-certificate"></a>Een clientcertificaat verwijderen

Als u een certificaat wilt verwijderen, selecteert u dit en selecteert **u vervolgens Verwijderen** in het contextmenu (**...**).

:::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png" alt-text="Een certificaat verwijderen":::

> [!IMPORTANT]
> Als naar het certificaat wordt verwezen door beleidsregels, wordt er een waarschuwingsscherm weergegeven. Als u het certificaat wilt verwijderen, moet u het certificaat eerst verwijderen uit alle beleidsregels die zijn geconfigureerd om het te gebruiken.

## <a name="next-steps"></a>Volgende stappen

* [API's beveiligen met behulp van verificatie via clientcertificaten in API Management](api-management-howto-mutual-certificates-for-clients.md)
* Meer informatie [over beleid in API Management](api-management-howto-policies.md)


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

