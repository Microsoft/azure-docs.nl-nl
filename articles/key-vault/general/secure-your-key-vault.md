---
title: Veilige toegang tot een sleutelkluis
description: Het toegangsmodel voor Azure Key Vault, waaronder Active Directory-verificatie en resource-eindpunten.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: sudbalas
ms.openlocfilehash: b1ec89db7bc12ffbd21c6e302e065449eba3ac20
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366484"
---
# <a name="secure-access-to-a-key-vault"></a>Veilige toegang tot een sleutelkluis

Azure Key Vault is een cloudservice die versleutelingssleutels en geheimen, zoals certificaten, verbindingsreeksen en wachtwoorden, beveiligt. Omdat deze gegevens gevoelig en bedrijfskritiek zijn, moet u de toegang tot uw sleutelkluizen beveiligen door alleen geautoriseerde toepassingen en gebruikers toe te staan. In dit artikel vindt u een overzicht van Key Vault toegangsmodel. Hier wordt verificatie en autorisatie uitgelegd en wordt beschreven hoe u de toegang tot uw sleutelkluizen beveiligt.

Zie [Over Azure Key Vault](overview.md) voor meer informatie over Key Vault. Zie [Sleutels, geheimen en certificaten](about-keys-secrets-certificates.md) voor meer informatie over wat er kan worden opgeslagen in een sleutelkluis.

## <a name="access-model-overview"></a>Overzicht van toegangsmodel

Toegang tot een sleutelkluis wordt beheerd via twee interfaces: het **beheervlak** en **het gegevensvlak**. In het beheervlak beheert u de Key Vault zelf. Bewerkingen in dit vlak omvatten het maken en verwijderen van sleutelkluizen, het ophalen Key Vault eigenschappen en het bijwerken van toegangsbeleid. In het gegevensvlak werkt u met de gegevens die zijn opgeslagen in een sleutelkluis. U kunt sleutels, geheimen en certificaten toevoegen, verwijderen en wijzigen.

Beide vlakken gebruiken [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) voor verificatie. Voor autorisatie maakt het beheervlak gebruik van op rollen gebaseerd toegangsbeheer [van Azure (Azure RBAC)](../../role-based-access-control/overview.md) en gebruikt het gegevensvlak een [Key Vault-toegangsbeleid](./assign-access-policy-portal.md) en Azure RBAC voor het uitvoeren van Key Vault [gegevensvlakbewerkingen.](./rbac-guide.md)

Voor toegang tot een sleutelkluis in beide vlak moeten alle aanroepers (gebruikers of toepassingen) de juiste verificatie en autorisatie hebben. Met verificatie wordt de identiteit van de aanroeper vastgesteld. Autorisatie bepaalt welke bewerkingen de aanroeper kan uitvoeren. Verificatie met Key Vault werkt in combinatie met [Azure AD (Active Directory)](../../active-directory/fundamentals/active-directory-whatis.md), dat verantwoordelijk is voor het verifiëren van de identiteit van elke opgegeven **beveiligingsprincipal**.

Een beveiligingsprincipal is een object dat een gebruiker, groep, service of toepassing vertegenwoordigt die toegang tot Azure-resources aanvraagt. Azure wijst een unieke **object-id** toe aan elke beveiligingsprincipal.

* Een beveiligingsprincipal voor een **gebruiker** identificeert een persoon met een profiel in Azure Active Directory.

* Een beveiligingsprincipal voor een **groep** identificeert een set gebruikers die zijn gemaakt in Azure Active Directory. Elke rol of machtiging die wordt toegewezen aan de groep, geldt voor alle gebruikers in de groep.

* Een **service-principal** is een type beveiligingsprincipaal waarmee een toepassing of service wordt geïdentificeerd, dat wil zeggen een stukje code in plaats van een gebruiker of groep. De object-id van een service-principal wordt aangeduid als de **client-id** en fungeert als de bijbehorende gebruikersnaam. Het clientgeheim of certificaat van de **service-principal** **fungeert** als het wachtwoord. Veel Azure-services bieden ondersteuning voor het [toewijzen van beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md) met geautomatiseerd beheer van **client-id en** **certificaat**. Beheerde identiteit is de veiligste en aanbevolen optie voor de authenticatie in Azure.

