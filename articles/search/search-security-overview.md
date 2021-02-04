---
title: Beveiligingsoverzicht
titleSuffix: Azure Cognitive Search
description: Meer informatie over de beveiligings functies in azure Cognitive Search voor het beveiligen van eind punten, inhoud en bewerkingen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/04/2021
ms.custom: references_regions
ms.openlocfilehash: 954d08fa163b481393df28ae22016859badea694
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537303"
---
# <a name="security-overview-for-azure-cognitive-search"></a>Beveiligings overzicht voor Azure Cognitive Search

In dit artikel worden de beveiligings functies in azure Cognitive Search beschreven voor het beveiligen van inhoud en bewerkingen.

Voor inkomende aanvragen voor een zoek service is er sprake van een voortgang van beveiligings maatregelen die het Search service-eind punt beveiligen: van API-sleutels op de aanvraag, tot binnenkomende regels in de firewall, tot persoonlijke eind punten die uw service volledig afschermen van het open bare Internet.

Voor uitgaande aanvragen bij andere services wordt de meest voorkomende aanvraag gedaan door Indexeer functies die inhoud uit externe bronnen lezen. U kunt referenties opgeven voor de connection string. U kunt ook een beheerde identiteit instellen om te zoeken naar een vertrouwde service wanneer er toegang wordt verkregen tot gegevens van Azure Storage, Azure SQL, Cosmos DB of andere Azure-gegevens bronnen. Een beheerde identiteit is een vervanging van referenties of toegangs sleutels voor de verbinding. Zie [verbinding maken met een gegevens bron met behulp van een beheerde identiteit](search-howto-managed-identities-data-sources.md)voor meer informatie over deze mogelijkheid.

Schrijf bewerkingen naar externe services zijn weinig: een zoek service schrijft naar logboek bestanden en schrijft naar Azure Storage bij het maken van kennis winkels, persistentie van in cache opgeslagen verrijkingen en permanente fout opsporingsgegevens. Andere service-to-service-aanroepen, zoals Cognitive Services, worden uitgevoerd op het interne netwerk.

Bekijk deze video met snel tempo voor een overzicht van de beveiligings architectuur en elke functie categorie.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

## <a name="network-security"></a>Netwerkbeveiliging

<a name="service-access-and-authentication"></a>

Binnenkomende beveiligings functies beveiligen het eind punt van de zoek service via toenemende niveaus van beveiliging en complexiteit. Eerst moeten voor alle aanvragen een API-sleutel voor geverifieerde toegang zijn vereist. Ten tweede kunt u desgewenst firewall regels instellen waarmee de toegang tot specifieke IP-adressen wordt beperkt. Voor geavanceerde beveiliging is een derde optie om de persoonlijke Azure-koppeling in te scha kelen voor het afschermen van uw service-eind punt van al het Internet verkeer.

### <a name="public-access-using-api-keys"></a>Open bare toegang met API-sleutels

Een zoek service is standaard toegankelijk via de open bare Cloud, met behulp van verificatie op basis van sleutels voor beheer of toegang tot het eind punt van de zoek service. Het verzenden van een geldige sleutel wordt beschouwd als een bewijs dat de aanvraag afkomstig is van een vertrouwde entiteit. Verificatie op basis van een sleutel wordt in de volgende sectie besproken.

### <a name="configure-ip-firewalls"></a>IP-firewalls configureren

Als u de toegang tot uw zoek service verder wilt beheren, kunt u binnenkomende firewall regels maken die toegang tot een specifiek IP-adres of een bereik van IP-adressen toestaan. Alle client verbindingen moeten worden gemaakt via een toegestaan IP-adres of de verbinding wordt geweigerd.

:::image type="content" source="media/search-security-overview/inbound-firewall-ip-restrictions.png" alt-text="voor beeld van een architectuur diagram voor beperkte toegang met IP":::

U kunt de portal gebruiken om [inkomende toegang te configureren](service-configure-firewall.md).

U kunt ook de REST Api's voor beheer gebruiken. Met ingang van API-versie 2020-03-13, met de para meter [IpRule](/rest/api/searchmanagement/services/createorupdate#iprule) , kunt u de toegang tot uw service beperken door IP-adressen, afzonderlijk of in een bereik, te identificeren die u toegang wilt verlenen tot uw zoek service.

