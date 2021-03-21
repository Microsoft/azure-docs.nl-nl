---
title: Veilige toegang tot een sleutelkluis
description: Het toegangs model voor Azure Key Vault, inclusief Active Directory-verificatie en resource-eind punten.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: sudbalas
ms.openlocfilehash: 94034edfa1a5c6ffccd022b4cbf7bae42cc0bae3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212464"
---
# <a name="secure-access-to-a-key-vault"></a>Veilige toegang tot een sleutelkluis

Azure Key Vault is een cloudservice die versleutelingssleutels en geheimen, zoals certificaten, verbindingsreeksen en wachtwoorden, beveiligt. Omdat deze gegevens gevoelig en zakelijk kritiek zijn, moet u de toegang tot uw sleutel kluizen beveiligen door alleen geautoriseerde toepassingen en gebruikers toe te staan. In dit artikel vindt u een overzicht van het toegangs model van Key Vault. Hierin worden verificatie en autorisatie uitgelegd en wordt beschreven hoe u de toegang tot uw sleutel kluizen kunt beveiligen.

Zie [Over Azure Key Vault](overview.md) voor meer informatie over Key Vault. Zie [Sleutels, geheimen en certificaten](about-keys-secrets-certificates.md) voor meer informatie over wat er kan worden opgeslagen in een sleutelkluis.

## <a name="access-model-overview"></a>Overzicht van toegangs modellen

Toegang tot een sleutel kluis wordt beheerd via twee interfaces: het **beheer vlak** en het **gegevens vlak**. Het beheer vlak is waar u Key Vault zichzelf beheert. Bewerkingen in dit vlak zijn onder andere het maken en verwijderen van sleutel kluizen, het ophalen van Key Vault eigenschappen en het bijwerken van het toegangs beleid. Op het gegevens vlak kunt u werken met de gegevens die zijn opgeslagen in een sleutel kluis. U kunt sleutels, geheimen en certificaten toevoegen, verwijderen en wijzigen.

Beide plannen gebruiken [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) voor verificatie. Voor autorisatie gebruikt het beheer vlak [Azure RBAC (op rollen gebaseerd toegangs beheer)](../../role-based-access-control/overview.md) en gebruikt het gegevens vlak een [Key Vault toegangs beleid](./assign-access-policy-portal.md) en [Azure RBAC voor Key Vault gegevensvlak bewerkingen](./rbac-guide.md).

Voor toegang tot een sleutel kluis in een van beide vlieg tuigen moeten alle bellers (gebruikers of toepassingen) de juiste verificatie en autorisatie hebben. Met verificatie wordt de identiteit van de aanroeper bepaald. Autorisatie bepaalt welke bewerkingen de aanroeper kan uitvoeren. Verificatie met Key Vault werkt in combinatie met [Azure AD (Active Directory)](../../active-directory/fundamentals/active-directory-whatis.md), dat verantwoordelijk is voor het verifiëren van de identiteit van elke opgegeven **beveiligingsprincipal**.

Een beveiligingsprincipal is een object dat een gebruiker, groep, service of toepassing vertegenwoordigt die toegang tot Azure-resources aanvraagt. Azure wijst een unieke **object-id** toe aan elke beveiligingsprincipal.

* Een beveiligingsprincipal voor een **gebruiker** identificeert een persoon met een profiel in Azure Active Directory.

* Een beveiligingsprincipal voor een **groep** identificeert een set gebruikers die zijn gemaakt in Azure Active Directory. Elke rol of machtiging die wordt toegewezen aan de groep, geldt voor alle gebruikers in de groep.

* Een **Service-Principal** is een type beveiligings-principal dat een toepassing of service aanduidt. Dit is een stukje code in plaats van een gebruiker of groep. De object-id van een service-principal wordt aangeduid als de **client-id** en fungeert als de bijbehorende gebruikersnaam. Het **client geheim** of het **certificaat** van de Service-Principal fungeert als het bijbehorende wacht woord. Veel Azure-Services bieden ondersteuning voor het toewijzen van [beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md) met geautomatiseerd beheer van de **client-id** en het **certificaat**. Beheerde identiteit is de veiligste en aanbevolen optie voor verificatie binnen Azure.

