---
title: Beveiligingsoverzicht
titleSuffix: Azure Cognitive Search
description: Meer informatie over de beveiligings functies in azure Cognitive Search voor het beveiligen van eind punten, inhoud en bewerkingen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/22/2020
ms.custom: references_regions
ms.openlocfilehash: 49364681f0c5b4b6cc4d5f20778edb61e9f6f5b3
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695777"
---
# <a name="security-in-azure-cognitive-search---overview"></a>Beveiliging in azure Cognitive Search-overzicht

In dit artikel worden de beveiligings functies in azure Cognitive Search beschreven voor het beveiligen van inhoud en bewerkingen.

+ In de opslaglaag is gegevens versleuteling ingebouwd voor alle door service beheerde inhoud die op schijf is opgeslagen, inclusief indexen, synoniemen en de definities van Indexeer functies, gegevens bronnen en vaardig heden. U kunt eventueel door de klant beheerde sleutels (CMK) toevoegen voor een bijkomende versleuteling van geïndexeerde inhoud. Voor services die na augustus 1 2020 zijn gemaakt, wordt CMK-versleuteling uitgebreid naar gegevens op tijdelijke schijven, voor een volledige dubbele versleuteling van geïndexeerde inhoud.

+ Inkomende beveiliging heeft betrekking op beveiligingen op het eind punt van de zoek service op toenemende niveaus van beveiliging: van API-sleutels op de aanvraag, tot binnenkomende regels in de firewall, tot privé-eind punten die uw service volledig afschermen van het open bare Internet.

+ Uitgaande beveiliging heeft betrekking op Indexeer functies die inhoud uit externe bronnen halen. Stel voor uitgaande aanvragen een beheerde identiteit in om te zoeken naar een vertrouwde service wanneer er toegang wordt verkregen tot gegevens van Azure Storage, Azure SQL, Cosmos DB of andere Azure-gegevens bronnen. Een beheerde identiteit is een vervanging van referenties of toegangs sleutels voor de verbinding. In dit artikel komen de uitgaande beveiliging niet aan bod. Zie [verbinding maken met een gegevens bron met behulp van een beheerde identiteit](search-howto-managed-identities-data-sources.md)voor meer informatie over deze mogelijkheid.

Bekijk deze video met snel tempo voor een overzicht van de beveiligings architectuur en elke functie categorie.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

<a name="encryption"></a>

## <a name="encrypted-transmissions-and-storage"></a>Versleutelde verzen dingen en opslag

In azure Cognitive Search begint versleuteling met verbindingen en verzen dingen, en wordt uitgebreid naar inhoud die is opgeslagen op schijf. Voor zoek services op het open bare Internet luistert Azure Cognitive Search op HTTPS-poort 443. Alle client-naar-service-verbindingen gebruiken TLS 1,2-versleuteling. Eerdere versies (1,0 of 1,1) worden niet ondersteund.

:::image type="content" source="media/search-security-overview/encryption-at-rest-cmk.png" alt-text="diagram waarin verschillende soorten beveiliging op elk niveau van service betrokkenheid worden weer gegeven":::

Voor gegevens die intern door de zoek service worden verwerkt, worden de [gegevens versleutelings modellen](../security/fundamentals/encryption-models.md)beschreven in de volgende tabel. Sommige functies, zoals kennis opslag, incrementele verrijking en indexering gebaseerd indexering, lezen van of schrijven naar gegevens structuren in andere Azure-Services. Deze services hebben hun eigen coderings niveaus gescheiden van Azure Cognitive Search.

| Model | Subknooppuntsleutels&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Vereiste&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Beperkingen | Van toepassing op |
|------------------|-------|-------------|--------------|------------|
| versleuteling aan de server zijde | Door Microsoft beheerde sleutels | Geen (ingebouwd) | Geen, beschikbaar op alle lagen, in alle regio's, voor inhoud die is gemaakt na januari 24 2018. | Inhoud (indices en synoniemen kaarten) en definities (Indexeer functies, gegevens bronnen, vaardig heden) |
| versleuteling aan de server zijde | door de klant beheerde sleutels | Azure Key Vault | Beschikbaar op factureer bare lagen, in alle regio's, voor inhoud die is gemaakt na januari 2019. | Inhoud (indices en synoniemen toewijzingen) op gegevens schijven |
| dubbele versleuteling aan de server zijde | door de klant beheerde sleutels | Azure Key Vault | Beschikbaar op factureer bare lagen, in geselecteerde regio's, op zoek Services na augustus 1 2020. | Inhoud (indexen en synoniemen) op gegevens schijven en tijdelijke schijven |