Zie Verifiëren bij Key Vault voor meer informatie over [verificatie Azure Key Vault](authentication.md)

## <a name="key-vault-authentication-options"></a>Key Vault verificatieopties

Wanneer u een sleutelkluis maakt in een Azure-abonnement, wordt deze automatisch gekoppeld aan de Azure AD-tenant van het abonnement. Alle aanroepers op beide vlakken moeten zich registreren in deze tenant en verifiëren voor toegang tot de sleutelkluis. In beide gevallen hebben toepassingen toegang tot Key Vault op drie manieren:

- **Alleen toepassing:** de toepassing vertegenwoordigt een service-principal of beheerde identiteit. Deze identiteit is het meest voorkomende scenario voor toepassingen die periodiek toegang moeten hebben tot certificaten, sleutels of geheimen uit de sleutelkluis. Dit scenario werkt alleen als de van de toepassing is opgegeven in het toegangsbeleid en de niet moet `objectId` worden opgegeven of moet `applicationId`  `null` zijn.
- **Alleen gebruiker:** de gebruiker heeft toegang tot de sleutelkluis vanuit elke toepassing die is geregistreerd in de tenant. Voorbeelden van dit type toegang zijn Azure PowerShell en de Azure Portal. Dit scenario werkt alleen als de van de gebruiker is opgegeven in het toegangsbeleid en de niet moet `objectId` worden opgegeven of moet `applicationId`  `null` zijn.
- **Toepassing-plus-gebruiker** (ook wel samengestelde identiteit _genoemd):_ de gebruiker is vereist  voor toegang tot de sleutelkluis vanuit een specifieke toepassing en de toepassing moet de OBO-stroom (on-behalf-of-authentication) gebruiken om de gebruiker te imiteren. Dit scenario werkt alleen als zowel `applicationId` als moeten worden opgegeven in het `objectId` toegangsbeleid. De `applicationId` identificeert de vereiste toepassing en de `objectId` identificeert de gebruiker. Deze optie is momenteel niet beschikbaar voor Azure RBAC voor gegevensvlak.