Zie [verifiëren voor Azure Key Vault](authentication.md) voor meer informatie over verificatie voor Key Vault.

## <a name="key-vault-authentication-options"></a>Verificatie opties Key Vault

Wanneer u een sleutel kluis maakt in een Azure-abonnement, wordt deze automatisch gekoppeld aan de Azure AD-Tenant van het abonnement. Alle bellers in beide abonnementen moeten zich registreren bij deze Tenant en verifiëren voor toegang tot de sleutel kluis. In beide gevallen kunnen toepassingen op drie manieren toegang krijgen tot Key Vault:

- **Alleen toepassing**: de toepassing vertegenwoordigt een service-principal of beheerde identiteit. Deze identiteit is het meest voorkomende scenario voor toepassingen die regel matig toegang moeten hebben tot certificaten, sleutels of geheimen van de sleutel kluis. Voor een goede werking van dit scenario `objectId` moet de toepassing worden opgegeven in het toegangs beleid en `applicationId` mag _niet_ worden opgegeven of moet zijn `null` .
- **Alleen gebruiker**: de gebruiker heeft toegang tot de sleutel kluis vanuit elke toepassing die is geregistreerd in de Tenant. Voor beelden van dit type toegang zijn Azure PowerShell en de Azure Portal. Dit scenario werkt alleen als de `objectId` gebruiker is opgegeven in het toegangs beleid en de `applicationId` mag _niet_ zijn opgegeven of moet zijn `null` .
- **Toepassings-plus-gebruiker** (soms aangeduid als _samengestelde identiteit_): de gebruiker is verplicht om toegang te krijgen tot de sleutel kluis van een specifieke toepassing _en_ de toepassing moet de stroom voor verificatie op naam (OBO) gebruiken om de gebruiker te imiteren. Als u dit scenario wilt gebruiken, `applicationId` moet u beide en `objectId` opgeven in het toegangs beleid. De `applicationId` identificeert de vereiste toepassing en de `objectId` identificeert de gebruiker. Deze optie is momenteel niet beschikbaar voor gegevens vlak Azure RBAC (preview).

