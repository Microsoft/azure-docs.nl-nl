---
title: Overzicht van Azure Key Vault-beveiliging
description: Een overzicht van beveiligingsfuncties en best practices voor Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: fe88933049ad39de57f879789e8c1b86ed7a54f5
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753268"
---
# <a name="azure-key-vault-security"></a>Azure Key Vault-beveiliging

Azure Key Vault versleutelingssleutels en geheimen (zoals certificaten, verbindingsreeksen en wachtwoorden) in de cloud. Bij het opslaan van gevoelige en bedrijfskritieke gegevens moet u echter stappen ondernemen om de beveiliging van uw kluizen en de gegevens die in deze kluizen zijn opgeslagen te maximaliseren.

Dit artikel bevat een overzicht van beveiligingsfuncties en best practices voor Azure Key Vault.

> [!NOTE]
> Zie De beveiligingsbasislijn voor Azure Key Vault voor een uitgebreide lijst met [beveiligingsaanbevelingen Azure Key Vault.](security-baseline.md)

## <a name="network-security"></a>Netwerkbeveiliging

U kunt de blootstelling van uw kluizen verminderen door op te geven welke IP-adressen er toegang toe hebben. Met de service-eindpunten voor virtuele Azure Key Vault kunt u de toegang tot een opgegeven virtueel netwerk beperken. Met de eindpunten kunt u ook de toegang tot een lijst met IPv4-adresbereiken (internetprotocol versie 4) beperken. Elke gebruiker die verbinding maakt met uw sleutelkluis van buiten deze bronnen, wordt de toegang geweigerd.  Zie Service-eindpunten voor [virtuele netwerken voor meer informatie Azure Key Vault](overview-vnet-service-endpoints.md)

Nadat firewallregels van kracht zijn, kunnen gebruikers alleen gegevens lezen van Key Vault wanneer hun aanvragen afkomstig zijn van toegestane virtuele netwerken of IPv4-adresbereiken. Dit is tevens van toepassing voor toegang tot Key Vault vanuit Azure Portal. Hoewel gebruikers kunnen bladeren naar een sleutelkluis van Azure Portal, kunnen ze mogelijk geen sleutels, geheimen of certificaten weergeven als hun clientcomputer niet in de lijst met toegestane clients staat. Zie Configure [Azure Key Vault firewalls and virtual networks (Firewalls en](network-security.md) virtuele netwerken configureren) voor implementatiestappen