### <a name="network-isolation-through-a-private-endpoint-no-internet-traffic"></a>Netwerk isolatie via een persoonlijk eind punt (geen Internet verkeer)

U kunt een [privé-eind punt](../private-link/private-endpoint-overview.md) voor Azure Cognitive Search een client in een [virtueel netwerk](../virtual-network/virtual-networks-overview.md) in staat te maken om veilig toegang te krijgen tot gegevens in een zoek index via een [privé-koppeling](../private-link/private-link-overview.md).

Het persoonlijke eind punt gebruikt een IP-adres uit de adres ruimte van het virtuele netwerk voor verbindingen met uw zoek service. Netwerk verkeer tussen de client en de zoek service gaat over het virtuele netwerk en een privé koppeling op het micro soft-backbone-netwerk, waardoor de bloot stelling van het open bare Internet wordt geëlimineerd. Met een VNET kan beveiligde communicatie tussen bronnen worden gewaarborgd, met uw on-premises netwerk en via internet.

:::image type="content" source="media/search-security-overview/inbound-private-link-azure-cog-search.png" alt-text="voor beeld van een architectuur diagram voor toegang tot privé-eind punten":::

Hoewel deze oplossing het veiligst is, is het gebruik van aanvullende services een extra kost prijs. Zorg er dus voor dat u een duidelijk beeld hebt van de voor delen voordat u zich kunt voordoen. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/private-link/)voor meer informatie over de kosten. Bekijk de video boven aan dit artikel voor meer informatie over hoe deze onderdelen samen werken. De optie dekking van het privé-eind punt begint bij 5:48 in de video. Zie [een persoonlijk eind punt maken voor Azure Cognitive Search](service-create-private-endpoint.md)voor instructies over het instellen van het eind punt.

## <a name="authentication"></a>Verificatie

Voor inkomende aanvragen voor de zoek service wordt verificatie via een [verplichte API-sleutel](search-security-api-keys.md) (een teken reeks die wille keurig gegenereerde getallen en letters bevat) die de aanvraag bewijst van een betrouw bare bron. Cognitive Search biedt momenteel geen ondersteuning voor Azure Active Directory-verificatie voor inkomende aanvragen.

Uitgaande aanvragen van een Indexeer functie zijn onderhevig aan verificatie door de externe service. De subservice voor indexering in Cognitive Search kan een vertrouwde service op Azure worden gemaakt en verbinding maken met andere services met behulp van een beheerde identiteit. Zie een [indexerings verbinding met een gegevens bron instellen met behulp van een beheerde identiteit](search-howto-managed-identities-data-sources.md)voor meer informatie.

## <a name="authorization"></a>Autorisatie

Cognitive Search biedt verschillende autorisatie modellen voor inhouds beheer en Service beheer. 

### <a name="authorization-for-content-management"></a>Autorisatie voor inhouds beheer

Autorisatie voor inhoud en bewerkingen met betrekking tot inhoud, is schrijf toegang, overeenkomstig de [API-sleutel](search-security-api-keys.md) die in de aanvraag is gegeven. De API-sleutel is een verificatie mechanisme, maar verleent ook toegang, afhankelijk van het type API-sleutel.

+ Beheerder sleutel (Hiermee staat u lees-/schrijftoegang toe voor Create-Read-update-delete-bewerkingen op de zoek service), gemaakt wanneer de service wordt ingericht

+ Query sleutel (alleen-lezen toegang toestaan tot de verzameling documenten van een index), gemaakt als vereist en zijn ontworpen voor client toepassingen die query's uitvoeren

In toepassings code geeft u het eind punt en een API-sleutel op om toegang tot inhoud en opties toe te staan. Een eind punt kan de service zelf, de verzameling indexen, een specifieke index, een documenten verzameling of een specifiek document zijn. Wanneer u samenvoegt, vormt het eind punt, de bewerking (bijvoorbeeld een aanvraag voor maken of bijwerken) en het machtigings niveau (volledige of alleen-lezen rechten op basis van de sleutel) de beveiligings formule die inhoud en bewerkingen beveiligt.

