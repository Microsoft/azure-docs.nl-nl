---
title: Beheerde identiteiten gebruiken in communicatie Services (.NET)
titleSuffix: An Azure Communication Services quickstart
description: Met beheerde identiteiten kunt u toegang verlenen tot Azure Communication Services vanuit toepassingen die worden uitgevoerd in azure Vm's, functie-apps en andere resources.
services: azure-communication-services
author: stefang931
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 12/04/2020
ms.author: gistefan
ms.reviewer: mikben
ms.openlocfilehash: 9fd8a17deeb49d836ff5902042bdb88696e29f31
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417867"
---
# <a name="use-managed-identities-net"></a>Beheerde identiteiten (.NET) gebruiken

Ga aan de slag met Azure Communication Services door beheerde identiteiten te gebruiken in .NET. De communicatie Services beheren en SMS-client bibliotheken ondersteunen Azure Active Directory-verificatie (Azure AD) met [beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md).

Deze Quick Start laat zien hoe u toegang tot de beheer-en SMS-client bibliotheken kunt machtigen vanuit een Azure-omgeving die beheerde identiteiten ondersteunt. Ook wordt beschreven hoe u uw code in een ontwikkel omgeving kunt testen.

## <a name="prerequisites"></a>Vereisten

 - Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free)
 - Een actieve Communication Services-resource en verbindingsreeks. [Een Communication Services-resource maken](https://docs.microsoft.com/azure/communication-services/quickstarts/create-communication-resource?tabs=windows&pivots=platform-azp).

## <a name="setting-up"></a>Instellen

### <a name="enable-managed-identities-on-a-virtual-machine-or-app-service"></a>Beheerde identiteiten op een virtuele machine of app service inschakelen

Beheerde identiteiten moeten zijn ingeschakeld op de Azure-resources die u wilt autoriseren. Zie een van de volgende artikelen voor meer informatie over het inschakelen van beheerde identiteiten voor Azure-resources:

- [Azure-portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure-CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sjabloon](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Client bibliotheken Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [App-Services](../../app-service/overview-managed-identity.md)

#### <a name="assign-azure-roles-with-the-azure-portal"></a>Azure-rollen toewijzen met de Azure Portal

1. Navigeer naar de Azure Portal.
1. Navigeer naar de Azure Communication Service-Resource.
1. Ga naar Access Control (IAM) menu-> + add-> roltoewijzing toevoegen.
1. Selecteer de rol ' Inzender ' (dit is de enige ondersteunde rol).
1. Selecteer ' door de gebruiker toegewezen beheerde identiteit ' (of een ' door het systeem toegewezen beheerde identiteit ') en selecteer vervolgens de gewenste identiteit. Sla uw selectie op.

![Beheerde identiteits functie](media/managed-identity-assign-role.png)

#### <a name="assign-azure-roles-with-powershell"></a>Azure-rollen toewijzen met Power shell

Als u rollen en machtigingen wilt toewijzen met behulp van Power shell, raadpleegt u [Azure-roltoewijzingen toevoegen of verwijderen met Azure PowerShell](../../../articles/role-based-access-control/role-assignments-powershell.md)

## <a name="add-managed-identity-to-your-communication-services-solution"></a>Beheerde identiteit toevoegen aan uw oplossing voor communicatie Services

### <a name="install-the-client-library-packages"></a>De client bibliotheek pakketten installeren

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Configuration
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>De client bibliotheek pakketten gebruiken

Voeg de volgende `using` instructies toe aan uw code voor het gebruik van de identiteits-en Azure Storage-client bibliotheken van Azure.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Configuration;
using Azure.Communication.Sms;
```

In de onderstaande voor beelden wordt gebruikgemaakt van de [DefaultAzureCredential](https://docs.microsoft.com/dotnet/api/azure.identity.defaultazurecredential). Deze referentie is geschikt voor productie-en ontwikkelings omgevingen.

### <a name="create-an-identity-and-issue-a-token"></a>Een identiteit maken en een token uitgeven

In het volgende code voorbeeld ziet u hoe u een service-client object met Azure Active Directory-tokens maakt, en vervolgens de client gebruikt om een token uit te geven voor een nieuwe gebruiker:

```csharp
     public async Task<Response<CommunicationUserToken>> CreateIdentityAndIssueTokenAsync(Uri resourceEdnpoint) 
     {
          TokenCredential credential = new DefaultAzureCredential();
     
          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();
     
          var tokenResponse = await client.IssueTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-azure-active-directory-tokens"></a>Een SMS-bericht verzenden met Azure Active Directory-tokens

In het volgende code voorbeeld ziet u hoe u een service-client object met Azure Active Directory-tokens maakt en vervolgens de-client gebruikt om een SMS-bericht te verzenden:

```csharp

     public async Task SendSmsAsync(Uri resourceEndpoint, PhoneNumber from, PhoneNumber to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
     
          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SendSmsOptions { EnableDeliveryReport = true } // optional
          );
     }
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over verificatie](../concepts/authentication.md)

U kunt ook het volgende doen:

- [Meer informatie over toegangs beheer op basis van rollen](../../../articles/role-based-access-control/index.yml)
- [Meer informatie over de Azure-identiteits bibliotheek voor .NET](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme)
- [Tokens voor gebruikerstoegang maken](../quickstarts/access-tokens.md)
- [Een sms-bericht verzenden](../quickstarts/telephony-sms/send.md)
- [Meer informatie over sms](../concepts/telephony-sms/concepts.md)