Met Azure Private Link hebt u via een privé-eindpunt in uw virtuele netwerk toegang tot Azure Key Vault en in Azure gehoste services van klanten of partners. Een privé-eindpunt in Azure is een netwerkinterface waarmee u privé en veilig verbinding maakt met een service die door Azure Private Link mogelijk wordt gemaakt. Het privé-eindpunt maakt gebruik van een privé-IP-adres van uw VNet, waardoor de service feitelijk in uw VNet wordt geplaatst. Al het verkeer naar de service kan worden gerouteerd via het privé-eindpunt, zodat er geen gateways, NAT-apparaten, ExpressRoute of VPN-verbindingen of openbare IP-adressen nodig zijn. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U kunt verbinding maken met een exemplaar van een Azure-resource, zodat u het hoogste granulariteit krijgt in toegangsbeheer.  Zie Integrate [Key Vault with Azure Private Link (Integratie van Key Vault met Azure Private Link](private-link-service.md)

## <a name="tls-and-https"></a>TLS en HTTPS

- De Key Vault front-end (gegevensvlak) is een server met meerdere tenants. Dit betekent dat sleutelkluizen van verschillende klanten hetzelfde openbare IP-adres kunnen delen. Om isolatie te bereiken, wordt elke HTTP-aanvraag onafhankelijk van andere aanvragen geverifieerd en geautoriseerd.
- U kunt oudere versies van TLS identificeren om beveiligingsproblemen te melden, maar omdat het openbare IP-adres wordt gedeeld, is het niet mogelijk voor het key vault-serviceteam om oude versies van TLS uit te schakelen voor afzonderlijke sleutelkluizen op transportniveau.
- Met het HTTPS-protocol kan de client deelnemen aan TLS-onderhandeling. **Clients kunnen de meest recente versie van TLS** afdwingen. Wanneer een client dit doet, gebruikt de hele verbinding de bijbehorende niveaubeveiliging. Het feit dat Key Vault oudere TLS-versies nog steeds ondersteunt, is niet nadelig voor de beveiliging van verbindingen met nieuwere TLS-versies.
- Ondanks bekende beveiligingsproblemen in het TLS-protocol is er geen bekende aanval waardoor een kwaadwillende agent informatie kan extraheren uit uw sleutelkluis wanneer de aanvaller een verbinding tot stand kan brengen met een TLS-versie met beveiligingsproblemen. De aanvaller moet zichzelf nog steeds verifiëren en autoriseren. Zolang legitieme clients altijd verbinding maken met recente TLS-versies, is het niet mogelijk dat referenties zijn gelekt uit beveiligingsproblemen bij oude TLS-versies.

## <a name="identity-management"></a>Identiteitsbeheer

Wanneer u een sleutelkluis in een Azure-abonnement maakt, wordt deze automatisch gekoppeld aan de Azure AD-tenant van het abonnement. Iedereen die inhoud uit een kluis probeert te beheren of op te halen, moet worden geverifieerd door Azure AD. In beide gevallen hebben toepassingen toegang tot Key Vault op drie manieren:

- **Alleen toepassing:** de toepassing vertegenwoordigt een service-principal of beheerde identiteit. Deze identiteit is het meest voorkomende scenario voor toepassingen die periodiek toegang moeten hebben tot certificaten, sleutels of geheimen uit de sleutelkluis. Dit scenario werkt alleen als de van de toepassing is opgegeven in het toegangsbeleid en de niet moet `objectId` worden opgegeven of moet `applicationId`  `null` zijn.
- **Alleen gebruiker:** de gebruiker heeft toegang tot de sleutelkluis vanuit elke toepassing die is geregistreerd in de tenant. Voorbeelden van dit type toegang zijn Azure PowerShell en de Azure Portal. Dit scenario werkt alleen als de van de gebruiker is opgegeven in het toegangsbeleid en de niet moet worden `objectId` `applicationId` opgegeven of moet  `null` zijn.
- **Toepassing-plus-gebruiker** (ook wel samengestelde identiteit _genoemd):_ de gebruiker is vereist  voor toegang tot de sleutelkluis vanuit een specifieke toepassing en de toepassing moet de OBO-stroom (on-behalf-of authentication) gebruiken om de gebruiker te imiteren. Dit scenario werkt alleen als zowel `applicationId` als moeten worden opgegeven in het `objectId` toegangsbeleid. De `applicationId` identificeert de vereiste toepassing en de `objectId` identificeert de gebruiker. Deze optie is momenteel niet beschikbaar voor Azure RBAC voor gegevensvlak.

In alle soorten toegang wordt de toepassing geverifieerd met Azure AD. De toepassing maakt gebruik van [een ondersteunde verificatiemethode](../../active-directory/develop/authentication-vs-authorization.md) op basis van het toepassingstype. De toepassing verkrijgt een token voor een resource in het vlak om toegang te verlenen. De resource is een eindpunt in het beheer- of gegevensvlak, op basis van de Azure-omgeving. De toepassing gebruikt het token en verzendt een REST API-aanvraag naar Key Vault. Bekijk de volledige verificatiestroom [voor meer informatie.](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

Zie basisprincipes van [Key Vault verificatie voor meer informatie](authentication-fundamentals.md)

## <a name="key-vault-authentication-options"></a>Key Vault verificatieopties

Wanneer u een sleutelkluis maakt in een Azure-abonnement, wordt deze automatisch gekoppeld aan de Azure AD-tenant van het abonnement. Alle aanroepers op beide vlakken moeten zich registreren in deze tenant en verifiëren voor toegang tot de sleutelkluis. In beide gevallen hebben toepassingen toegang tot Key Vault op drie manieren:

- **Alleen toepassing:** de toepassing vertegenwoordigt een service-principal of beheerde identiteit. Deze identiteit is het meest voorkomende scenario voor toepassingen die periodiek toegang moeten hebben tot certificaten, sleutels of geheimen uit de sleutelkluis. Dit scenario werkt alleen als de van de toepassing moet worden opgegeven in het toegangsbeleid en de niet moet `objectId` worden opgegeven of moet `applicationId`  `null` zijn.
- **Alleen gebruiker:** de gebruiker heeft toegang tot de sleutelkluis vanuit elke toepassing die is geregistreerd in de tenant. Voorbeelden van dit type toegang zijn Azure PowerShell en de Azure Portal. Dit scenario werkt alleen als de van de gebruiker is opgegeven in het toegangsbeleid en de niet moet `objectId` worden opgegeven of moet `applicationId`  `null` zijn.
- **Toepassing-plus-gebruiker** (ook wel samengestelde identiteit _genoemd):_ de gebruiker is vereist  voor toegang tot de sleutelkluis vanuit een specifieke toepassing en de toepassing moet de OBO-stroom (on-behalf-of-authentication) gebruiken om de gebruiker te imiteren. Dit scenario werkt alleen als zowel `applicationId` als moeten worden opgegeven in het `objectId` toegangsbeleid. De `applicationId` identificeert de vereiste toepassing en de `objectId` identificeert de gebruiker. Deze optie is momenteel niet beschikbaar voor Azure RBAC voor gegevensvlak (preview).

In alle soorten toegang wordt de toepassing geverifieerd met Azure AD. De toepassing maakt gebruik van [elke ondersteunde verificatiemethode](../../active-directory/develop/authentication-vs-authorization.md) op basis van het toepassingstype. De toepassing verkrijgt een token voor een resource in het vlak om toegang te verlenen. De resource is een eindpunt in het beheer- of gegevensvlak, op basis van de Azure-omgeving. De toepassing gebruikt het token en verzendt een REST API-aanvraag naar Key Vault. Bekijk de volledige verificatiestroom [voor meer informatie.](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

Het model van één mechanisme voor verificatie voor beide vlakken heeft verschillende voordelen:

- Organisaties kunnen de toegang tot alle sleutelkluizen in hun organisatie centraal controleren.
- Als een gebruiker weggaat, heeft deze onmiddellijk geen toegang meer tot alle sleutelkluizen in de organisatie.
- Organisaties kunnen verificatie aanpassen met behulp van de opties in Azure AD, zoals meervoudige verificatie inschakelen voor extra beveiliging.

## <a name="access-model-overview"></a>Overzicht van toegangsmodel

Toegang tot een sleutelkluis wordt beheerd via twee interfaces: het **beheervlak** en **het gegevensvlak**. In het beheervlak beheert u de Key Vault zelf. Bewerkingen in dit vlak omvatten het maken en verwijderen van sleutelkluizen, het ophalen Key Vault eigenschappen en het bijwerken van toegangsbeleid. In het gegevensvlak werkt u met de gegevens die zijn opgeslagen in een sleutelkluis. U kunt sleutels, geheimen en certificaten toevoegen, verwijderen en wijzigen.

Beide vlakken gebruiken [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) voor verificatie. Voor autorisatie maakt het beheervlak gebruik van op rollen gebaseerd toegangsbeheer [van Azure (Azure RBAC)](../../role-based-access-control/overview.md) en gebruikt het gegevensvlak [een Key Vault-toegangsbeleid](./assign-access-policy-portal.md) en Azure RBAC voor Key Vault [gegevensvlakbewerkingen.](./rbac-guide.md)

Voor toegang tot een sleutelkluis in beide vlak moeten alle aanroepers (gebruikers of toepassingen) de juiste verificatie en autorisatie hebben. Met verificatie wordt de identiteit van de aanroeper vastgesteld. Autorisatie bepaalt welke bewerkingen de aanroeper kan uitvoeren. Verificatie met Key Vault werkt in combinatie met [Azure AD (Active Directory)](../../active-directory/fundamentals/active-directory-whatis.md), dat verantwoordelijk is voor het verifiëren van de identiteit van elke opgegeven **beveiligingsprincipal**.

Een beveiligingsprincipal is een object dat een gebruiker, groep, service of toepassing vertegenwoordigt die toegang tot Azure-resources aanvraagt. Azure wijst een unieke **object-id** toe aan elke beveiligingsprincipal.

- Een beveiligingsprincipal voor een **gebruiker** identificeert een persoon met een profiel in Azure Active Directory.
- Een beveiligingsprincipal voor een **groep** identificeert een set gebruikers die zijn gemaakt in Azure Active Directory. Elke rol of machtiging die wordt toegewezen aan de groep, geldt voor alle gebruikers in de groep.
- Een **service-principal** is een type beveiligingsprincipaal waarmee een toepassing of service wordt geïdentificeerd, dat wil zeggen, een stukje code in plaats van een gebruiker of groep. De object-id van een service-principal wordt aangeduid als de **client-id** en fungeert als de bijbehorende gebruikersnaam. Het clientgeheim of **het certificaat van** de service-principal **fungeert** als het wachtwoord. Veel Azure-services bieden ondersteuning voor het [toewijzen van beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md) met geautomatiseerd beheer **van client-id en** **certificaat**. Beheerde identiteit is de veiligste en aanbevolen optie voor de authenticatie in Azure.

Zie Verifiëren bij Key Vault voor meer informatie [over verificatie Azure Key Vault](authentication.md)

## <a name="privileged-access"></a>Bevoegde toegang

Autorisatie bepaalt welke bewerkingen de aanroeper kan uitvoeren. Autorisatie in Key Vault maakt gebruik van een combinatie van op rollen gebaseerd toegangsbeheer [van Azure (Azure RBAC)](../../role-based-access-control/overview.md) en Azure Key Vault toegangsbeleid.

Toegang tot kluizen vindt plaats via twee interfaces of vlakken. Deze vlakken zijn het beheervlak en het gegevensvlak.

- In *het beheervlak* beheert u de Key Vault zelf en dit is de interface die wordt gebruikt om kluizen te maken en te verwijderen. U kunt ook eigenschappen van de sleutelkluis lezen en toegangsbeleid beheren.
- Met *de gegevensvlak* kunt u werken met de gegevens die zijn opgeslagen in een sleutelkluis. U kunt sleutels, geheimen en certificaten toevoegen, verwijderen en wijzigen.

Toepassingen hebben toegang tot de vlakken via eindpunten. De toegangsregelaars voor de twee vlakken werken onafhankelijk van elkaar. Als u een toepassing toegang wilt verlenen tot het gebruik van sleutels in een sleutelkluis, verleent u toegang tot de gegevensvlak met behulp van Key Vault toegangsbeleid of Azure RBAC. Als u een gebruiker leestoegang wilt verlenen tot Key Vault eigenschappen en tags, maar niet tot gegevens (sleutels, geheimen of certificaten), verleent u toegang tot de beheervlak met Azure RBAC.

In de volgende tabel ziet u de eindpunten voor de beheer- en gegevensvlakken.

| &nbsp;Toegangsvlak | Eindpunten voor toegang | Operations | &nbsp;Toegangsbeheermechanisme |
| --- | --- | --- | --- |
| Beheerlaag | **Wereldwijd:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure van de Amerikaanse overheid:**<br> management.usgovcloudapi.net:443<br><br> **Azure Duitsland:**<br> management.microsoftazure.de:443 | Sleutelkluizen maken, lezen, bijwerken en verwijderen<br><br>Toegangsbeleid Key Vault instellen<br><br>Tags Key Vault instellen | Azure RBAC |
| Gegevenslaag | **Wereldwijd:**<br> &lt;kluisnaam&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;kluisnaam&gt;.vault.azure.cn:443<br><br> **Azure van de Amerikaanse overheid:**<br> &lt;kluisnaam&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Duitsland:**<br> &lt;kluisnaam&gt;.vault.microsoftazure.de:443 | Sleutels: encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, recover, backup, restore, purge<br><br> Certificaten: managecontacts, getissuers, listissuers, setissuers, deleteissuers, manageissuers, get, list, create, import, update, delete, recover, backup, restore, purge<br><br>  Geheimen: get, list, set, delete,recover, backup, restore, purge | Key Vault toegangsbeleid of Azure RBAC |

### <a name="managing-administrative-access-to-key-vault"></a>Beheerderstoegang tot Key Vault

Wanneer u een sleutelkluis in een resourcegroep maakt, beheert u de toegang met behulp van Azure AD. U verleent gebruikers of groepen de mogelijkheid om de sleutelkluizen in een resourcegroep te beheren. U kunt toegang verlenen op een specifiek bereikniveau door de juiste Azure-rollen toe te wijzen. Als u een gebruiker toegang wilt verlenen om sleutelkluizen te beheren, wijst u een vooraf gedefinieerde rol toe aan de gebruiker `key vault Contributor` voor een specifiek bereik. De volgende bereiken kunnen worden toegewezen aan een Azure-rol:

- **Abonnement:** een Azure-rol die is toegewezen op abonnementsniveau, is van toepassing op alle resourcegroepen en resources binnen dat abonnement.
- **Resourcegroep:** een Azure-rol die is toegewezen op het niveau van de resourcegroep, is van toepassing op alle resources in die resourcegroep.
- **Specifieke resource:** een Azure-rol die is toegewezen voor een specifieke resource, is van toepassing op die resource. In dit geval is de resource een specifieke sleutelkluis.

Er zijn verschillende vooraf gedefinieerde rollen. Als een vooraf gedefinieerde rol niet aan uw behoeften past, kunt u uw eigen rol definiëren. Zie Azure [RBAC: Ingebouwde rollen voor meer informatie.](../../role-based-access-control/built-in-roles.md)

> [!IMPORTANT]
> Als een gebruiker machtigingen heeft voor een key vault-beheervlak, kan de gebruiker zichzelf toegang verlenen tot de gegevensvlak door een Key Vault `Contributor` in te stellen. U moet goed bepalen wie `Contributor` roltoegang heeft tot uw sleutelkluizen. Zorg ervoor dat alleen gemachtigde personen uw sleutelkluizen, sleutels, geheimen en certificaten kunnen openen en beheren.

### <a name="controlling-access-to-key-vault-data"></a>Toegang tot Key Vault beheren

Key Vault toegangsbeleid verleent machtigingen afzonderlijk aan sleutels, geheimen of certificaten. U kunt een gebruiker alleen toegang verlenen tot sleutels en niet tot geheimen. Toegangsmachtigingen voor sleutels, geheimen en certificaten worden beheerd op kluisniveau.

> [!IMPORTANT]
> Key Vault bieden geen ondersteuning voor gedetailleerde machtigingen op objectniveau, zoals een specifieke sleutel, geheim of certificaat. Wanneer een gebruiker toestemming krijgt om sleutels te maken en te verwijderen, kan deze deze bewerkingen uitvoeren op alle sleutels in die sleutelkluis.

U kunt toegangsbeleid instellen voor een sleutelkluis met [de Azure Portal,](assign-access-policy-portal.md) [de Azure CLI,](assign-access-policy-cli.md) [Azure PowerShell](assign-access-policy-powershell.md)of de Key Vault Management [REST API's.](/rest/api/keyvault/)

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

Key Vault logboekregistratie slaat informatie op over de activiteiten die worden uitgevoerd op uw kluis. Zie logboekregistratie voor Key Vault [meer informatie.](logging.md)

U kunt de Key Vault integreren met Event Grid om een melding te ontvangen wanneer de status van een sleutel, certificaat of geheim dat is opgeslagen in de sleutelkluis is gewijzigd. Zie Monitoring [Key Vault with Azure Event Grid (Bewaking van Key Vault met Azure Event Grid](event-grid-overview.md)

Het is ook belangrijk om de status van uw sleutelkluis te bewaken, om ervoor te zorgen dat uw service werkt zoals bedoeld. Zie Monitoring [and alerting for](alert.md)Azure Key Vault voor meer informatie Azure Key Vault.

## <a name="backup-and-recovery"></a>Back-ups maken en herstellen

Azure Key Vault u de beveiliging voor het verwijderen en opsluizen van gegevens kunt u verwijderde kluizen en kluisobjecten herstellen. Zie overzicht van Azure Key Vault [voor soft-delete voor meer informatie.](soft-delete-overview.md)

U moet ook regelmatig back-ups van uw kluis maken bij het bijwerken/verwijderen/maken van objecten in een kluis.  

## <a name="next-steps"></a>Volgende stappen

- [Azure Key Vault beveiligingsbasislijn](security-baseline.md)
- [Best practices voor Azure Key Vault](security-baseline.md)
- [Service-eindpunten voor virtuele netwerken voor Azure Key Vault](overview-vnet-service-endpoints.md)
- [Azure RBAC: Ingebouwde rollen](../../role-based-access-control/built-in-roles.md)