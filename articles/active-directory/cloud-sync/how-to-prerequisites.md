---
title: Vereisten voor Azure AD Connect Cloud synchronisatie in azure AD
description: In dit artikel worden de vereisten en hardwarevereisten beschreven die u nodig hebt voor de Cloud synchronisatie.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0277d4ce263610576178e3844a0665ab6506fbfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579158"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-sync"></a>Vereisten voor Azure AD Connect Cloud synchronisatie
Dit artikel bevat richt lijnen voor het kiezen en gebruiken van Azure Active Directory (Azure AD) Connect Cloud Sync als uw identiteits oplossing.

## <a name="cloud-provisioning-agent-requirements"></a>Vereisten voor de inrichtings agent voor Cloud
U hebt het volgende nodig om Azure AD Connect Cloud synchronisatie te kunnen gebruiken:

- Domein beheerder of ondernemings Administrator referenties voor het maken van de Azure AD Connect Cloud Sync gMSA (door groep beheerd service account) voor het uitvoeren van de Agent service. 
- Een hybride identiteits beheerders account voor uw Azure AD-Tenant die geen gast gebruiker is.
- Een on-premises server voor de inrichtings agent met Windows 2016 of hoger.  Deze server moet een laag 0-server zijn op basis van het [Active Directory administratieve laag model](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).
- On-premises firewall configuraties.

## <a name="group-managed-service-accounts"></a>Door groep beheerde serviceaccounts
Een beheerd service account voor een groep is een beheerd domein account dat automatische wachtwoord beheer, vereenvoudigd Service Principal Name (SPN)-beheer biedt, de mogelijkheid om het beheer te delegeren aan andere beheerders en deze functionaliteit uit te breiden op meerdere servers.  Azure AD Connect Cloud Sync ondersteunt en maakt gebruik van een gMSA voor het uitvoeren van de agent.  Tijdens de installatie wordt u gevraagd om beheerders referenties, om dit account te maken.  Het account wordt weer gegeven als (domain\provAgentgMSA $).  Zie [beheerde service accounts voor groepen](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) voor meer informatie over een gMSA 

### <a name="prerequisites-for-gmsa"></a>Vereisten voor gMSA:
1.  Het Active Directory schema in het forest van het gMSA-domein moet worden bijgewerkt naar Windows Server 2016.
2.  [Power shell RSAT-modules](/windows-server/remote/remote-server-administration-tools) op een domein controller
3.  Op ten minste één domein controller in het domein moet Windows Server 2016 worden uitgevoerd.
4.  Een server die lid is van een domein waarop de agent wordt geïnstalleerd, moet Windows Server 2016 of hoger zijn.

### <a name="custom-gmsa-account"></a>Aangepast gMSA-account
Als u een aangepast gMSA-account maakt, moet u ervoor zorgen dat het account de volgende machtigingen heeft.

|Type |Naam |Access |Van toepassing op| 
|-----|-----|-----|-----|
|Toestaan |gMSA-account |Alle eigenschappen lezen |Onderliggende objecten van apparaat| 
|Toestaan |gMSA-account|Alle eigenschappen lezen |Onderliggend InetOrgPerson-objecten| 
|Toestaan |gMSA-account |Alle eigenschappen lezen |Onderliggende computer objecten| 
|Toestaan |gMSA-account |Alle eigenschappen lezen |Onderliggende foreignSecurityPrincipal-objecten| 
|Toestaan |gMSA-account |Volledig beheer |Objecten van onderliggende groep| 
|Toestaan |gMSA-account |Alle eigenschappen lezen |Onderliggende gebruikers objecten| 
|Toestaan |gMSA-account |Alle eigenschappen lezen |Onderliggende contact personen-objecten| 
|Toestaan |gMSA-account |Gebruikers objecten maken/verwijderen|Dit object en alle onderliggende objecten| 