### <a name="controlling-access-to-indexes"></a>Toegang tot indexen beheren

In azure Cognitive Search is een afzonderlijke index geen beveiligbaar object. In plaats daarvan wordt de toegang tot een index bepaald op de service laag (lees-of schrijf toegang op basis van welke API-sleutel u opgeeft), samen met de context van een bewerking.

Voor alleen-lezen toegang kunt u de structuur query aanvragen om verbinding te maken met behulp van een [query sleutel](search-security-rbac.md)en de specifieke index die door uw app wordt gebruikt, toevoegen. In een query aanvraag is er geen idee van het samen voegen van indexen of het tegelijkertijd openen van meerdere indexen, zodat alle aanvragen een enkele index per definitie doel hebben. Als zodanig definieert de constructie van de query aanvraag zelf (een sleutel plus een enkele doel index) de beveiligings grens.

De beheerder en ontwikkelaars toegang tot indexen zijn niet-gedifferentieerd: beide moeten schrijf toegang hebben voor het maken, verwijderen en bijwerken van objecten die worden beheerd door de service. Iedereen met een [beheerders sleutel](search-security-rbac.md) voor uw service kan elke index in dezelfde service lezen, wijzigen of verwijderen. Voor beveiliging tegen onbedoelde of schadelijke verwijdering van indexen is uw interne broncode beheer voor code assets de oplossing voor het terugdraaien van een ongewenste index verwijderen of wijzigen. Azure Cognitive Search heeft een failover in het cluster om Beschik baarheid te garanderen, maar de code van uw eigen programma wordt niet opgeslagen of uitgevoerd voor het maken of laden van indexen.

Voor multitenancy-oplossingen die beveiligings grenzen vereisen op index niveau, bevatten dergelijke oplossingen doorgaans een middelste laag, die klanten gebruiken voor het verwerken van index isolatie. Zie [ontwerp patronen voor SaaS-toepassingen met meerdere tenants en Azure Cognitive Search](search-modeling-multitenant-saas-applications.md)voor meer informatie over de multi tenant-use-case.

### <a name="controlling-access-to-documents"></a>Toegang tot documenten beheren

Als u nauw keuriger controle per gebruiker met de zoek resultaten nodig hebt, kunt u beveiligings filters voor uw query's maken en documenten retour neren die zijn gekoppeld aan een bepaalde beveiligings identiteit. 

Conceptueel equivalent van ' beveiliging op rijniveau ', de autorisatie voor inhoud in de index wordt niet systeem eigen ondersteund met behulp van vooraf gedefinieerde rollen of roltoewijzingen die worden toegewezen aan entiteiten in Azure Active Directory. Gebruikers machtigingen voor gegevens in externe systemen, zoals Cosmos DB, worden niet overgedragen met deze gegevens als ze worden geïndexeerd door Cognitive Search.

Tijdelijke oplossingen voor oplossingen waarvoor beveiliging op rijniveau is vereist: het maken van een veld in de gegevens bron dat een beveiligings groep of gebruikers identiteit vertegenwoordigt, en vervolgens filters in Cognitive Search gebruiken om selectie resultaten van documenten en inhoud op basis van identiteiten te wissen. In de volgende tabel worden twee benaderingen beschreven waarmee Zoek resultaten van niet-geautoriseerde inhoud worden bijgesneden.

