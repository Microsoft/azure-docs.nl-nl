---
title: Een Azure Active Directory beheerde identiteits toepassing maken vanuit Azure CLI
titleSuffix: An Azure Communication Services quickstart
description: Met beheerde identiteiten kunt u toegang verlenen tot Azure Communication Services vanuit toepassingen die worden uitgevoerd in azure Vm's, functie-apps en andere resources.
services: azure-communication-services
author: jbeauregardb
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 02/25/2021
ms.author: jbeauregardb
ms.reviewer: mikben
ms.openlocfilehash: f60c7b0b4c89833d489a435fde85896a8f8bd72f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662693"
---
# <a name="authorize-access-with-managed-identity-to-your-communication-resource-in-your-development-environment"></a>Toegang verlenen met beheerde identiteit voor uw communicatie bron in uw ontwikkel omgeving

De Azure Identity client-bibliotheek biedt Azure Active Directory (Azure AD)-token verificatie voor de Azure SDK. De nieuwste versies van de Azure Communication Services-client bibliotheken voor .NET, Java, python en Java script kunnen worden geïntegreerd met de Azure Identity-bibliotheek om een OAuth 2,0-token te verkrijgen voor autorisatie van aanvragen van Azure Communication Services.

Een voor deel van de Azure Identity client-bibliotheek is dat u dezelfde code kunt gebruiken om te verifiëren in meerdere services, ongeacht of uw toepassing wordt uitgevoerd in de ontwikkel omgeving of in Azure. De Azure Identity client-bibliotheek verifieert een beveiligingsprincipal. Wanneer uw code wordt uitgevoerd in azure, is de beveiligingsprincipal een beheerde identiteit voor Azure-resources. In de ontwikkel omgeving bestaat de beheerde identiteit niet, zodat de client bibliotheek de gebruiker of een geregistreerde toepassing verifieert voor test doeleinden.

## <a name="prerequisites"></a>Vereisten

 - Azure CLI. [Installatie handleiding](https://docs.microsoft.com/cli/azure/install-azure-cli)
 - Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free)

## <a name="setting-up"></a>Instellen

Beheerde identiteiten moeten zijn ingeschakeld op de Azure-resources die u wilt autoriseren. Zie een van de volgende artikelen voor meer informatie over het inschakelen van beheerde identiteiten voor Azure-resources:

- [Azure-portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure-CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sjabloon](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Client bibliotheken Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [App-Services](../../app-service/overview-managed-identity.md)

## <a name="authenticate-a-registered-application-in-the-development-environment"></a>Een geregistreerde toepassing verifiëren in de ontwikkel omgeving

Als uw ontwikkel omgeving geen ondersteuning biedt voor eenmalige aanmelding of aanmelding via een webbrowser, kunt u een geregistreerde toepassing gebruiken om te verifiëren vanuit de ontwikkel omgeving.

### <a name="creating-an-azure-active-directory-registered-application"></a>Een Azure Active Directory geregistreerde toepassing maken

Als u een geregistreerde toepassing vanuit de Azure-CLI wilt maken, moet u zijn aangemeld bij het Azure-account waarin u wilt dat de bewerkingen worden uitgevoerd. U kunt dit doen door de opdracht te gebruiken `az login` en uw referenties in te voeren in de browser. Zodra u bent aangemeld bij uw Azure-account vanuit de CLI, kunt u de opdracht aanroepen `az ad sp create-for-rbac` om de geregistreerde toepassing te maken.

In de volgende voor beelden wordt de Azure CLI gebruikt om een nieuwe geregistreerde toepassing te maken

```azurecli
az ad sp create-for-rbac --name <application-name> 
```

Met de `az ad sp create-for-rbac` opdracht wordt een lijst met Service-Principal-eigenschappen in JSON-indeling geretourneerd. Kopieer deze waarden zodat u ze kunt gebruiken om de vereiste omgevings variabelen in de volgende stap te maken.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```
> [!IMPORTANT]
> Het kan enkele minuten duren voordat Azure-roltoewijzingen worden doorgegeven.

#### <a name="set-environment-variables"></a>Omgevingsvariabelen instellen

De Azure Identity client-bibliotheek leest waarden uit drie omgevings variabelen tijdens runtime om de toepassing te verifiëren. De volgende tabel beschrijft de waarde die moet worden ingesteld voor elke omgevings variabele.

|Omgevingsvariabele|Waarde
|-|-
|`AZURE_CLIENT_ID`|`appId` waarde van de gegenereerde JSON 
|`AZURE_TENANT_ID`|`tenant` waarde van de gegenereerde JSON
|`AZURE_CLIENT_SECRET`|`password` waarde van de gegenereerde JSON

> [!IMPORTANT]
> Nadat u de omgevings variabelen hebt ingesteld, sluit u het console venster en opent u het opnieuw. Als u Visual Studio of een andere ontwikkel omgeving gebruikt, moet u deze mogelijk opnieuw opstarten zodat de nieuwe omgevings variabelen kunnen worden geregistreerd.


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over verificatie](../concepts/authentication.md)

U kunt ook het volgende doen:

- [Meer informatie over Azure Identity Library](/dotnet/api/overview/azure/identity-readme)