Zie [beheerde service accounts voor groepen](how-to-install.md#group-managed-service-accounts)voor stappen voor het bijwerken van een bestaande agent voor het gebruik van een gMSA-account.

### <a name="in-the-azure-active-directory-admin-center"></a>In het Azure Active Directory-beheercentrum

1. Maak een alleen-Cloud account voor hybride identiteits beheerders in uw Azure AD-Tenant. Op deze manier kunt u de configuratie van uw Tenant beheren als uw on-premises Services mislukken of niet meer beschikbaar zijn. Meer informatie over het [toevoegen van een Hybrid Identity Administrator-account voor de Cloud](../fundamentals/add-users-azure-active-directory.md). Het volt ooien van deze stap is van cruciaal belang om ervoor te zorgen dat de Tenant niet wordt vergrendeld.
1. Voeg een of meer [aangepaste domeinnamen](../fundamentals/add-custom-domain.md) toe aan uw Azure AD-tenant. Uw gebruikers kunnen zich aanmelden met een van deze domeinnamen.

### <a name="in-your-directory-in-active-directory"></a>In uw directory in Active Directory

Voer het [hulp programma IdFix](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) uit om de Directory kenmerken voor te bereiden op synchronisatie.

### <a name="in-your-on-premises-environment"></a>In uw on-premises omgeving

1. Identificeer een hostserver die lid is van een domein en waarop Windows Server 2016 of hoger wordt uitgevoerd, met een minimum van 4 GB RAM en .NET 4.7.1 + runtime.

2. Het Power shell-uitvoerings beleid op de lokale server moet worden ingesteld op niet-gedefinieerde of RemoteSigned.

3. Als er zich een firewall tussen uw servers en Azure AD bevindt, configureert u de volgende items:
    - Zorg ervoor dat agenten *uitgaande* aanvragen voor Azure AD via de volgende poorten kunnen maken:

      | Poortnummer | Hoe dat wordt gebruikt |
      | --- | --- |
      | **80** | Hiermee worden de certificaatintrekkingslijsten (Crl's) gedownload tijdens het valideren van het TLS/SSL-certificaat.  |
      | **443** | Hiermee wordt alle uitgaande communicatie met de service verwerkt. |
      | **8080** (optioneel) | Agents rapporteren hun status elke 10 minuten via poort 8080, als poort 443 niet beschikbaar is. Deze status wordt weer gegeven in de Azure AD-Portal. |

    - Als met uw firewall regels worden afgedwongen op basis van de herkomst van gebruikers, opent u deze poorten voor verkeer dat afkomstig is van Windows-services die als een netwerkservice worden uitgevoerd.
    - Als u met uw firewall of proxy veilige achtervoegsels kunt opgeven, voegt u verbindingen toe aan \* . msappproxy.net en \* . servicebus.Windows.net. Als dat niet het geval is, moet u toegang toestaan tot de [IP-adresbereiken van Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653), die elke week worden bijgewerkt.
    - Uw agenten hebben voor de eerste registratie toegang nodig tot login.windows.net en login.microsoftonline.com. Open uw firewall ook voor deze URL's.
    - Voor validatie van het certificaat kunt u de volgende Url's blok keren: mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 en www \. Microsoft.com:80. Omdat deze URL's worden gebruikt voor certificaatvalidatie met andere Microsoft-producten, is het mogelijk dat u deze URL's al hebt gedeblokkeerd.

    >[!NOTE]
    > Het is niet mogelijk om de inrichtings agent voor Clouds te installeren op Windows Server Core.

### <a name="additional-requirements"></a>Aanvullende vereisten

- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>TLS-vereisten

> [!NOTE]
> Transport Layer Security (TLS) is een protocol dat zorgt voor beveiligde communicatie. Het wijzigen van de TLS-instellingen is van invloed op het hele forest. Zie [Update voor het inschakelen van TLS 1,1 en tls 1,2 als standaard beveiligde protocollen in WinHTTP in Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)voor meer informatie.

Op de Windows-Server die als host fungeert voor de Azure AD Connect Cloud-inrichtings agent moet TLS 1,2 zijn ingeschakeld voordat u deze installeert.

Voer de volgende stappen uit om TLS 1,2 in te scha kelen.

1. Stel de volgende registersleutels in:

    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Start de server opnieuw.

## <a name="known-limitations"></a>Bekende beperkingen

Hier volgen enkele bekende beperkingen:

### <a name="delta-synchronization"></a>Deltasynchronisatie

- Filteren van groeps bereik voor Delta synchronisatie biedt geen ondersteuning voor meer dan 1500 leden.
- Wanneer u een groep verwijdert die wordt gebruikt als onderdeel van het filter bereik van een groep, worden gebruikers die lid zijn van de groep, niet verwijderd. 
- Wanneer u de naam van de organisatie-eenheid of groep in het bereik wijzigt, worden de gebruikers niet verwijderd door Delta synchronisatie.

### <a name="provisioning-logs"></a>Inrichtingslogboeken
- Bij inrichtings Logboeken wordt niet duidelijk onderscheid gemaakt tussen Create-en update-bewerkingen.  Mogelijk wordt er een bewerking voor het maken van een update en een update bewerking voor een maken weer geven.

### <a name="group-re-naming-or-ou-re-naming"></a>Hernoemen van groep of organisatie-eenheid opnieuw
- Als u de naam van een groep of organisatie-eenheid in AD binnen het bereik van een bepaalde configuratie wijzigt, kan de synchronisatie taak voor de Cloud de naam wijziging in AD niet herkennen. De taak wordt niet in quarantaine gezet en blijft in orde.


## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect--cloudsynchronisatie?](what-is-cloud-sync.md)