---
title: Een Azure Service Fabric-service uitvoeren als een AD-gebruiker of-groep
description: Meer informatie over het uitvoeren van een service als een Active Directory gebruiker of groep op een zelfstandige Windows-cluster Service Fabric.
ms.topic: conceptual
ms.date: 03/29/2018
ms.openlocfilehash: d4a7afc2ddb0f39014a7cf0fd006d7fe23673a95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91840724"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Een service uitvoeren als een Active Directory gebruiker of groep
Op een zelfstandige Windows Server-cluster kunt u een service uitvoeren als een Active Directory gebruiker of groep met behulp van een runas-beleid.  Service Fabric toepassingen worden standaard uitgevoerd onder het account dat door het Fabric.exe proces wordt uitgevoerd. Door toepassingen onder verschillende accounts uit te voeren, zelfs in een gedeelde gehoste omgeving, zijn ze beter te beveiligen tegen elkaar. Houd er rekening mee dat dit Active Directory on-premises binnen uw domein gebruikt en niet Azure Active Directory (Azure AD).  U kunt ook een service uitvoeren als een [beheerd service account voor een groep (gMSA)](service-fabric-run-service-as-gmsa.md).

Door een domein gebruiker of-groep te gebruiken, kunt u toegang krijgen tot andere bronnen in het domein (bijvoorbeeld bestands shares) waaraan machtigingen zijn verleend.

In het volgende voor beeld ziet u een Active Directory gebruiker met de naam *test* User met het domein wachtwoord versleuteld met behulp van een certificaat met de naam *MyCert*. U kunt de `Invoke-ServiceFabricEncryptText` Power shell-opdracht gebruiken om de tekst van de geheime versleuteling te maken. Zie [geheimen beheren in service Fabric-toepassingen](service-fabric-application-secret-management.md) voor meer informatie.

U moet de persoonlijke sleutel van het certificaat implementeren om het wacht woord te ontsleutelen naar de lokale computer met behulp van een out-of-band-methode (in Azure is dit via Azure Resource Manager). Wanneer Service Fabric vervolgens het service pakket implementeert op de machine, kan het geheim ontsleutelen en (samen met de gebruikers naam) verificatie verifiëren met Active Directory om onder deze referenties te worden uitgevoerd.

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```

> [!NOTE] 
> Als u een runas-beleid toepast op een service en het service manifest declareert eindpunt resources met het HTTP-protocol, moet u ook een **SecurityAccessPolicy** opgeven.  Zie [beleid voor beveiligings toegang toewijzen voor HTTP-en HTTPS-eind punten](service-fabric-assign-policy-to-endpoint.md)voor meer informatie. 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Lees de volgende artikelen als volgende stap:
* [Inzicht in het toepassings model](service-fabric-application-model.md)
* [Resources opgeven in een service manifest](service-fabric-service-manifest-resources.md)
* [Een app implementeren](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