### <a name="service-managed-keys"></a>Door service beheerde sleutels

Door service beheerde versleuteling is een micro soft-interne bewerking, gebaseerd op [Azure Storage-service versleuteling](../storage/common/storage-service-encryption.md), met 256-bits [AES-versleuteling](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard). Deze fout treedt automatisch op bij alle indexeringen, met inbegrip van incrementele updates voor indexen die niet volledig zijn versleuteld (gemaakt vóór 2018 januari).

### <a name="customer-managed-keys-cmk"></a>Door de klant beheerde sleutels (CMK)

Voor door de klant beheerde sleutels is een extra factureer bare service vereist, Azure Key Vault, die zich in een andere regio kan bevinden, maar onder hetzelfde abonnement als Azure Cognitive Search. Het inschakelen van CMK-versleuteling verhoogt de index grootte en vermindert de query prestaties. Op basis van waarnemingen tot heden kunt u verwachten dat er in query tijden een toename van 30%-60% wordt weer geven, hoewel de werkelijke prestaties afhankelijk zijn van de index definitie en typen query's. Als gevolg van deze invloed van prestaties raden we u aan deze functie alleen in te scha kelen voor indexen die echt nodig zijn. Zie voor meer informatie door de [klant beheerde versleutelings sleutels configureren in Azure Cognitive Search](search-security-manage-encryption-keys.md).

<a name="double-encryption"></a>

### <a name="double-encryption"></a>Dubbele versleuteling

In azure Cognitive Search is dubbele versleuteling een uitbrei ding van CMK. Het is gezien als twee-fold versleuteling (eenmaal per CMK, en opnieuw door door de service beheerde sleutels) en uitgebreid in het bereik, met lange termijn opslag die is geschreven naar een gegevens schijf en opslag op korte termijn die naar tijdelijke schijven wordt geschreven. Het verschil tussen CMK vóór augustus 1 2020 en after, en wat CMK een functie met dubbele code ring maakt in azure Cognitive Search, is de extra versleuteling van gegevens op rest op tijdelijke schijven.

Dubbele versleuteling is momenteel beschikbaar op nieuwe services die na 1 augustus zijn gemaakt in deze regio's:

+ West US 2
+ VS - oost
+ VS - zuid-centraal
+ VS (overheid) - Virginia
+ VS (overheid) - Arizona

<a name="service-access-and-authentication"></a>

## <a name="inbound-security-and-endpoint-protection"></a>Beveiliging van binnenkomende en Endpoint Protection

Binnenkomende beveiligings functies beveiligen het eind punt van de zoek service via toenemende niveaus van beveiliging en complexiteit. Eerst moeten voor alle aanvragen een API-sleutel voor geverifieerde toegang zijn vereist. Ten tweede kunt u desgewenst firewall regels instellen waarmee de toegang tot specifieke IP-adressen wordt beperkt. Voor geavanceerde beveiliging is een derde optie om de persoonlijke Azure-koppeling in te scha kelen voor het afschermen van uw service-eind punt van al het Internet verkeer.

### <a name="public-access-using-api-keys"></a>Open bare toegang met API-sleutels

Een zoek service is standaard toegankelijk via de open bare Cloud, met behulp van verificatie op basis van sleutels voor beheer of toegang tot het eind punt van de zoek service. Een [API-sleutel](search-security-rbac.md) is een teken reeks die bestaat uit wille keurig gegenereerde cijfers en letters. Het type sleutel (beheerder of query) bepaalt het toegangs niveau. Het verzenden van een geldige sleutel wordt beschouwd als een bewijs dat de aanvraag afkomstig is van een vertrouwde entiteit.

