---
title: Beperkte toegang verlenen tot gegevens met Shared Access signatures (SAS)
titleSuffix: Azure Storage
description: Meer informatie over het gebruik van Shared Access signatures (SAS) voor het delegeren van toegang tot Azure Storage resources, waaronder blobs, wacht rijen, tabellen en bestanden.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/28/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 51e73222233602491b0c8ed3835d032610c68e0d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722783"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Beperkte toegang verlenen tot Azure Storage-resources met behulp van Shared Access signatures (SAS)

Een Shared Access Signature (SAS) biedt beveiligde gedelegeerde toegang tot resources in uw opslag account. Met een SAS hebt u gedetailleerde controle over hoe een client toegang heeft tot uw gegevens. Bijvoorbeeld:
 
- Welke bronnen de client mag gebruiken.

- Welke machtigingen ze hebben voor deze resources.

- Hoe lang de SAS geldig is.

## <a name="types-of-shared-access-signatures"></a>Typen handtekeningen voor gedeelde toegang

Azure Storage ondersteunt drie typen hand tekeningen voor gedeelde toegang:

- SAS voor gebruikers overdracht

- Service-SA'S

- Account-SAS

### <a name="user-delegation-sas"></a>SAS voor gebruikers overdracht

Een SAS voor gebruikers overdracht wordt beveiligd met Azure Active Directory (Azure AD)-referenties en ook door de machtigingen die zijn opgegeven voor de SAS. Een SAS voor gebruikers overdracht is alleen van toepassing op Blob-opslag.