In alle soorten toegang wordt de toepassing geverifieerd met Azure AD. De toepassing maakt gebruik van [een ondersteunde verificatiemethode](../../active-directory/develop/authentication-vs-authorization.md) op basis van het toepassingstype. De toepassing verkrijgt een token voor een resource in het vlak om toegang te verlenen. De resource is een eindpunt in het beheer- of gegevensvlak, op basis van de Azure-omgeving. De toepassing gebruikt het token en verzendt een REST API-aanvraag naar Key Vault. Bekijk de volledige verificatiestroom [voor meer informatie.](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

Het model van één verificatiemechanisme voor beide vlakken heeft verschillende voordelen:

- Organisaties kunnen de toegang tot alle sleutelkluizen in hun organisatie centraal controleren.
- Als een gebruiker weggaat, heeft deze onmiddellijk geen toegang meer tot alle sleutelkluizen in de organisatie.
- Organisaties kunnen verificatie aanpassen met behulp van de opties in Azure AD, zoals meervoudige verificatie inschakelen voor extra beveiliging.

## <a name="resource-endpoints"></a>Resource-eindpunten

Toepassingen hebben toegang tot de vlakken via eindpunten. De toegangsregelaars voor de twee vlakken werken onafhankelijk van elkaar. Als u een toepassing toegang wilt verlenen tot het gebruik van sleutels in een sleutelkluis, verleent u gegevensvlaktoegang met behulp van Key Vault toegangsbeleid of Azure RBAC. Als u een gebruiker leestoegang wilt verlenen tot Key Vault eigenschappen en tags, maar niet tot gegevens (sleutels, geheimen of certificaten), verleent u toegang tot de beheervlak met Azure RBAC.

In de volgende tabel ziet u de eindpunten voor de beheer- en gegevensvlakken.

| &nbsp;Toegangsvlak | Eindpunten voor toegang | Operations | Mechanisme &nbsp; voor toegangsbeheer |
| --- | --- | --- | --- |
| Beheerlaag | **Wereldwijd:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure van de Amerikaanse overheid:**<br> management.usgovcloudapi.net:443<br><br> **Azure Duitsland:**<br> management.microsoftazure.de:443 | Sleutelkluizen maken, lezen, bijwerken en verwijderen<br><br>Toegangsbeleid Key Vault instellen<br><br>Tags Key Vault instellen | Azure RBAC |
| Gegevenslaag | **Wereldwijd:**<br> &lt;kluisnaam&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;kluisnaam&gt;.vault.azure.cn:443<br><br> **Azure van de Amerikaanse overheid:**<br> &lt;kluisnaam&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Duitsland:**<br> &lt;kluisnaam&gt;.vault.microsoftazure.de:443 | Sleutels: encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, recover, backup, restore, purge<br><br> Certificaten: managecontacts, getissuers, listissuers, setissuers, deleteissuers, manageissuers, get, list, create, import, update, delete, recover, backup, restore, purge<br><br>  Geheimen: get, list, set, delete,recover, backup, restore, purge | Key Vault toegangsbeleid of Azure RBAC |

## <a name="management-plane-and-azure-rbac"></a>Beheervlak en Azure RBAC

In het beheervlak gebruikt u op rollen gebaseerd toegangsbeheer [van Azure (Azure RBAC)](../../role-based-access-control/overview.md) om de bewerkingen te autoreren die een aanroeper kan uitvoeren. In het Azure RBAC-model heeft elk Azure-abonnement een exemplaar van Azure AD. U verleent vanuit deze directory toegang tot gebruikers, groepen en toepassingen. Toegang wordt verleend voor het beheren van resources in het Azure-abonnement die gebruikmaken van Azure Resource Manager implementatiemodel.

U maakt een sleutelkluis in een resourcegroep en beheert de toegang met behulp van Azure AD. U verleent gebruikers of groepen de mogelijkheid om de sleutelkluizen in een resourcegroep te beheren. U verleent toegang op een specifiek bereikniveau door de juiste Azure-rollen toe te wijzen. Als u een gebruiker toegang wilt verlenen om sleutelkluizen te beheren, wijst u een vooraf gedefinieerde [rol Key Vault](../../role-based-access-control/built-in-roles.md#key-vault-contributor) Inzender toe aan de gebruiker voor een specifiek bereik. De volgende bereiken kunnen worden toegewezen aan een Azure-rol:

- **Abonnement:** een Azure-rol die is toegewezen op abonnementsniveau, is van toepassing op alle resourcegroepen en resources binnen dat abonnement.
- **Resourcegroep:** een Azure-rol die is toegewezen op het niveau van de resourcegroep, is van toepassing op alle resources in die resourcegroep.
- **Specifieke resource:** een Azure-rol die is toegewezen voor een specifieke resource, is van toepassing op die resource. In dit geval is de resource een specifieke sleutelkluis.

Er zijn verschillende vooraf gedefinieerde rollen. Als een vooraf gedefinieerde rol niet aan uw behoeften past, kunt u uw eigen rol definiëren. Zie [Ingebouwde rollen in Azure](../../role-based-access-control/built-in-roles.md) voor meer informatie. 

U moet machtigingen `Microsoft.Authorization/roleAssignments/write` voor `Microsoft.Authorization/roleAssignments/delete` en hebben, zoals Beheerder voor [gebruikerstoegang](../../role-based-access-control/built-in-roles.md#user-access-administrator) of [Eigenaar](../../role-based-access-control/built-in-roles.md#owner)

> [!IMPORTANT]
> Als een gebruiker machtigingen heeft voor een key vault-beheervlak, kan de gebruiker zichzelf toegang verlenen tot de gegevensvlak door een Key Vault `Contributor` in te stellen. U moet goed bepalen wie `Contributor` roltoegang heeft tot uw sleutelkluizen. Zorg ervoor dat alleen gemachtigde personen uw sleutelkluizen, sleutels, geheimen en certificaten kunnen openen en beheren.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Beleid voor gegevensvlak en toegang

U kunt gegevensvlaktoegang verlenen door Key Vault toegangsbeleid voor een sleutelkluis in te stellen. Als u dit toegangsbeleid wilt instellen, moet een gebruiker, groep of toepassing machtigingen hebben voor het `Key Vault Contributor` beheervlak voor die sleutelkluis.

U verleent een gebruiker, groep of toepassing toegang om specifieke bewerkingen uit te voeren voor sleutels of geheimen in een sleutelkluis. Key Vault ondersteunt maximaal 1024 vermeldingen van toegangsbeleid voor een sleutelkluis. Als u meerdere gebruikers toegang wilt verlenen tot de gegevensvlak, maakt u een Azure AD-beveiligingsgroep en voegt u gebruikers toe aan die groep.

U kunt hier de volledige lijst met kluis- en geheime bewerkingen bekijken: [Key Vault Bewerkingsverwijzing](/rest/api/keyvault/#vault-operations)

<a id="key-vault-access-policies"></a> Key Vault toegangsbeleid verleent machtigingen afzonderlijk aan sleutels, geheimen en certificaten.  Toegangsmachtigingen voor sleutels, geheimen en certificaten zijn op kluisniveau. 

Zie Assign a Key Vault access policy (Toegangsbeleid voor een [sleutelkluis toewijzen) Key Vault meer](assign-access-policy-portal.md) informatie over het gebruik van toegangsbeleid voor key vault

> [!IMPORTANT]
> Key Vault toegangsbeleid van toepassing op kluisniveau. Wanneer een gebruiker toestemming krijgt om sleutels te maken en te verwijderen, kan deze deze bewerkingen uitvoeren op alle sleutels in die sleutelkluis.
Key Vault bieden geen ondersteuning voor gedetailleerde machtigingen op objectniveau, zoals een specifieke sleutel, geheim of certificaat. 
>

## <a name="data-plane-and-azure-rbac"></a>Gegevensvlak en Azure RBAC

Op rollen gebaseerd toegangsbeheer van Azure is een alternatief machtigingsmodel voor het beheer van de toegang tot Azure Key Vault gegevensvlak, dat kan worden ingeschakeld op afzonderlijke sleutelkluizen. Het Azure RBAC-machtigingsmodel is exclusief en zodra deze is ingesteld, is het toegangsbeleid voor de kluis inactief geworden. Azure Key Vault definieert een set ingebouwde Azure-rollen die algemene sets machtigingen omvatten die worden gebruikt voor toegang tot sleutels, geheimen of certificaten.

Wanneer een Azure-rol wordt toegewezen aan een Azure AD-beveiligingsprincipaal, verleent Azure toegang tot deze resources voor die beveiligingsprincipaal. Toegang kan worden beperkt tot het niveau van het abonnement, de resourcegroep, de sleutelkluis of een afzonderlijke sleutel, geheim of certificaat. Een Azure AD-beveiligingsprincipaal kan een gebruiker, een groep, een toepassingsservice-principal of een beheerde identiteit voor [Azure-resources zijn.](../../active-directory/managed-identities-azure-resources/overview.md)

De belangrijkste voordelen van het gebruik van Azure RBAC-machtigingen ten opzichte van toegangsbeleid voor kluizen zijn gecentraliseerd toegangsbeheer en [de integratie ervan met Privileged Identity Management (PIM).](../../active-directory/privileged-identity-management/pim-configure.md) Privileged Identity Management biedt op tijd en goedkeuring gebaseerde rolactiveringen om de risico's van buitensporige, onnodige of verkeerd gebruikte toegangsmachtigingen te beperken voor resources die u belangrijk vindt.

Zie Key Vault-sleutels, certificaten en geheimen met op rollen gebaseerd toegangsbeheer van Azure voor meer informatie over de [Key Vault-gegevensvlak met Azure](rbac-guide.md) RBAC

## <a name="firewalls-and-virtual-networks"></a>Firewalls en virtuele netwerken

Voor een extra beveiligingslaag kunt u firewalls en regels voor virtuele netwerken configureren. U kunt standaard Key Vault firewalls en virtuele netwerken configureren om toegang tot verkeer van alle netwerken (inclusief internetverkeer) te weigeren. U kunt toegang verlenen tot verkeer van specifieke virtuele [Azure-netwerken](../../virtual-network/virtual-networks-overview.md) en ip-adresbereiken van openbare internet, zodat u een beveiligde netwerkgrens voor uw toepassingen kunt bouwen.

Hier zijn enkele voorbeelden van hoe u service-eindpunten kunt gebruiken:

* U gebruikt Key Vault om versleutelingssleutels, toepassingsgeheimen en certificaten op te slaan en u wilt de toegang tot uw sleutelkluis blokkeren via het openbare internet.
* U wilt de toegang tot uw sleutelkluis vergrendelen zodat alleen uw toepassing, of een korte lijst met aangewezen hosts, verbinding kan maken met uw sleutelkluis.
* Er wordt een toepassing uitgevoerd in uw virtuele Azure-netwerk en dit virtuele netwerk is vergrendeld voor al het binnenkomende en uitgaande verkeer. Uw toepassing moet nog steeds verbinding maken met Key Vault om geheimen of certificaten op te halen of cryptografische sleutels te gebruiken.

Zie Configure Azure Key Vault firewalls and virtual networks (Firewalls en virtuele netwerken configureren) voor Key Vault [firewalls en virtuele netwerken](network-security.md)

> [!NOTE]
> Key Vault firewalls en regels voor virtuele netwerken zijn alleen van toepassing op het gegevensvlak van Key Vault. Key Vault bewerkingen op de besturingsvlak (zoals bewerkingen maken, verwijderen en wijzigen, toegangsbeleid instellen, firewalls instellen en regels voor virtuele netwerken) worden niet beïnvloed door firewalls en regels voor virtuele netwerken.

## <a name="private-endpoint-connection"></a>Verbinding met privé-eindpunt

In het geval van een noodzaak om de blootstelling Key Vault openbaar te blokkeren, kan een [privé-eindpunt](../../private-link/private-endpoint-overview.md) van Azure worden gebruikt. Een privé-eindpunt in Azure is een netwerkinterface waarmee u privé en veilig verbinding maakt met een service die door Azure Private Link mogelijk wordt gemaakt. Het privé-eindpunt maakt gebruik van een privé-IP-adres van uw VNet, waardoor de service feitelijk in uw VNet wordt geplaatst. Al het verkeer naar de service kan worden gerouteerd via het privé-eindpunt, zodat er geen gateways, NAT-apparaten, ExpressRoute of VPN-verbindingen of openbare IP-adressen nodig zijn. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U kunt verbinding maken met een exemplaar van een Azure-resource, zodat u het hoogste granulariteit krijgt in toegangsbeheer.

Algemene scenario's voor het gebruik Private Link voor Azure-services:

- **Privétoegang tot services op het Azure-platform**: Verbind uw virtuele netwerk met services in Azure zonder een openbaar IP-adres bij de bron of bestemming. Serviceproviders kunnen hun services weergeven in hun eigen virtuele netwerk en consumenten hebben toegang tot deze services in hun lokale virtuele netwerk. Het Private Link-platform zorgt voor de connectiviteit tussen de consument en de services via het Azure-backbonenetwerk. 
 
- **On-premises en peered netwerken**: Toegang tot services die worden uitgevoerd in Azure vanuit on-premises via ExpressRoute-privépeering, VPN-tunnels en gekoppelde virtuele netwerken met behulp van privé-eindpunten. U hoeft geen openbare peering in te stellen of via internet te gaan om de service te bereiken. Private Link biedt een veilige manier om workloads naar Azure te migreren.
 
- **Bescherming tegen gegevenslekken**: Een privé-eindpunt wordt toegewezen aan een instantie van een PaaS-resource in plaats van de gehele service. Consumenten kunnen alleen verbinding maken met de specifieke resource. Toegang tot andere resources in de service is geblokkeerd. Dit mechanisme biedt beveiliging tegen risico's van gegevenslekken. 
 
- **Globaal bereik**: Maak privé verbinding met services die in andere regio's worden uitgevoerd. Het virtuele netwerk van de consument kan zich in regio A bevinden en kan verbinding maken met services achter een Private Link in regio B.  
 
- **Breid uit naar uw eigen services**: Schakel dezelfde ervaring en functionaliteit in om uw service privé te maken voor consumenten in Azure. Als u uw service achter een standaard Azure Load Balancer plaatst, kunt u deze inschakelen voor Private Link. De gebruiker kan vervolgens rechtstreeks verbinding maken met uw service met behulp van een privé-eindpunt in zijn eigen virtuele netwerk. U kunt de verbindingsaanvragen beheren met een goedkeuringsaanroepstroom. Azure Private Link werkt voor consumenten en services die deel uitmaken van verschillende Azure Active Directory-tenants. 

Zie Voor meer informatie over privé-eindpunten [Key Vault met Azure Private Link](./private-link-service.md)

## <a name="example"></a>Voorbeeld

In dit voorbeeld ontwikkelen we een toepassing die gebruikmaakt van een certificaat voor TLS/SSL, Azure Storage voor het opslaan van gegevens en een RSA 2048-bits sleutel voor het versleutelen van gegevens in Azure Storage. Onze toepassing wordt uitgevoerd in een virtuele Azure-machine (VM) (of een virtuele-machineschaalset). We kunnen een sleutelkluis gebruiken om de toepassingsgeheimen op te slaan. We kunnen het bootstrapcertificaat opslaan dat door de toepassing wordt gebruikt voor verificatie bij Azure AD.

We hebben toegang nodig tot de volgende opgeslagen sleutels en geheimen:
- **TLS/SSL-certificaat:** wordt gebruikt voor TLS/SSL.
- **Opslagsleutel:** wordt gebruikt voor toegang tot het opslagaccount.
- **RSA 2048-bits** sleutel: wordt gebruikt voor het verpakken/uitpakken van gegevensversleutelingssleutel door Azure Storage.
- **Door toepassing beheerde identiteit:** wordt gebruikt voor verificatie met Azure AD. Nadat toegang tot Key Vault is verleend, kan de toepassing de opslagsleutel en het certificaat ophalen.

We moeten de volgende rollen definiëren om op te geven wie onze toepassing kan beheren, implementeren en controleren:
- **Beveiligingsteam**: IT-personeel van het kantoor van de CSO (Chief Security Officer) of soortgelijke medewerkers. Het beveiligingsteam is verantwoordelijk voor de juiste beveiliging van geheimen. De geheimen kunnen TLS/SSL-certificaten, RSA-sleutels voor versleuteling, verbindingsreeksen en opslagaccountsleutels bevatten.
- **Ontwikkelaars en operators**: De medewerkers die de toepassing ontwikkelen en implementeren in Azure. De leden van dit team maken geen deel uit van het beveiligingspersoneel. Ze mogen geen toegang hebben tot gevoelige gegevens, zoals TLS/SSL-certificaten en RSA-sleutels. Alleen de toepassing die ze implementeren, mag toegang hebben tot gevoelige gegevens.
- **Auditors**: Deze rol is voor medewerkers die niet behoren tot het team van ontwikkelaars of algemene IT-medewerkers. Ze controleren het gebruik en onderhoud van certificaten, sleutels en geheimen en zorgen voor de naleving van beveiligingsstandaarden.

Er is nog een rol die zich buiten het bereik van de toepassing bevindt: de beheerder van het abonnement (of de resourcegroep). De abonnementsbeheerder stelt de initiële toegangsmachtigingen voor het beveiligingsteam in. Ze verlenen toegang tot het beveiligingsteam door gebruik te maken van een resourcegroep met de benodigde resources voor de toepassing.

We moeten de volgende bewerkingen voor onze rollen autoriseren:

**Beveiligingsteam**
- Maak sleutelkluizen.
- Schakel logboekregistratie Key Vault in.
- Sleutels en geheimen toevoegen.
- Back-ups van sleutels maken voor herstel na noodherstel.
- Stel Key Vault toegangsbeleid in en wijs rollen toe om machtigingen te verlenen aan gebruikers en toepassingen voor specifieke bewerkingen.
- De sleutels en geheimen periodiek roll.

**Ontwikkelaars en operators**
- Haal verwijzingen op van het beveiligingsteam voor de bootstrap- en TLS/SSL-certificaten (vingerafdruk), opslagsleutel (geheime URI) en RSA-sleutel (sleutel-URI) voor verpakken/uitpakken.
- Ontwikkel en implementeer de toepassing om programmatisch toegang te krijgen tot certificaten en geheimen.

**Auditors**
- Bekijk de Key Vault om het juiste gebruik van sleutels en geheimen en naleving van de standaarden voor gegevensbeveiliging te bevestigen.

De volgende tabel bevat een overzicht van de toegangsmachtigingen voor onze rollen en toepassing.

| Rol | Machtigingen voor de beheerlaag | Machtigingen voor de gegevensvlak - toegangsbeleid voor de kluis | Machtigingen voor gegevensvlak - Azure RBAC  |
| --- | --- | --- | --- |
| Beveiligingsteam | [Key Vault Inzender](../../role-based-access-control/built-in-roles.md#key-vault-contributor) | Certificaten: alle bewerkingen <br> Sleutels: alle bewerkingen <br> Geheimen: alle bewerkingen | [Key Vault Administrator](../../role-based-access-control/built-in-roles.md#key-vault-administrator) |
| Ontwikkelaars en &nbsp; operators | Key Vault implementeren<br><br> **Opmerking:** met deze machtiging kunnen geïmplementeerde VM's geheimen ophalen uit een sleutelkluis. | Geen | Geen |
| Auditors | Geen | Certificaten: lijst <br> Sleutels: weergeven<br>Geheimen: weergeven<br><br> **Opmerking:** met deze machtiging kunnen auditors kenmerken (tags, activeringsdatums, vervaldatums) controleren voor sleutels en geheimen die niet in de logboeken worden ingediend. | [Key Vault Lezer](../../role-based-access-control/built-in-roles.md#key-vault-reader) |
| Azure Storage-account | Geen | Sleutels: get, list, wrapKey, unwrapKey <br> | [Key Vault cryptoserviceversleutelingsgebruiker](../../role-based-access-control/built-in-roles.md#key-vault-crypto-service-encryption-user) |
| Toepassing | Geen | Geheimen: get, list <br> Certificaten: get, list | [Key Vault Reader,](../../role-based-access-control/built-in-roles.md#key-vault-reader) [Key Vault Secret User](../../role-based-access-control/built-in-roles.md#key-vault-secrets-user) |

De drie teamrollen hebben toegang tot andere resources nodig, samen met Key Vault machtigingen. Voor het implementeren van VM's (of de Web Apps-functie van Azure App Service), moeten ontwikkelaars en operators toegang implementeren. Auditors hebben leestoegang nodig tot het opslagaccount waar de Key Vault logboeken worden opgeslagen.

In ons voorbeeld wordt een eenvoudig scenario beschreven. In de praktijk kunnen scenario's complexer zijn. U kunt machtigingen voor uw sleutelkluis aanpassen op basis van uw behoeften. We gaan ervan uit dat het beveiligingsteam de sleutel- en geheimreferenties (URI's en vingerafdrukken) aanlevert die worden gebruikt door het DevOps-personeel in hun toepassingen. Ontwikkelaars en operators hebben geen toegang tot een gegevensvlak nodig. We richten ons op de beveiliging van uw sleutelkluis.

> [!NOTE]
> In dit voorbeeld ziet u Key Vault toegang wordt vergrendeld in productie. Ontwikkelaars moeten hun eigen abonnement of resourcegroep hebben met volledige machtigingen voor het beheren van hun kluizen, VM's en het opslagaccount waarin ze de toepassing ontwikkelen.

## <a name="resources"></a>Resources

- [Over Azure Key Vault](overview.md)
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
- [Azure RBAC](../../role-based-access-control/overview.md)
- [Private Link](../../private-link/private-link-overview.md)

## <a name="next-steps"></a>Volgende stappen

[Verifiëren bij Azure Key Vault](authentication.md)

[Een Key Vault-toegangsbeleid toewijzen](assign-access-policy-portal.md)

[Azure-rol toewijzen voor toegang tot sleutels, geheimen en certificaten](rbac-guide.md)

[Key Vault-firewalls en virtuele netwerken configureren](network-security.md)

[Een Private Link-verbinding tot stand brengen met Key Vault](private-link-service.md)