Er zijn twee toegangs niveaus voor uw zoek service, ingeschakeld door de volgende API-sleutels:

+ Beheerder sleutel (Hiermee staat u lees-/schrijftoegang toe voor [Create-Read-update-delete-](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) bewerkingen op de Search-service)

+ Query sleutel (alleen-lezen toegang toestaan tot de verzameling documenten van een index)

*Beheerders sleutels* worden gemaakt wanneer de service wordt ingericht. Er zijn twee beheer sleutels, aangeduid als *primair* en *secundair* , om ze recht te laten, maar in feite zijn ze uitwisselbaar. Elke service heeft twee beheer sleutels, zodat u één kunt implementeren zonder de toegang tot uw service te verliezen. U kunt de [beheer sleutel periodiek opnieuw genereren](search-security-api-keys.md#regenerate-admin-keys) volgens de aanbevolen procedures voor Azure-beveiliging, maar u kunt niet toevoegen aan het totale aantal beheerders sleutels. Er zijn Maxi maal twee beheer sleutels per zoek service.

*Query sleutels* worden als vereist gemaakt en zijn ontworpen voor client toepassingen die query's uitvoeren. U kunt Maxi maal 50 query sleutels maken. In toepassings code geeft u de zoek-URL en een query-API-sleutel op om alleen-lezen toegang tot de documenten verzameling van een specifieke index toe te staan. Samen, het eind punt, een API-sleutel voor alleen-lezen toegang en een doel index bepalen het bereik en toegangs niveau van de verbinding van uw client toepassing.

Verificatie is vereist voor elke aanvraag, waarbij elke aanvraag bestaat uit een verplichte sleutel, een bewerking en een object. Wanneer u samenvoegt, zijn de twee machtigings niveaus (volledig of alleen-lezen) plus de context (bijvoorbeeld een query bewerking in een index) voldoende voor het bieden van beveiliging op volledige spectrum voor service bewerkingen. Zie [API-sleutels maken en beheren](search-security-api-keys.md)voor meer informatie over sleutels.

### <a name="ip-restricted-access"></a>IP-beperkte toegang

Als u de toegang tot uw zoek service verder wilt beheren, kunt u binnenkomende firewall regels maken die toegang tot een specifiek IP-adres of een bereik van IP-adressen toestaan. Alle client verbindingen moeten worden gemaakt via een toegestaan IP-adres of de verbinding wordt geweigerd.

:::image type="content" source="media/search-security-overview/inbound-firewall-ip-restrictions.png" alt-text="voor beeld van een architectuur diagram voor beperkte toegang met IP":::

U kunt de portal gebruiken om [inkomende toegang te configureren](service-configure-firewall.md).

U kunt ook de REST Api's voor beheer gebruiken. Met ingang van API-versie 2020-03-13, met de para meter [IpRule](/rest/api/searchmanagement/services/createorupdate#iprule) , kunt u de toegang tot uw service beperken door IP-adressen, afzonderlijk of in een bereik, te identificeren die u toegang wilt verlenen tot uw zoek service.

### <a name="private-endpoint-no-internet-traffic"></a>Privé-eind punt (geen Internet verkeer)

Een [persoonlijk eind punt](../private-link/private-endpoint-overview.md) voor Azure Cognitive Search staat een client in een [virtueel netwerk](../virtual-network/virtual-networks-overview.md) toe om veilig toegang te krijgen tot gegevens in een zoek index via een [persoonlijke koppeling](../private-link/private-link-overview.md).

Het persoonlijke eind punt gebruikt een IP-adres uit de adres ruimte van het virtuele netwerk voor verbindingen met uw zoek service. Netwerk verkeer tussen de client en de zoek service gaat over het virtuele netwerk en een privé koppeling op het micro soft-backbone-netwerk, waardoor de bloot stelling van het open bare Internet wordt geëlimineerd. Met een VNET kan beveiligde communicatie tussen bronnen worden gewaarborgd, met uw on-premises netwerk en via internet.

:::image type="content" source="media/search-security-overview/inbound-private-link-azure-cog-search.png" alt-text="voor beeld van een architectuur diagram voor toegang tot privé-eind punten":::

Hoewel deze oplossing het veiligst is, is het gebruik van aanvullende services een extra kost prijs. Zorg er dus voor dat u een duidelijk beeld hebt van de voor delen voordat u zich kunt voordoen. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/private-link/)voor meer informatie over de kosten. Bekijk de video boven aan dit artikel voor meer informatie over hoe deze onderdelen samen werken. De optie dekking van het privé-eind punt begint bij 5:48 in de video. Zie [een persoonlijk eind punt maken voor Azure Cognitive Search](service-create-private-endpoint.md)voor instructies over het instellen van het eind punt.