Zie [een gebruiker delegering sa's (rest API) maken](/rest/api/storageservices/create-user-delegation-sas)voor meer informatie over de sa's van de gebruikers overdracht.

### <a name="service-sas"></a>Service-SA'S

Een service-SAS is beveiligd met de sleutel van het opslag account. Een service-SAS delegeert toegang tot een bron in slechts een van de Azure Storage services: Blob-opslag, wachtrij opslag, tabel opslag of Azure Files.

Zie [Create a Service SAS (rest API) (Engelstalig)](/rest/api/storageservices/create-service-sas)voor meer informatie over de service-sa's.

### <a name="account-sas"></a>Account-SAS

Een account-SAS wordt beveiligd met de sleutel van het opslag account. Een account-SAS delegeert toegang tot resources in een of meer van de opslagservices. Alle bewerkingen die beschikbaar zijn via een service of de SAS voor gebruikers overdracht zijn ook beschikbaar via een account-SAS. 

U kunt de toegang ook delegeren naar het volgende:

- Bewerkingen op service niveau (bijvoorbeeld de bewerkingen service- **Eigenschappen ophalen/instellen** en **service statistieken ophalen** ). 

- Lees-, schrijf-en verwijder bewerkingen die niet zijn toegestaan met een service-SAS. 

[Maak een account SAS (rest API)](/rest/api/storageservices/create-account-sas)voor meer informatie over de account-SAS.

> [!NOTE]
> Micro soft raadt u aan om Azure AD-referenties, indien mogelijk, te gebruiken als een beveiligings best practice, in plaats van de account sleutel te gebruiken, wat eenvoudiger kan worden aangetast. Wanneer het ontwerp van uw toepassing gedeelde toegangs handtekeningen vereist voor toegang tot Blob Storage, gebruikt u Azure AD-referenties om een gebruikers delegering SA'S te maken indien mogelijk voor superieure beveiliging. Zie [toegang tot blobs en wacht rijen toestaan met Azure Active Directory](storage-auth-aad.md)voor meer informatie.

Een Shared Access Signature kan een van de volgende twee vormen hebben:

- **Ad-hoc SAS**. Wanneer u een ad-hoc SAS maakt, worden de start tijd, verloop tijd en machtigingen opgegeven in de SAS-URI. Elk type SAS kan een ad-hoc-SAS zijn.

- **Service-sa's met opgeslagen toegangs beleid**. Een opgeslagen toegangs beleid wordt gedefinieerd in een resource container. Dit kan een BLOB-container, tabel, wachtrij of bestands share zijn. Het beleid voor opgeslagen toegang kan worden gebruikt om beperkingen te beheren voor een of meer hand tekeningen voor gedeelde toegang van services. Wanneer u een service-SAS koppelt aan een opgeslagen toegangs beleid, neemt de SAS de beperkingen op &mdash; voor de start tijd, de verloop tijd en de machtigingen die zijn &mdash; gedefinieerd voor het opgeslagen toegangs beleid.

> [!NOTE]
> Een SAS van gebruikers of een account-SAS moet een ad-hoc-SA'S zijn. Opgeslagen toegangs beleid wordt niet ondersteund voor de gebruikers delegering SA'S of de account-SAS.

## <a name="how-a-shared-access-signature-works"></a>Hoe een Shared Access Signature werkt

Een Shared Access Signature is een ondertekende URI die verwijst naar een of meer opslag resources. De URI bevat een token dat een speciale set query parameters bevat. Het token geeft aan hoe de bronnen kunnen worden gebruikt door de client. Een van de query parameters, de hand tekening, wordt samengesteld op basis van de SAS-para meters en ondertekend met de sleutel die is gebruikt voor het maken van de SAS. Deze hand tekening wordt door Azure Storage gebruikt om toegang tot de opslag bron te verlenen.

> [!NOTE]
> Het is niet mogelijk om de generatie van SAS-tokens te controleren. Gebruikers met bevoegdheden voor het genereren van een SAS-token, hetzij met behulp van de account sleutel, hetzij via een Azure RBAC-roltoewijzing, kunnen dit doen zonder de kennis van de eigenaar van het opslag account. Zorg ervoor dat u de machtigingen beperkt waarmee gebruikers SAS-tokens kunnen genereren. Als u wilt voor komen dat gebruikers een SAS genereren die is ondertekend met de account sleutel voor werk belastingen voor blobs en wacht rijen, kunt u de toegang tot gedeelde sleutels voor het opslag account niet meer toestaan. Zie [autorisatie met gedeelde sleutel voor komen](shared-key-authorization-prevent.md)voor meer informatie.

### <a name="sas-signature-and-authorization"></a>SAS-hand tekening en autorisatie

U kunt een SAS-token ondertekenen met een sleutel voor gebruikers overdracht of met een sleutel voor het opslag account (gedeelde sleutel). 

#### <a name="signing-a-sas-token-with-a-user-delegation-key"></a>Een SAS-token ondertekenen met een sleutel voor gebruikers overdracht

U kunt een SAS-token ondertekenen met behulp van een *sleutel voor gebruikers overdracht* die is gemaakt met behulp van de referenties van Azure Active Directory (Azure AD). Een SAS voor gebruikers overdracht is ondertekend met de sleutel gebruikers overdracht.

Als u de sleutel wilt ophalen en vervolgens de SAS wilt maken, moet aan een Azure AD-beveiligingsprincipal een Azure-rol worden toegewezen die de `Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey` actie bevat. Zie [een gebruikers delegatie maken (rest API)](/rest/api/storageservices/create-user-delegation-sas)voor meer informatie.

#### <a name="signing-a-sas-token-with-an-account-key"></a>Een SAS-token ondertekenen met een account sleutel

Zowel een service-SAS als een account-SAS zijn ondertekend met de sleutel van het opslag account. Een toepassing moet toegang hebben tot de account sleutel om een SAS te maken die is ondertekend met de account sleutel.

Wanneer een aanvraag een SAS-token bevat, wordt die aanvraag geautoriseerd op basis van de manier waarop dat SAS-token is ondertekend. De toegangs sleutel of referenties die u gebruikt om een SAS-token te maken, worden ook door Azure Storage gebruikt om toegang te verlenen aan een client die de SAS bezit.

De volgende tabel bevat een overzicht van de manier waarop elk type SAS-token is geautoriseerd.

| Type SAS | Type autorisatie |
|-|-|
| SAS voor gebruikers overdracht (alleen Blob-opslag) | Azure AD |
| Service-SA'S | Gedeelde sleutel |
| Account-SAS | Gedeelde sleutel |

Micro soft raadt u aan om de SAS voor gebruikers te gebruiken wanneer dat mogelijk is voor een superieure beveiliging.

### <a name="sas-token"></a>SAS-token

Het SAS-token is een teken reeks die u aan de client zijde genereert, bijvoorbeeld door gebruik te maken van een van de Azure Storage-client bibliotheken. Het SAS-token wordt op geen enkele manier bijgehouden door Azure Storage. U kunt een onbeperkt aantal SAS-tokens maken aan de client zijde. Nadat u een SAS hebt gemaakt, kunt u deze distribueren naar client toepassingen die toegang nodig hebben tot resources in uw opslag account.

Client toepassingen bieden de SAS-URI die moet Azure Storage als onderdeel van een aanvraag. Vervolgens controleert de service de SAS-para meters en de hand tekening om te controleren of deze geldig is. Als de service controleert of de hand tekening geldig is, wordt de aanvraag geautoriseerd. Anders wordt de aanvraag geweigerd met fout code 403 (verboden).

Hier volgt een voor beeld van een SAS-URI van de service, waarin de bron-URI en het SAS-token worden weer gegeven. Omdat de SAS-token de URI-query teken reeks bevat, moet de bron-URI eerst worden gevolgd door een vraag teken en vervolgens door het SAS-token:

![Onderdelen van een SAS-URI (Service)](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>Wanneer moet u een Shared Access Signature gebruiken?

Gebruik een SAS om beveiligde toegang tot resources in uw opslag account te verlenen aan elke client die anders geen machtigingen heeft voor deze resources.

Een veelvoorkomend scenario waarbij een SAS handig is, is een service waar gebruikers hun eigen gegevens lezen en schrijven naar uw opslag account. In een scenario waarin een opslagaccount gebruikersgegevens opslaat, zijn er twee typische ontwerppatronen:

1. Clients uploaden en downloaden gegevens via een front-endproxyservice, waarmee verificatie wordt uitgevoerd. Met deze front-end proxy service kunt u bedrijfs regels valideren. Voor grote hoeveel heden gegevens of trans acties met veel volumes is het maken van een service die kan worden geschaald naar het bereik van de vraag mogelijk kostbaar of lastig.

   ![Scenario diagram: front-end proxy service](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

2. Met een eenvoudige service wordt de client indien nodig geverifieerd en vervolgens een SAS gegenereerd. Zodra de client toepassing de SAS heeft ontvangen, kan deze rechtstreeks toegang krijgen tot Storage-account bronnen. Toegangs machtigingen worden gedefinieerd door de SAS en voor het interval dat is toegestaan door de SAS. Dankzij de SAS hoeven alle gegevens niet via de front-endproxyservice te worden doorgestuurd.

   ![Scenario diagram: SAS-Provider service](./media/storage-sas-overview/sas-storage-provider-service.png)

Veel echte Services kunnen gebruikmaken van een hybride van deze twee benaderingen. Sommige gegevens kunnen bijvoorbeeld worden verwerkt en gevalideerd via de front-end-proxy. Andere gegevens worden opgeslagen en/of worden direct gelezen met SAS.

Daarnaast is een SAS vereist om toegang te verlenen tot het bron object in een Kopieer bewerking in bepaalde scenario's:

- Wanneer u een BLOB kopieert naar een andere blob die zich in een ander opslag account bevindt. 
  
  U kunt eventueel ook een SAS gebruiken om toegang te verlenen tot de doel-blob.

- Wanneer u een bestand kopieert naar een ander bestand dat zich in een ander opslag account bevindt. 

  U kunt eventueel ook een SAS gebruiken om toegang tot het doel bestand te verlenen.

- Wanneer u een BLOB naar een bestand of een bestand naar een BLOB kopieert. 

  U moet een SAS gebruiken, zelfs als de bron-en doel objecten zich in hetzelfde opslag account bevinden.

## <a name="best-practices-when-using-sas"></a>Best practices bij gebruik van SAS

Wanneer u gebruikmaakt van hand tekeningen voor gedeelde toegang in uw toepassingen, moet u rekening houden met twee mogelijke Risico's:

- Als een SAS wordt gelekt, kan deze worden gebruikt door iedereen die deze verkrijgt, waardoor uw opslag account mogelijk kan worden aangetast.

- Als een SAS die aan een client toepassing is gegeven, verloopt en de toepassing geen nieuwe SA'S kan ophalen van uw service, kan de functionaliteit van de toepassing worden belemmerd.

De volgende aanbevelingen voor het gebruik van hand tekeningen voor gedeelde toegang kunnen helpen bij het oplossen van deze Risico's:

- **Gebruik altijd HTTPS** om een SAS te maken of te distribueren. Als een SAS wordt door gegeven via HTTP en onderschept, kan een aanvaller die een man-in-the-middle-aanval uitvoert, de SAS lezen. Vervolgens kunnen ze die SA'S gebruiken, net zoals de beoogde gebruiker. Dit kan leiden tot gevoelige gegevens of beschadiging van gegevens door de kwaadwillende gebruiker.

- **Gebruik, indien mogelijk, een SAS voor gebruikers overdracht.** Een SAS voor gebruikers overdracht biedt superieure beveiliging voor een service-SAS of een account-SAS. Een SAS voor gebruikers overdracht wordt beveiligd met Azure AD-referenties, zodat u uw account sleutel niet hoeft op te slaan met uw code.

- **Er is een intrekkings plan aanwezig voor een SAS.** Zorg ervoor dat u hebt voor bereid dat u reageert als een SAS is aangetast.

- **Definieer een opgeslagen toegangs beleid voor een service-SAS.** Opgeslagen toegangs beleid biedt u de mogelijkheid om machtigingen in te trekken voor een service-SA'S zonder dat u de sleutels van het opslag account opnieuw hoeft te genereren. Stel de verloop tijd in de toekomst (of oneindig) in en zorg ervoor dat deze regel matig wordt bijgewerkt om deze verder in de toekomst te verplaatsen.

- **De bijna-termijn verloop tijden voor een ad-hoc SAS-service SAS of account-SA'S gebruiken.** Op deze manier, zelfs als er een SAS is aangetast, is deze alleen geldig voor een korte periode. Deze procedure is vooral belang rijk als u niet kunt verwijzen naar een opgeslagen toegangs beleid. De bijna-termijn verloop tijd beperkt ook de hoeveelheid gegevens die naar een BLOB kan worden geschreven door de beschik bare tijd voor het uploaden ervan te beperken.

- **Laat clients automatisch de SA'S vernieuwen als dat nodig is.** Clients moeten de SA'S goed vernieuwen voordat het verloopt, om tijd te bieden voor nieuwe pogingen als de service die de SAS biedt, niet beschikbaar is. Dit kan in sommige gevallen onnodig zijn. U kunt bijvoorbeeld voor komen dat de SA'S worden gebruikt voor een klein aantal directe, korte bedrijfs activiteiten. Deze bewerkingen worden naar verwachting binnen de verloop periode voltooid. Als gevolg hiervan wordt u niet verwacht dat de SA'S worden vernieuwd. Als u echter een-client hebt die regel matig aanvragen maakt via SAS, is de kans dat de verval datum is verlopen. 

- **Wees voorzichtig met de start tijd van SAS.** Als u de begin tijd voor een SAS instelt op de huidige tijd, kunnen fouten in de eerste paar minuten in aflopende omstandigheden optreden. Dit wordt veroorzaakt door verschillende machines met iets andere huidige tijden (ook wel Clock scheefheid genoemd). In het algemeen stelt u de start tijd in op ten minste 15 minuten in het verleden. U kunt de service ook niet instellen, waardoor deze onmiddellijk in alle gevallen geldig is. Dit geldt ook voor verloop tijd. Vergeet niet dat u tot wel 15 minuten aan de hand van een wille keurige aanvraag kunt zien. Voor clients die gebruikmaken van een REST-versie vóór 2012-02-12, is de maximale duur voor een SAS die niet verwijst naar een opgeslagen toegangs beleid, 1 uur. Beleids regels die een langere periode dan 1 uur opgeven, mislukken.

- **Wees voorzichtig met de SAS DateTime-indeling.** Voor sommige hulpprogram ma's (zoals AzCopy) hebt u datum notaties nodig voor ' +% Y-% m-% dT% H:%M:% SZ '. Deze indeling omvat met name de seconden. 

- **De resource moet specifiek zijn voor toegang tot de bron.** Een beveiligings best practice is om een gebruiker te voorzien van de mini maal vereiste bevoegdheden. Als een gebruiker alleen lees toegang nodig heeft tot één entiteit, dan verlenen zij Lees-en schrijf toegang tot de ene entiteit en niet lezen/schrijven/verwijderen voor alle entiteiten. Dit helpt ook de schade te beperken als een SAS is aangetast omdat de SAS minder kracht in de handen van een aanvaller heeft.

- **U begrijpt dat uw account wordt gefactureerd voor gebruik, inclusief via een SAS.** Als u schrijf toegang voor een BLOB biedt, kan een gebruiker ervoor kiezen om een 200 GB-BLOB te uploaden. Als u de gebruikers ook lees toegang hebt gegeven, kunnen ze het 10 keer downloaden, waardoor er 2 TB worden bespaard op basis van de kosten voor u. U kunt ook beperkte machtigingen opgeven om de mogelijke acties van kwaadwillende gebruikers te helpen voor komen. Gebruik SA'S met een korte levens duur om deze dreiging te verminderen (maar mindful aan de eind tijd te scheefen).

- **Valideer gegevens die zijn geschreven met behulp van een SAS.** Wanneer een client toepassing gegevens naar uw opslag account schrijft, houd er dan rekening mee dat er problemen met die gegevens kunnen optreden. Als u van plan bent om gegevens te valideren, voert u die validatie uit nadat de gegevens zijn geschreven en voordat deze door uw toepassing worden gebruikt. Deze oefening beschermt ook tegen beschadigde of schadelijke gegevens die naar uw account worden geschreven, hetzij door een gebruiker die de SA'S heeft aangeschaft, hetzij door een gebruiker die misbruik maakt van een gelekte SAS.

- **Weet wanneer u geen SAS wilt gebruiken.** Soms is het gebruik van een SAS niet alleen voor de Risico's die zijn gekoppeld aan een bepaalde bewerking ten opzichte van uw opslag account. Voor dergelijke bewerkingen maakt u een middelste laag service die naar uw opslag account schrijft na het uitvoeren van de validatie, verificatie en controle van bedrijfs regels. Soms is het eenvoudiger om de toegang op andere manieren te beheren. Als u bijvoorbeeld alle blobs in een container openbaar leesbaar wilt maken, kunt u de container openbaar maken, in plaats van een SAS aan elke client voor toegang te bieden.

- **Gebruik Azure Monitor en Azure Storage Logboeken om uw toepassing te bewaken.** Autorisatie fouten kunnen optreden vanwege een storing in uw SAS-Provider service. Ze kunnen ook optreden wanneer een opgeslagen toegangs beleid per ongeluk wordt verwijderd. U kunt de logboek registratie van Azure Monitor en opslag analyse gebruiken om elke Prikker in deze typen autorisatie fouten te observeren. Zie [Azure Storage metrische gegevens in azure monitor](../blobs/monitor-blob-storage.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json) en [Azure Opslaganalyse logboek registratie](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)voor meer informatie.

> [!NOTE]
> Opslag houdt geen rekening met het aantal Shared Access-hand tekeningen dat is gegenereerd voor een opslag account en er kan geen API worden verstrekt. Als u het aantal gedeelde toegangs handtekeningen wilt weten dat voor een opslag account is gegenereerd, moet u het nummer hand matig bijhouden.

## <a name="get-started-with-sas"></a>Aan de slag met SAS

Raadpleeg de volgende artikelen voor elk SAS-type om aan de slag te gaan met hand tekeningen voor gedeelde toegang.

### <a name="user-delegation-sas"></a>SAS voor gebruikers overdracht

- [Een SAS voor gebruikers overdracht maken voor een container of BLOB met Power shell](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Een SAS voor gebruikers overdracht maken voor een container of BLOB met de Azure CLI](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Een SAS voor gebruikers overdracht maken voor een container of BLOB met .NET](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>Service-SA'S

- [Een service-SAS maken voor een container of BLOB met .NET](../blobs/sas-service-create.md)

### <a name="account-sas"></a>Account-SAS

- [Een account-SAS maken met .NET](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Volgende stappen

- [Toegang delegeren met een hand tekening voor gedeelde toegang (REST API)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Een SAS (REST API) voor gebruikers overdracht maken](/rest/api/storageservices/create-user-delegation-sas)
- [Een service-SAS maken (REST API)](/rest/api/storageservices/create-service-sas)
- [Een account maken SAS (REST API)](/rest/api/storageservices/create-account-sas)