Bij alle soorten toegang verifieert de toepassing met Azure AD. De toepassing gebruikt een [ondersteunde verificatie methode](../../active-directory/develop/authentication-vs-authorization.md) op basis van het toepassings type. De toepassing verkrijgt een token voor een resource in het vlak om toegang te verlenen. De resource is een eind punt in het beheer-of gegevens vlak, gebaseerd op de Azure-omgeving. De toepassing gebruikt het token en verzendt een REST API aanvraag naar Key Vault. Bekijk voor meer informatie de [volledige verificatie stroom](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Het model van één mechanisme voor verificatie voor beide abonnementen heeft verschillende voor delen:

- Organisaties kunnen de toegang centraal beheren voor alle sleutel kluizen in hun organisatie.
- Als een gebruiker deze verlaat, gaan ze onmiddellijk toegang tot alle sleutel kluizen in de organisatie.
- Organisaties kunnen verificatie aanpassen met behulp van de opties in azure AD, zoals om multi-factor Authentication in te scha kelen voor extra beveiliging.

## <a name="resource-endpoints"></a>Resource-eind punten

Toepassingen hebben toegang tot de abonnementen via eind punten. De toegangs controle voor de twee abonnementen werkt afzonderlijk. Als u een toepassing toegang wilt verlenen voor het gebruik van sleutels in een sleutel kluis, geeft u toegang tot het gegevens vlak met behulp van een Key Vault toegangs beleid of Azure RBAC (preview). Als u een gebruiker lees toegang wilt verlenen voor Key Vault eigenschappen en tags, maar geen toegang tot gegevens (sleutels, geheimen of certificaten), verleent u beheer vlak toegang met Azure RBAC.

De volgende tabel bevat de eind punten voor de beheer-en gegevens abonnementen.

| Toegangs &nbsp; vlak | Eindpunten voor toegang | Operations | Mechanisme voor toegangs &nbsp; beheer |
| --- | --- | --- | --- |
| Beheerlaag | **Wereldwijd:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure van de Amerikaanse overheid:**<br> management.usgovcloudapi.net:443<br><br> **Azure Duitsland:**<br> management.microsoftazure.de:443 | Sleutel kluizen maken, lezen, bijwerken en verwijderen<br><br>Key Vault toegangs beleid instellen<br><br>Key Vault Tags instellen | Azure RBAC |
| Gegevenslaag | **Wereldwijd:**<br> &lt;kluisnaam&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;kluisnaam&gt;.vault.azure.cn:443<br><br> **Azure van de Amerikaanse overheid:**<br> &lt;kluisnaam&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Duitsland:**<br> &lt;kluisnaam&gt;.vault.microsoftazure.de:443 | Sleutels: versleutelen, ontsleutelen, wrapKey, sleutel uitpakken, ondertekenen, verifiëren, ophalen, lijst maken, bijwerken, importeren, verwijderen, herstellen, back-up maken, herstellen, opschonen<br><br> Certificaten: managecontacts, getissuers, listissuers, setissuers, deleteissuers, manageissuers, Get, List, maken, importeren, bijwerken, verwijderen, herstellen, back-up maken, herstellen, opschonen<br><br>  Geheimen: ophalen, lijst, instellen, verwijderen, herstellen, back-ups maken, herstellen, opschonen | Key Vault toegangs beleid of Azure RBAC (preview-versie)|

## <a name="management-plane-and-azure-rbac"></a>Beheer vlak en Azure RBAC

In het beheer vlak gebruikt u [Azure RBAC (op rollen gebaseerd toegangs beheer)](../../role-based-access-control/overview.md) om de bewerkingen te autoriseren die een aanroeper kan uitvoeren. In het model van Azure RBAC heeft elk Azure-abonnement een exemplaar van Azure AD. U verleent toegang aan gebruikers, groepen en toepassingen vanuit deze map. Toegang is verleend om resources te beheren in het Azure-abonnement dat gebruikmaakt van het Azure Resource Manager-implementatie model.

U maakt een sleutel kluis in een resource groep en beheert de toegang met behulp van Azure AD. U verleent gebruikers of groepen de mogelijkheid om de sleutel kluizen in een resource groep te beheren. U verleent de toegang op een specifiek Scope niveau door de juiste Azure-rollen toe te wijzen. Als u toegang wilt verlenen aan een gebruiker om sleutel kluizen te beheren, wijst u een vooraf gedefinieerde [Key Vault Inzender](../../role-based-access-control/built-in-roles.md#key-vault-contributor) functie toe aan de gebruiker in een specifiek bereik. De volgende Scope niveaus kunnen worden toegewezen aan een Azure-rol:

- **Abonnement**: een Azure-rol die is toegewezen op abonnements niveau, is van toepassing op alle resource groepen en resources in dat abonnement.
- **Resource groep**: een Azure-rol die is toegewezen op het niveau van de resource groep, is van toepassing op alle resources in die resource groep.
- **Specifieke resource**: een Azure-rol die is toegewezen voor een specifieke resource, is van toepassing op die resource. In dit geval is de resource een specifieke sleutel kluis.

Er zijn verschillende vooraf gedefinieerde rollen. Als een vooraf gedefinieerde rol niet aan uw behoeften voldoet, kunt u uw eigen rol definiëren. Zie [Ingebouwde rollen in Azure](../../role-based-access-control/built-in-roles.md) voor meer informatie. 

U moet beschikken over `Microsoft.Authorization/roleAssignments/write` en `Microsoft.Authorization/roleAssignments/delete` -machtigingen, zoals beheerder of [eigenaar](../../role-based-access-control/built-in-roles.md#owner) van [gebruikers toegang](../../role-based-access-control/built-in-roles.md#user-access-administrator)

> [!IMPORTANT]
> Als een gebruiker `Contributor` machtigingen heeft voor een sleutel kluis beheer vlak, kan de gebruiker zichzelf toegang verlenen tot het gegevens vlak door een Key Vault toegangs beleid in te stellen. U moet nauw keurig bepalen wie `Contributor` rollen toegang heeft tot uw sleutel kluizen. Zorg ervoor dat alleen geautoriseerde personen uw sleutel kluizen, sleutels, geheimen en certificaten kunnen gebruiken en beheren.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Gegevens vlak en toegangs beleid

U kunt toegang tot het gegevens vlak verlenen door Key Vault toegangs beleid in te stellen voor een sleutel kluis. Als u dit toegangs beleid wilt instellen, moet een gebruiker, groep of toepassing `Key Vault Contributor` machtigingen hebben voor het beheer vlak voor die sleutel kluis.

U verleent toegang aan een gebruiker, groep of toepassing om specifieke bewerkingen uit te voeren voor sleutels of geheimen in een sleutel kluis. Key Vault ondersteunt Maxi maal 1.024 toegangs beleidsregels voor een sleutel kluis. Een Azure AD-beveiligings groep maken en gebruikers toevoegen aan de groep om gegevenslaag toegang te geven tot meerdere gebruikers.

U kunt hier de volledige lijst met kluis-en geheime bewerkingen zien: [referentie voor Key Vault bewerking](/rest/api/keyvault/#vault-operations)

<a id="key-vault-access-policies"></a> Key Vault toegangs beleid worden machtigingen afzonderlijk verleend aan sleutels, geheimen en certificaten.  Toegangs machtigingen voor sleutels, geheimen en certificaten bevinden zich op het niveau van de kluis. 

Zie [toegangs beleid voor Key Vault toewijzen](assign-access-policy-portal.md) voor meer informatie over het gebruik van sleutel kluis toegangs beleid.

> [!IMPORTANT]
> Key Vault toegangs beleid is van toepassing op het niveau van de kluis. Wanneer een gebruiker gemachtigd is om sleutels te maken en te verwijderen, kunnen ze deze bewerkingen uitvoeren op alle sleutels in die sleutel kluis.
Key Vault toegangs beleid biedt geen ondersteuning voor granulaire machtigingen op object niveau, zoals een specifieke sleutel, geheim of certificaat. 
>

## <a name="data-plane-and-azure-rbac-preview"></a>Gegevens vlak en Azure RBAC (preview-versie)

Toegangs beheer op basis van rollen is een alternatief machtigings model voor het beheren van de toegang tot Azure Key Vault data-vlak dat kan worden ingeschakeld op individuele sleutel kluizen. Het Azure RBAC-machtigings model is exclusief en eenmaal ingesteld, is het beleid voor kluis toegang inactief geworden. Azure Key Vault definieert een set ingebouwde Azure-rollen die algemene sets machtigingen omvatten die worden gebruikt voor toegang tot sleutels, geheimen of certificaten.

Wanneer een Azure-rol is toegewezen aan een Azure AD-beveiligings-principal, verleent Azure toegang tot de resources voor die beveiligings-principal. De toegang kan worden beperkt tot het niveau van het abonnement, de resource groep, de sleutel kluis of een afzonderlijke sleutel, geheim of certificaat. Een beveiligings-principal voor Azure AD kan een gebruiker, een groep, een service-principal van de toepassing of een [beheerde identiteit voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)zijn.

Belangrijkste voor delen van het gebruik van Azure RBAC-machtigingen via een kluis toegangs beleid zijn gecentraliseerd toegangs beheer en de integratie met [privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md). Privileged Identity Management biedt op tijd en goedkeuring gebaseerde rolactiveringen om de risico's van buitensporige, onnodige of verkeerd gebruikte toegangsmachtigingen te beperken voor resources die u belangrijk vindt.

Zie voor meer informatie over Key Vault gegevens vlak met Azure RBAC [Key Vault sleutels, certificaten en geheimen met een op rollen gebaseerd toegangs beheer van Azure](rbac-guide.md)

## <a name="firewalls-and-virtual-networks"></a>Firewalls en virtuele netwerken

Voor een extra beveiligingslaag kunt u firewalls en regels voor virtuele netwerken configureren. U kunt Key Vault firewalls en virtuele netwerken configureren om de toegang tot verkeer van alle netwerken (met inbegrip van Internet verkeer) standaard te weigeren. U kunt toegang verlenen aan verkeer van specifieke [virtuele Azure-netwerken](../../virtual-network/virtual-networks-overview.md) en open bare IP-adresbereiken voor het Internet, zodat u een beveiligde netwerk grens voor uw toepassingen maakt.

Hier volgen enkele voor beelden van hoe u service-eind punten kunt gebruiken:

* U gebruikt Key Vault om versleutelings sleutels, toepassings geheimen en certificaten op te slaan, en u wilt de toegang tot uw sleutel kluis blok keren via het open bare Internet.
* U de toegang tot uw sleutel kluis wilt vergren delen zodat alleen uw toepassing of een korte lijst met aangewezen hosts verbinding kan maken met uw sleutel kluis.
* U hebt een toepassing die wordt uitgevoerd in uw virtuele Azure-netwerk en dit virtuele netwerk is vergrendeld voor al het binnenkomende en uitgaande verkeer. Uw toepassing moet nog steeds verbinding maken met Key Vault om geheimen of certificaten op te halen, of om cryptografische sleutels te gebruiken.

Zie [Azure Key Vault firewalls en virtuele netwerken configureren](network-security.md) voor meer informatie over Key Vault firewall en virtuele netwerken.

> [!NOTE]
> Key Vault firewalls en regels voor virtuele netwerken zijn alleen van toepassing op het gegevens vlak van Key Vault. Key Vault besturings vlak bewerkingen (zoals het maken, verwijderen en wijzigen van bewerkingen, het instellen van toegangs beleid, firewalls en regels voor virtuele netwerken) worden niet beïnvloed door firewalls en regels voor virtuele netwerken.

## <a name="private-endpoint-connection"></a>Verbinding met particulier eind punt

In het geval dat Key Vault bloot stelling aan het publiek volledig moet worden geblokkeerd, kan een [persoonlijk Azure-eind punt](../../private-link/private-endpoint-overview.md) worden gebruikt. Een privé-eindpunt in Azure is een netwerkinterface waarmee u privé en veilig verbinding maakt met een service die door Azure Private Link mogelijk wordt gemaakt. Het privé-eindpunt maakt gebruik van een privé-IP-adres van uw VNet, waardoor de service feitelijk in uw VNet wordt geplaatst. Al het verkeer naar de service kan worden gerouteerd via het privé-eindpunt, zodat er geen gateways, NAT-apparaten, ExpressRoute of VPN-verbindingen of openbare IP-adressen nodig zijn. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U kunt verbinding maken met een exemplaar van een Azure-resource, zodat u het hoogste granulariteit krijgt in toegangsbeheer.

Algemene scenario's voor het gebruik van een persoonlijke koppeling voor Azure-Services:

- **Privétoegang tot services op het Azure-platform**: Verbind uw virtuele netwerk met services in Azure zonder een openbaar IP-adres bij de bron of bestemming. Serviceproviders kunnen hun services weergeven in hun eigen virtuele netwerk en consumenten hebben toegang tot deze services in hun lokale virtuele netwerk. Het Private Link-platform zorgt voor de connectiviteit tussen de consument en de services via het Azure-backbonenetwerk. 
 
- **On-premises en peered netwerken**: Toegang tot services die worden uitgevoerd in Azure vanuit on-premises via ExpressRoute-privépeering, VPN-tunnels en gekoppelde virtuele netwerken met behulp van privé-eindpunten. Het is niet nodig om de open bare peering in te stellen of door te bladeren op internet om de service te bereiken. Private Link biedt een veilige manier om workloads naar Azure te migreren.
 
- **Bescherming tegen gegevenslekken**: Een privé-eindpunt wordt toegewezen aan een instantie van een PaaS-resource in plaats van de gehele service. Consumenten kunnen alleen verbinding maken met de specifieke resource. Toegang tot andere resources in de service is geblokkeerd. Dit mechanisme biedt beveiliging tegen risico's van gegevenslekken. 
 
- **Globaal bereik**: Maak privé verbinding met services die in andere regio's worden uitgevoerd. Het virtuele netwerk van de consument kan zich in regio A bevinden en kan verbinding maken met services achter een Private Link in regio B.  
 
- **Breid uit naar uw eigen services**: Schakel dezelfde ervaring en functionaliteit in om uw service privé te maken voor consumenten in Azure. Als u uw service achter een standaard Azure Load Balancer plaatst, kunt u deze inschakelen voor Private Link. De gebruiker kan vervolgens rechtstreeks verbinding maken met uw service met behulp van een privé-eindpunt in zijn eigen virtuele netwerk. U kunt de verbindingsaanvragen beheren met een goedkeuringsaanroepstroom. Azure Private Link werkt voor consumenten en services die deel uitmaken van verschillende Azure Active Directory-tenants. 

Zie [Key Vault met persoonlijke Azure-koppeling](./private-link-service.md) voor meer informatie over persoonlijke eind punten.

## <a name="example"></a>Voorbeeld

In dit voor beeld ontwikkelen we een toepassing die gebruikmaakt van een certificaat voor TLS/SSL, Azure Storage voor het opslaan van gegevens en een RSA 2.048-bits sleutel voor het versleutelen van gegevens in Azure Storage. Onze toepassing wordt uitgevoerd op een virtuele machine (VM) van Azure (of een schaalset voor virtuele machines). We kunnen een sleutel kluis gebruiken om de toepassings geheimen op te slaan. We kunnen het Boots trap-certificaat dat door de toepassing wordt gebruikt, opslaan voor verificatie met Azure AD.

We hebben toegang tot de volgende opgeslagen sleutels en geheimen nodig:
- **TLS/SSL-certificaat**: wordt gebruikt voor TLS/SSL.
- **Opslag sleutel**: wordt gebruikt voor toegang tot het opslag account.
- **RSA 2.048-bits sleutel**: wordt gebruikt voor de versleutelings sleutel voor verpakken/uitpakken van gegevens Azure Storage.
- Door de **toepassing beheerde identiteit**: wordt gebruikt voor verificatie met Azure AD. Nadat toegang tot Key Vault is verleend, kan de toepassing de opslag sleutel en het certificaat ophalen.

We moeten de volgende rollen definiëren om aan te geven wie onze toepassing kan beheren, implementeren en controleren:
- **Beveiligingsteam**: IT-personeel van het kantoor van de CSO (Chief Security Officer) of soortgelijke medewerkers. Het beveiligings team is verantwoordelijk voor het behoorlijk veilig bewaren van geheimen. De geheimen kunnen TLS/SSL-certificaten, RSA-sleutels voor versleuteling, verbindings reeksen en opslag account sleutels bevatten.
- **Ontwikkelaars en operators**: De medewerkers die de toepassing ontwikkelen en implementeren in Azure. De leden van dit team maken geen deel uit van het beveiligingspersoneel. Ze mogen geen toegang hebben tot gevoelige gegevens zoals TLS/SSL-certificaten en RSA-sleutels. Alleen de toepassing die ze implementeren, moet toegang hebben tot gevoelige gegevens.
- **Auditors**: Deze rol is voor medewerkers die niet behoren tot het team van ontwikkelaars of algemene IT-medewerkers. Ze controleren het gebruik en onderhoud van certificaten, sleutels en geheimen en zorgen voor de naleving van beveiligingsstandaarden.

Er is nog een rol die zich buiten het bereik van de toepassing bevindt: de beheerder van het abonnement (of de resourcegroep). De abonnementsbeheerder stelt de initiële toegangsmachtigingen voor het beveiligingsteam in. Ze verlenen toegang tot het beveiligingsteam door gebruik te maken van een resourcegroep met de benodigde resources voor de toepassing.

We moeten de volgende bewerkingen voor onze rollen autoriseren:

**Beveiligingsteam**
- Maak sleutel kluizen.
- Schakel Key Vault logboek registratie in.
- Voeg sleutels en geheimen toe.
- Maak back-ups van sleutels voor herstel na nood gevallen.
- Stel Key Vault toegangs beleid in en wijs rollen toe om machtigingen te verlenen aan gebruikers en toepassingen voor specifieke bewerkingen.
- Stel de sleutels en geheimen regel matig samen.

**Ontwikkelaars en operators**
- Referenties ophalen van het beveiligings team voor de Boots trap-en TLS/SSL-certificaten (vinger afdrukken), de opslag sleutel (geheime URI) en de RSA-sleutel (sleutel-URI) voor terugloop/uitpakken.
- Ontwikkel en implementeer de toepassing om via een programma toegang te krijgen tot certificaten en geheimen.

**Auditors**
- Bekijk de Key Vault-Logboeken om het juiste gebruik van sleutels en geheimen te bevestigen en te voldoen aan de normen voor gegevens beveiliging.

De volgende tabel bevat een overzicht van de toegangs machtigingen voor onze rollen en toepassingen.

| Rol | Machtigingen voor de beheerlaag | Machtigingen voor gegevens vlak: beleid voor kluis toegang | Machtigingen voor gegevens vlak-Azure RBAC  |
| --- | --- | --- | --- |
| Beveiligingsteam | [Inzender Key Vault](../../role-based-access-control/built-in-roles.md#key-vault-contributor) | Certificaten: alle bewerkingen <br> Sleutels: alle bewerkingen <br> Geheimen: alle bewerkingen | [Key Vault beheerder](../../role-based-access-control/built-in-roles.md#key-vault-administrator) |
| Ontwikkel aars en &nbsp; Opera tors | Machtiging voor Key Vault implementeren<br><br> **Opmerking**: met deze machtiging kunnen geïmplementeerde vm's worden gebruikt voor het ophalen van geheimen uit een sleutel kluis. | Geen | Geen |
| Auditors | Geen | Certificaten: lijst <br> Sleutels: weergeven<br>Geheimen: weergeven<br><br> **Opmerking**: met deze machtiging kunnen Audi tors kenmerken (tags, activerings datums, verval datums) controleren op sleutels en geheimen die niet in de logboeken zijn verzonden. | [Key Vault lezer](../../role-based-access-control/built-in-roles.md#key-vault-reader) |
| Azure Storage-account | Geen | Sleutels: Get, List, wrapKey, sleutel uitpakken <br> | [Versleutelings gebruiker van crypto grafie-service Key Vault](../../role-based-access-control/built-in-roles.md#key-vault-crypto-service-encryption-user) |
| Toepassing | Geen | Geheimen: ophalen, lijst <br> Certificaten: ophalen, lijst | [Key Vault lezer](../../role-based-access-control/built-in-roles.md#key-vault-reader), [Key Vault geheime gebruiker](../../role-based-access-control/built-in-roles.md#key-vault-secrets-user) |

De drie team rollen hebben toegang tot andere resources, samen met Key Vault machtigingen. Ontwikkel aars en Opera tors hebben toegang nodig om Vm's (of de Web Apps-functie van Azure App Service) te implementeren. Audi tors hebben lees toegang nodig tot het opslag account waarin de Key Vault logboeken worden opgeslagen.

In ons voorbeeld wordt een eenvoudig scenario beschreven. In de praktijk kunnen scenario's complexer zijn. U kunt machtigingen voor uw sleutelkluis aanpassen op basis van uw behoeften. We gaan ervan uit dat het beveiligingsteam de sleutel- en geheimreferenties (URI's en vingerafdrukken) aanlevert die worden gebruikt door het DevOps-personeel in hun toepassingen. Ontwikkelaars en operators hebben geen toegang tot een gegevensvlak nodig. We richten ons op de beveiliging van uw sleutelkluis.

> [!NOTE]
> In dit voor beeld ziet u hoe Key Vault toegang wordt vergrendeld tijdens de productie. Ontwikkel aars moeten hun eigen abonnement of resource groep hebben met volledige machtigingen voor het beheren van hun kluizen, Vm's en het opslag account waar ze de toepassing ontwikkelen.

## <a name="resources"></a>Resources

- [Over Azure Key Vault](overview.md)
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
- [Azure RBAC](../../role-based-access-control/overview.md)
- [Private Link](../../private-link/private-link-overview.md)

## <a name="next-steps"></a>Volgende stappen

[Verifiëren bij Azure Key Vault](authentication.md)

[Een Key Vault toegangs beleid toewijzen](assign-access-policy-portal.md)

[Azure-rol toewijzen voor toegang tot sleutels, geheimen en certificaten](rbac-guide.md)

[Key Vault-firewalls en virtuele netwerken configureren](network-security.md)

[Een koppeling met een persoonlijke verbinding tot stand brengen met Key Vault](private-link-service.md)