## <a name="index-access"></a>Index toegang

In azure Cognitive Search is een afzonderlijke index geen beveiligbaar object. In plaats daarvan wordt de toegang tot een index bepaald op basis van de service laag (lees-of schrijf toegang tot de service), samen met de context van een bewerking.

Voor toegang door eind gebruikers kunt u query aanvragen structureren om verbinding te maken met behulp van een [query sleutel](search-security-rbac.md), waardoor elke aanvraag alleen-lezen is, en de specifieke index die door uw app wordt gebruikt. In een query aanvraag is er geen idee van het samen voegen van indexen of het tegelijkertijd openen van meerdere indexen, zodat alle aanvragen een enkele index per definitie doel hebben. Als zodanig definieert de constructie van de query aanvraag zelf (een sleutel plus een enkele doel index) de beveiligings grens.

De beheerder en ontwikkelaars toegang tot indexen zijn niet-gedifferentieerd: beide moeten schrijf toegang hebben voor het maken, verwijderen en bijwerken van objecten die worden beheerd door de service. Iedereen met een [beheerders sleutel](search-security-rbac.md) voor uw service kan elke index in dezelfde service lezen, wijzigen of verwijderen. Voor beveiliging tegen onbedoelde of schadelijke verwijdering van indexen is uw interne broncode beheer voor code assets de oplossing voor het terugdraaien van een ongewenste index verwijderen of wijzigen. Azure Cognitive Search heeft een failover in het cluster om Beschik baarheid te garanderen, maar de code van uw eigen programma wordt niet opgeslagen of uitgevoerd voor het maken of laden van indexen.

Voor multitenancy-oplossingen die beveiligings grenzen vereisen op index niveau, bevatten dergelijke oplossingen doorgaans een middelste laag, die klanten gebruiken voor het verwerken van index isolatie. Zie [ontwerp patronen voor SaaS-toepassingen met meerdere tenants en Azure Cognitive Search](search-modeling-multitenant-saas-applications.md)voor meer informatie over de multi tenant-use-case.

## <a name="user-access"></a>Gebruikerstoegang

Hoe een gebruiker toegang heeft tot een index en andere objecten, wordt bepaald door het type API-sleutel voor de aanvraag. De meeste ontwikkel aars maken en toewijzen [query sleutels](search-security-api-keys.md) voor Zoek opdrachten aan client zijde. Een query sleutel geeft alleen-lezen toegang tot Doorzoek bare inhoud in de index.

Als u nauw keuriger controle per gebruiker met de zoek resultaten nodig hebt, kunt u beveiligings filters voor uw query's maken en documenten retour neren die zijn gekoppeld aan een bepaalde beveiligings identiteit. In plaats van vooraf gedefinieerde rollen en roltoewijzingen, wordt toegangs beheer op basis van een id geïmplementeerd als een *filter* waarmee Zoek resultaten van documenten en inhoud worden verkleind op basis van identiteiten. In de volgende tabel worden twee benaderingen beschreven waarmee Zoek resultaten van niet-geautoriseerde inhoud worden bijgesneden.