| Methode | Description |
|----------|-------------|
|[Beveiligings beperking op basis van identiteits filters](search-security-trimming-for-azure-search.md)  | Documenteert de basis werk stroom voor het implementeren van toegangs beheer voor gebruikers identiteit. Het onderwerp bevat het toevoegen van beveiligings-id's aan een index en legt vervolgens een overzicht van de filtering uit voor dat veld om de resultaten van verboden inhoud te kunnen knippen. |
|[Beveiligings beperking op basis van Azure Active Directory-identiteiten](search-security-trimming-for-azure-search-with-aad.md)  | In dit artikel wordt het vorige artikel uitgebreid met stappen voor het ophalen van identiteiten van Azure Active Directory (Azure AD), een van de [gratis services](https://azure.microsoft.com/free/) in het Azure-Cloud platform. |

### <a name="authorization-for-service-management"></a>Autorisatie voor Service beheer

Service Management-bewerkingen worden geautoriseerd via [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../role-based-access-control/overview.md). Azure RBAC is een autorisatie systeem dat is gebouwd op [Azure Resource Manager](../azure-resource-manager/management/overview.md) voor het inrichten van Azure-resources. 

In azure Cognitive Search wordt Resource Manager gebruikt om de service te maken of te verwijderen, de API-sleutels te beheren en de service te schalen. Als zodanig bepalen Azure-roltoewijzingen dat deze taken kunnen worden uitgevoerd, ongeacht of ze de [Portal](search-manage.md), [Power shell](search-manage-powershell.md)of de [rest-api's van beheer](/rest/api/searchmanagement/search-howto-management-rest-api)gebruiken.

Er zijn [drie basis rollen](search-security-rbac.md#management-tasks-by-role) gedefinieerd voor het beheer van de zoek service. De roltoewijzingen kunnen worden gemaakt met behulp van een ondersteunde methodologie (Portal, Power shell, enzovoort) en worden gerespecteerd. De rollen eigenaar en Inzender kunnen diverse beheer functies uitvoeren. U kunt de rol van lezer toewijzen aan gebruikers die alleen essentiële informatie weer geven.

> [!Note]
> Met behulp van Azure-mechanismen kunt u een abonnement of resource vergren delen om te voor komen dat uw zoek service per ongeluk of onbevoegde wordt verwijderd door gebruikers met beheerders rechten. Zie voor meer informatie [bronnen vergren delen om onverwachte verwijdering te voor komen](../azure-resource-manager/management/lock-resources.md).

## <a name="threat-protection"></a>Bescherming tegen bedreigingen

Toegang tot inhoud in een zoek service vindt alleen gebruik van query's. Als uw zoek service het doel is van een query aanval, worden query's door het systeem verwijderd naarmate de piek capaciteit van het systeem nadert. 

Bandbreedte beperking werkt anders voor verschillende Api's. Query-Api's (zoeken/suggesties/automatisch volt ooien) en het indexeren van Api's worden dynamisch beperkt op basis van de belasting van de service. Index-Api's en service bewerkingen-API hebben limieten voor statisch aantal aanvragen. U kunt de limieten voor de statische frequentie aanvragen bekijken in [beperkings limieten](search-limits-quotas-capacity.md#throttling-limits). Zie [query aanvragen bewaken](search-monitor-queries.md)voor meer inzicht in het beperken van het gedrag.

<a name="encryption"></a>

## <a name="data-protection"></a>Gegevensbeveiliging

In de opslaglaag is gegevens versleuteling ingebouwd voor alle door service beheerde inhoud die op schijf wordt opgeslagen, inclusief indexen, synoniemen en de definities van Indexeer functies, gegevens bronnen en vaardig heden. U kunt eventueel door de klant beheerde sleutels (CMK) toevoegen voor een bijkomende versleuteling van geïndexeerde inhoud. Voor services die na augustus 1 2020 zijn gemaakt, wordt CMK-versleuteling uitgebreid naar gegevens op tijdelijke schijven, voor een volledige dubbele versleuteling van geïndexeerde inhoud.

### <a name="data-in-transit"></a>Actieve gegevens

In azure Cognitive Search begint versleuteling met verbindingen en verzen dingen, en wordt uitgebreid naar inhoud die is opgeslagen op schijf. Voor zoek services op het open bare Internet luistert Azure Cognitive Search op HTTPS-poort 443. Alle client-naar-service-verbindingen gebruiken TLS 1,2-versleuteling. Eerdere versies (1,0 of 1,1) worden niet ondersteund.

### <a name="encrypted-data-at-rest"></a>Versleutelde gegevens in rust

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

In azure Cognitive Search is dubbele versleuteling een uitbrei ding van CMK. Het is gezien als twee-fold versleuteling (eenmaal per CMK, en opnieuw door door service beheerde sleutels) en uitgebreid in het bereik, met lange termijn opslag die is geschreven naar een gegevens schijf, en opslag op korte termijn die naar tijdelijke schijven wordt geschreven. Het verschil tussen CMK vóór augustus 1 2020 en after, en wat CMK een functie met dubbele code ring maakt in azure Cognitive Search, is de extra versleuteling van gegevens op rest op tijdelijke schijven.

Dubbele versleuteling is momenteel beschikbaar op nieuwe services die na 1 augustus zijn gemaakt in deze regio's:

+ VS - west 2
+ VS - oost
+ VS - zuid-centraal
+ VS (overheid) - Virginia
+ VS (overheid) - Arizona

## <a name="security-management"></a>Beveiligingsbeheer

### <a name="api-keys"></a>API-sleutels

Afhankelijkheid van de op API-sleutel gebaseerde verificatie houdt in dat u een plan hebt voor het opnieuw genereren van de beheerders sleutel met regel matige tussen pozen, volgens de aanbevolen procedures voor Azure-beveiliging. Er zijn Maxi maal twee beheer sleutels per zoek service. Zie [API-sleutels maken en beheren](search-security-api-keys.md)voor meer informatie over het beveiligen en beheren van API-sleutels.

#### <a name="activity-and-diagnostic-logs"></a>Diagnostische en activiteitslogboeken

Cognitive Search registreert geen gebruikers identiteiten, dus u kunt niet verwijzen naar Logboeken voor informatie over een specifieke gebruiker. De service maakt echter logboeken maken-lezen-bijwerken-verwijderen, die u mogelijk kunt correleren met andere logboeken om het Agentschap van specifieke acties te begrijpen.

Door gebruik te maken van waarschuwingen en de infra structuur voor logboek registratie in azure, kunt u een query uitvoeren op volume pieken of andere acties die afwijken van de verwachte werk belasting. Zie [logboek gegevens verzamelen en analyseren](search-monitor-logs.md) en [query aanvragen controleren](search-monitor-queries.md)voor meer informatie over het instellen van Logboeken.

### <a name="certifications-and-compliance"></a>Certificering en compliance

Azure Cognitive Search maakt deel uit van regel matige controles en is gecertificeerd tegen een aantal globale, regionale en branchespecifieke normen voor zowel de open bare Cloud als Azure Government. Voor de volledige lijst downloadt u de [ **Microsoft Azure compliance-aanbod**](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) van het technisch document op de pagina officiële controle rapporten.

Voor naleving kunt u [Azure Policy](../governance/policy/overview.md) gebruiken om de aanbevolen procedures voor beveiliging van [Azure Security](../security/benchmarks/introduction.md)te implementeren. Azure Security Bench Mark is een verzameling beveiligings aanbevelingen die zijn samengevoegd in beveiligings mechanismen die worden toegewezen aan belang rijke acties die u moet uitvoeren om bedreigingen voor services en gegevens te beperken. Er zijn momenteel 11 beveiligings controles, waaronder [netwerk beveiliging](../security/benchmarks/security-control-network-security.md), [logboek registratie en controle](../security/benchmarks/security-control-logging-monitoring.md), en [gegevens beveiliging](../security/benchmarks/security-control-data-protection.md) om een paar te noemen.

Azure Policy is een functie die in Azure is ingebouwd en die u helpt bij het beheren van de naleving voor meerdere standaarden, inclusief die van Azure Security Bench Mark. Voor bekende benchmarks biedt Azure Policy ingebouwde definities die beide criteria bieden, evenals een actie die kan worden uitgevoerd en die niet compatibel is.

Voor Azure Cognitive Search is er momenteel één ingebouwde definitie. Het is voor diagnostische logboek registratie. Met deze ingebouwde kunt u een beleid toewijzen dat een wille keurige zoek service identificeert waarvoor diagnostische logboek registratie ontbreekt en vervolgens weer wordt ingeschakeld. Zie [Azure Policy regulerende nalevings controles voor Azure Cognitive Search](security-controls-policy.md)voor meer informatie.

## <a name="see-also"></a>Zie ook

+ [Basisbeginselen van Azure Security](../security/fundamentals/index.yml)
+ [Azure-beveiliging](https://azure.microsoft.com/overview/security)
+ [Azure Security Center](../security-center/index.yml)