| Methode | Beschrijving |
|----------|-------------|
|[Beveiligings beperking op basis van identiteits filters](search-security-trimming-for-azure-search.md)  | Documenteert de basis werk stroom voor het implementeren van toegangs beheer voor gebruikers identiteit. Het onderwerp bevat het toevoegen van beveiligings-id's aan een index en legt vervolgens een overzicht van de filtering uit voor dat veld om de resultaten van verboden inhoud te kunnen knippen. |
|[Beveiligings beperking op basis van Azure Active Directory-identiteiten](search-security-trimming-for-azure-search-with-aad.md)  | In dit artikel wordt het vorige artikel uitgebreid met stappen voor het ophalen van identiteiten van Azure Active Directory (Azure AD), een van de [gratis services](https://azure.microsoft.com/free/) in het Azure-Cloud platform. |

## <a name="administrative-rights"></a>Beheer rechten

[Toegangs beheer op basis van rollen (Azure RBAC) van Azure](../role-based-access-control/overview.md) is een autorisatie systeem dat is gebouwd op [Azure Resource Manager](../azure-resource-manager/management/overview.md) voor het inrichten van Azure-resources. In azure Cognitive Search wordt Resource Manager gebruikt om de service te maken of te verwijderen, de API-sleutels te beheren en de service te schalen. Als zodanig bepalen Azure-roltoewijzingen dat deze taken kunnen worden uitgevoerd, ongeacht of ze de [Portal](search-manage.md), [Power shell](search-manage-powershell.md)of de [rest-api's van beheer](/rest/api/searchmanagement/search-howto-management-rest-api)gebruiken.

Beheerders rechten voor inhoud die in de service wordt gehost, zoals de mogelijkheid om een index te maken of verwijderen, worden daarentegen door de API-sleutels uitgesteld zoals beschreven in de [vorige sectie](#index-access).

> [!TIP]
> Met behulp van Azure-mechanismen kunt u een abonnement of resource vergren delen om te voor komen dat uw zoek service per ongeluk of onbevoegde wordt verwijderd door gebruikers met beheerders rechten. Zie voor meer informatie [bronnen vergren delen om onverwachte verwijdering te voor komen](../azure-resource-manager/management/lock-resources.md).

## <a name="certifications-and-compliance"></a>Certificering en compliance

Azure Cognitive Search is gecertificeerd voor meerdere globale, regionale en branchespecifieke standaarden voor zowel de open bare Cloud als Azure Government. Voor de volledige lijst downloadt u de [ **Microsoft Azure compliance-aanbod**](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) van het technisch document op de pagina officiële controle rapporten.

Voor naleving kunt u [Azure Policy](../governance/policy/overview.md) gebruiken om de aanbevolen procedures voor beveiliging van [Azure Security](../security/benchmarks/introduction.md)te implementeren. Azure Security Bench Mark is een verzameling beveiligings aanbevelingen die zijn samengevoegd in beveiligings mechanismen die worden toegewezen aan belang rijke acties die u moet uitvoeren om bedreigingen voor services en gegevens te beperken. Er zijn momenteel 11 beveiligings controles, waaronder [netwerk beveiliging](../security/benchmarks/security-control-network-security.md), [logboek registratie en controle](../security/benchmarks/security-control-logging-monitoring.md), en [gegevens beveiliging](../security/benchmarks/security-control-data-protection.md) om een paar te noemen.

Azure Policy is een functie die in Azure is ingebouwd en die u helpt bij het beheren van de naleving voor meerdere standaarden, inclusief die van Azure Security Bench Mark. Voor bekende benchmarks biedt Azure Policy ingebouwde definities die beide criteria bieden, evenals een actie die kan worden uitgevoerd en die niet compatibel is.

Voor Azure Cognitive Search is er momenteel één ingebouwde definitie. Het is voor diagnostische logboek registratie. Met deze ingebouwde kunt u een beleid toewijzen dat een wille keurige zoek service identificeert waarvoor diagnostische logboek registratie ontbreekt en vervolgens weer wordt ingeschakeld. Zie [Azure Policy regulerende nalevings controles voor Azure Cognitive Search](security-controls-policy.md)voor meer informatie.

## <a name="see-also"></a>Zie ook

+ [Basisbeginselen van Azure Security](../security/fundamentals/index.yml)
+ [Azure-beveiliging](https://azure.microsoft.com/overview/security)
+ [Azure Security Center](../security-center/index.yml)
