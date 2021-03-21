---
title: Inleiding tot Azure Storage - cloudopslag in Azure | Microsoft Docs
description: De kern Azure Storage platform is de oplossing voor de Cloud opslag van micro soft. Azure Storage biedt opslag voor gegevensobjecten die maximaal beschikbaar, veilig, duurzaam, in hoge mate schaalbaar en redundant is.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: b49e780714bd5e0662c6cb7ef208c473a8b45ccc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96498146"
---
# <a name="introduction-to-the-core-azure-storage-services"></a>Inleiding tot de kern Azure Storage services

Het Azure Storage platform is de oplossing voor de Cloud opslag van micro soft voor moderne scenario's voor gegevens opslag. Core Storage services biedt een zeer schaal bare object opslag voor gegevens objecten, schijf opslag voor Azure virtual machines (Vm's), een File System-Service voor de Cloud, een berichten Archief voor betrouw bare berichten en een NoSQL-archief. De services zijn:

- **Duurzaam en maximaal beschikbaar.** Redundantie zorgt ervoor dat uw gegevens veilig zijn in geval van tijdelijke hardwarefouten. U kunt er ook voor kiezen gegevens te repliceren in datacenters of geografische regio's voor extra beveiliging tegen lokale rampen of natuurrampen. Op deze manier gerepliceerde gegevens blijven maximaal beschikbaar in het geval van een stroomstoring.
- **Beveiligd.** Alle gegevens die naar een Azure Storage-account worden geschreven, worden versleuteld door de service. Azure Storage biedt u gedetailleerde controle over wie toegang tot uw gegevens heeft.
- **Aanpas.** Azure Storage is in hoge mate schaalbaar om te voldoen aan de gegevensopslag- en prestatiebehoeften van de huidige toepassingen.
- **Bijgehouden.** Azure verwerkt onderhoud van hardware, updates en kritieke problemen voor u.
- **Toegankelijk.** Gegevens in Azure Storage zijn overal ter wereld toegankelijk via HTTP of HTTPS. Micro soft biedt client bibliotheken voor Azure Storage in verschillende talen, waaronder .NET, Java, Node.js, Python, PHP, Ruby, Go en anderen, en een rijp REST API. Azure Storage ondersteunt scripts in Azure PowerShell of Azure CLI. En Azure Portal en Azure Storage Explorer bieden handige visuele oplossingen voor het werken met uw gegevens.  

## <a name="core-storage-services"></a>Kern opslag Services

Het Azure Storage-platform bevat de volgende gegevens Services:

- [Azure Blobs](../blobs/storage-blobs-introduction.md): een in hoge mate schaalbaar objectarchief voor tekst en binaire gegevens. Biedt ook ondersteuning voor big data Analytics via Data Lake Storage Gen2.
- [Azure files](../files/storage-files-introduction.md): beheerde bestands shares voor Cloud-en on-premises implementaties.
- [Azure Queues](../queues/storage-queues-introduction.md): een berichtenarchief voor betrouwbare uitwisseling van berichten tussen toepassingsonderdelen.
- [Azure Tables](../tables/table-storage-overview.md): een NoSQL-archief voor schemaloze opslag van gestructureerde gegevens.
- [Azure-schijven](../../virtual-machines/managed-disks-overview.md): opslag volumes op blok niveau voor Azure-vm's.

Elke service kan worden geopend via een opslagaccount. Zie [Een opslagaccount maken](storage-account-create.md) om aan de slag te gaan.

## <a name="example-scenarios"></a>Voorbeeldscenario's

In de volgende tabel worden de bestanden, blobs, schijven, wacht rijen en tabellen vergeleken en worden voorbeeld scenario's voor elke weer gegeven.

| Functie | Beschrijving | Wanneer gebruikt u dit? |
|--------------|-------------|-------------|
| **Azure Files** |Biedt volledig beheerde Cloud bestands shares waar u overal toegang toe hebt via het industrie standaard SMB-protocol (Server Message Block).<br><br>U kunt Azure-bestands shares koppelen vanuit Cloud-of on-premises implementaties van Windows, Linux en macOS. | U wilt een toepassing "lift en verschuiving" verplaatsen naar de cloud die al gebruikmaakt van de systeem eigen Api's van het systeem voor het delen van gegevens tussen IT en andere toepassingen die worden uitgevoerd in Azure.<br/><br/>U wilt on-premises bestands servers of NAS-apparaten vervangen of aanvullen.<br><br> U wilt hulpprogram ma's voor ontwikkeling en fout opsporing opslaan die toegankelijk moeten zijn vanaf een groot aantal virtuele machines. |
| **Azure-blobs** | Hiermee kunnen ongestructureerde gegevens worden opgeslagen en geopend op een enorme schaal in blok-blobs.<br/><br/>Biedt ook ondersteuning voor [Azure data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) voor oplossingen voor enter prise Big Data Analytics. | U wilt dat uw toepassing streaming en wille keurige toegangs scenario's ondersteunt.<br/><br/>U wilt vanaf elke locatie toegang hebben tot toepassings gegevens.<br/><br/>U wilt een Enter prise data Lake bouwen op Azure en big data Analytics uitvoeren. |
| **Azure-schijven** | Hiermee staat u toe dat gegevens permanent worden opgeslagen en toegankelijk vanaf een gekoppelde virtuele harde schijf. | U wilt ' lift-en Shift-toepassingen die gebruikmaken van systeem eigen Api's voor bestands systemen, om gegevens te lezen en te schrijven naar permanente schijven.<br/><br/>U wilt gegevens opslaan die niet nodig zijn voor toegang tot de virtuele machine waarop de schijf is aangesloten. |
| **Azure-wachtrijen** | Hiermee wordt asynchrone berichten wachtrij tussen toepassings onderdelen toegestaan. | U wilt toepassings onderdelen loskoppelen en asynchrone berichten gebruiken om onderling te communiceren.<br><br>Voor hulp bij het gebruik van wachtrij opslag versus Service Bus wachtrijen, Zie [opslag wachtrijen en service bus wacht rijen-vergeleken en daarentegen](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md). |
| **Azure-tabellen** | Bieden u de mogelijkheid om gestructureerde NoSQL-gegevens op te slaan in de Cloud, met een sleutel/kenmerk opslag met een schemaloos ontwerp. | U wilt flexibele gegevens sets, zoals gebruikers gegevens, opslaan voor webtoepassingen, adres boeken, apparaatgegevens of andere soorten meta gegevens die uw service nodig heeft. <br/><br/>Zie [ontwikkelen met Azure Cosmos DB Table-API en Azure Table Storage](../../cosmos-db/table-support.md)voor meer informatie over het gebruik van tabel opslag versus de Azure Cosmos db table-API. |

## <a name="blob-storage"></a>Blob Storage

Azure Blob Storage is Microsoft's oplossing voor de opslag van objecten in de cloud. Blob Storage is geoptimaliseerd voor het opslaan van grote hoeveelheden ongestructureerde gegevens, zoals tekst of binaire gegevens.

Blob-opslag is ideaal voor:

- Het rechtstreeks aan een browser leveren van afbeeldingen of documenten.
- De opslag van bestanden voor gedistribueerde toegang.
- Streaming van video en audio.
- De opslag van gegevens voor back-up en herstel, herstel na noodgevallen en archivering.
- De opslag van gegevens voor analyse door een on-premises of in Azure gehoste service.

Objecten in Blob-opslag zijn overal ter wereld toegankelijk via HTTP of HTTPS. Gebruikers of clienttoepassingen hebben toegang tot blobs via URL's, de [REST-API van Azure Storage](/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](/powershell/module/azure.storage), [Azure CLI](/cli/azure/storage) of een Azure Storage-clientbibliotheek. Deze clientbibliotheken zijn beschikbaar voor meerdere talen, waaronder [.NET](/dotnet/api/overview/azure/storage), [Java](/java/api/overview/azure/storage), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) en [Ruby](https://azure.github.io/azure-storage-ruby).

Zie [Inleiding tot blob-opslag](../blobs/storage-blobs-introduction.md) voor meer informatie over blob-opslag.

## <a name="azure-files"></a>Azure Files

Met [Azure files](../files/storage-files-introduction.md) kunt u Maxi maal beschik bare netwerk bestands shares instellen die toegankelijk zijn via het standaard SMB-protocol (Server Message Block). Dit betekent dat meerdere VM's dezelfde bestanden kunnen delen met zowel lees- als schrijftoegang. U kunt de bestanden ook lezen met behulp van de REST-interface of de opslagclientbibliotheken.

Eén ding dat Azure Files onderscheidt van bestanden in een zakelijke bestandsshare is het feit dat u overal ter wereld toegang hebt tot de bestanden via een URL die verwijst naar het bestand en een SAS-token (Shared Access Signature) bevat. U kunt SAS-tokens genereren. Met deze tokens hebt u gedurende een opgegeven tijdperiode toegang tot een specifiek privéasset.

Bestandsshares kunnen worden gebruikt voor veelvoorkomende scenario's:

- Veel on-premises toepassingen maken gebruik van bestandsshares. Met deze functie kunt u gemakkelijker toepassingen die gegevens delen, migreren naar Azure. Als u de bestandsshare koppelt aan dezelfde stationsletter die wordt gebruikt voor de on-premises toepassing, werkt het gedeelte van de toepassing dat toegang heeft tot de bestandsshare, (vrijwel) ongewijzigd.

- Configuratiebestanden kunnen worden opgeslagen in een bestandsshare en zijn toegankelijk vanaf meerdere VM's. Hulpprogramma's en hulpmiddelen die worden gebruikt door meerdere ontwikkelaars in een groep, kunnen worden opgeslagen in een bestandsshare. Hierdoor kan iedereen ze vinden en maakt iedereen ook gebruik van dezelfde versie.

- Bron logboeken, metrische gegevens en crash dumps zijn slechts drie voor beelden van informatie die naar een bestands share kan worden geschreven en later kunnen worden verwerkt of geanalyseerd.

Raadpleeg de [Inleiding tot Azure Files](../files/storage-files-introduction.md) voor meer informatie over Azure Files.

Sommige SMB-functies zijn niet van toepassing op de Cloud. Zie [functies die niet worden ondersteund door de Azure File-Service](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)voor meer informatie.

## <a name="queue-storage"></a>Queue Storage

De Azure Queue-service wordt gebruikt voor het opslaan en ophalen van berichten. Berichten in de wachtrij kunnen maximaal 64 kB groot zijn, en een wachtrij kan miljoenen berichten bevatten. Wachtrijen worden doorgaans gebruikt voor het opslaan van lijsten met berichten die asynchroon moeten worden verwerkt.

Stel dat u uw klanten in de gelegenheid wilt stellen om afbeeldingen te uploaden en dat u voor elke afbeelding miniaturen wilt maken. U kunt uw klant dan laten wachten totdat u tijdens het uploaden van de afbeeldingen de miniaturen hebt gemaakt. Een alternatief is het inzetten van een wachtrij. Wanneer de klant klaar is met uploaden, schrijft u een bericht naar de wachtrij. Vervolgens gebruikt u Azure Function om het bericht op te halen uit de wachtrij en de miniaturen te maken. Al deze verwerkingsstappen kunnen afzonderlijk worden geschaald, waardoor u meer controle hebt bij het afstemmen van de procedure op uw specifieke scenario.

Raadpleeg de [Inleiding tot Azure Queues](../queues/storage-queues-introduction.md) voor meer informatie over Azure Queues.

## <a name="table-storage"></a>Table Storage

Azure Table Storage maakt nu deel uit van Cosmos DB. Voor documentatie over Azure Table Storage raadpleegt u [Overzicht van Azure Table Storage](../tables/table-storage-overview.md). Naast de bestaande Azure Table Storage-service is er een nieuwe Azure Cosmos DB tabel-API die voor doorvoer geoptimaliseerde tabellen, wereldwijde distributie en automatische secundaire indexen biedt. Zie [Azure Cosmos DB Table-API](../../cosmos-db/table-introduction.md)voor meer informatie en om de nieuwe Premium-ervaring uit te proberen.

Zie [Overzicht van Azure Table Storage](../tables/table-storage-overview.md) voor meer informatie over Table Storage.

## <a name="disk-storage"></a>File Storage

Een Azure Managed disk is een virtuele harde schijf (VHD). U kunt dit beschouwen als een fysieke schijf op een on-premises server, maar is gevirtualiseerd. Azure Managed disks worden opgeslagen als pagina-blobs, die een wille keurig i/o-opslag object in azure zijn. Er wordt een beheerde schijf ' beheerd ' aangeroepen omdat het een abstractie is boven pagina-blobs, Blob-containers en Azure Storage-accounts. Met Managed disks hoeft u alleen maar de schijf in te richten en Azure zorgt voor de rest.

Zie [Introduction to Azure Managed disks](../../virtual-machines/managed-disks-overview.md)(Engelstalig) voor meer informatie over beheerde schijven.

## <a name="types-of-storage-accounts"></a>Typen opslagaccounts

Azure Storage biedt verschillende soorten opslagaccounts. Elk type ondersteunt verschillende functies en heeft een eigen prijsmodel. Zie [Overzicht van Azure-opslagaccounts](storage-account-overview.md) voor meer informatie over de typen opslagaccounts.

## <a name="secure-access-to-storage-accounts"></a>Beveiligde toegang tot opslag accounts

Elke aanvraag voor Azure Storage moet worden geautoriseerd. Azure Storage ondersteunt de volgende autorisatie methoden:

- **Integratie van Azure Active Directory (Azure AD) voor Blob-en wachtrij gegevens.** Azure Storage ondersteunt verificatie en autorisatie met Azure AD voor de BLOB-en Queue-Services via Azure op rollen gebaseerd toegangs beheer (Azure RBAC). Het is raadzaam aanvragen met Azure AD te autoriseren voor superieure beveiliging en gebruiks gemak. Zie [toegang tot Azure-blobs en-wacht rijen toestaan met Azure Active Directory](storage-auth-aad.md)voor meer informatie.
- **Azure AD-autorisatie via SMB voor Azure Files.** Azure Files ondersteunt autorisatie op basis van een identiteit via SMB (Server Message Block) via Azure Active Directory Domain Services (Azure AD DS) of on-premises Active Directory Domain Services (preview). De Windows-Vm's die zijn gekoppeld aan een domein, hebben toegang tot Azure-bestands shares met Azure AD-referenties. Zie voor meer informatie [overzicht van Azure files verificatie op basis van identiteiten voor SMB-toegang](../files/storage-files-active-directory-overview.md) en [het plannen van een Azure files-implementatie](../files/storage-files-planning.md#identity).
- **Autorisatie met gedeelde sleutel.** De Azure Storage Blob-, file-, Queue-en Table-Services ondersteunen autorisatie met gedeelde sleutel. Een client die gebruikmaakt van gedeelde sleutel autorisatie, geeft een header door elke aanvraag die is ondertekend met de toegangs sleutel voor het opslag account. Zie [autoriseren met gedeelde sleutel](/rest/api/storageservices/authorize-with-shared-key)voor meer informatie.
- **Autorisatie met behulp van Shared Access signatures (SAS).** Een Shared Access Signature (SAS) is een teken reeks met een beveiligings token dat kan worden toegevoegd aan de URI voor een opslag resource. Met het beveiligings token worden beperkingen, zoals machtigingen en het toegangs interval ingekapseld. Zie [using Shared Access signatures (SAS) (Engelstalig)](storage-sas-overview.md)voor meer informatie.
- **Anonieme toegang tot containers en blobs.** Een container en de bijbehorende blobs zijn mogelijk openbaar beschikbaar. Wanneer u opgeeft dat een container of BLOB openbaar is, kan iedereen deze anoniem lezen. Er is geen verificatie vereist. Zie [Anonieme leestoegang tot containers en blobs beheren](../blobs/anonymous-read-access-configure.md) voor meer informatie.

## <a name="encryption"></a>Versleuteling

Er zijn twee basis typen versleuteling beschikbaar voor de core Storage-services. Raadpleeg de Engelstalige [Azure Storage-beveiligingshandleiding](../blobs/security-recommendations.md) voor meer informatie over beveiliging en versleuteling.

### <a name="encryption-at-rest"></a>Versleuteling 'at rest'

Azure Storage versleuteling beschermt en beveiligt uw gegevens om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie. Azure Storage worden alle gegevens automatisch versleuteld voordat ze worden opgeslagen in het opslag account en ontsleuteld voordat ze worden opgehaald. De processen voor versleuteling, ontsleuteling en sleutel beheer zijn transparant voor gebruikers. Klanten kunnen er ook voor kiezen om hun eigen sleutels te beheren met behulp van Azure Key Vault. Zie [Azure Storage versleuteling voor Data-at-rest](storage-service-encryption.md)voor meer informatie.

### <a name="client-side-encryption"></a>Clientversleuteling

De Azure Storage-client bibliotheken bieden methoden voor het versleutelen van gegevens uit de client bibliotheek voordat deze via de kabel worden verzonden en het antwoord wordt ontsleuteld. Gegevens die worden versleuteld via versleuteling aan de client zijde worden ook versleuteld door Azure Storage. Zie [versleuteling aan client zijde met .net voor Azure Storage](storage-client-side-encryption.md)voor meer informatie over versleuteling aan de client zijde.

## <a name="redundancy"></a>Redundantie

Azure Storage slaat meerdere kopieën van uw gegevens op om ervoor te zorgen dat uw gegevens duurzaam zijn. Als u uw opslagaccount gaat instellen, selecteert u een type replicatie. Zie [Redundantie in Azure Storage](./storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) voor meer informatie.

## <a name="transfer-data-to-and-from-azure-storage"></a>Gegevens overdragen van en naar Azure Storage

Er zijn verschillende mogelijkheden om gegevens te verplaatsen van of naar Azure Storage. Welke optie u kiest, is afhankelijk van de grootte van de gegevensset en de bandbreedte van het netwerk. Zie [Een Azure-oplossing kiezen voor gegevensoverdracht](storage-choose-data-transfer-solution.md) voor meer informatie.

## <a name="pricing"></a>Prijzen

Bij het nemen van beslissingen over hoe uw gegevens worden opgeslagen en geopend, moet u ook rekening houden met de kosten. Zie [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie.

## <a name="storage-apis-libraries-and-tools"></a>Storage-API's, -bibliotheken en -hulpprogramma's

U kunt toegang krijgen tot bronnen in een opslag account op basis van elke taal die HTTP/HTTPS-aanvragen kan maken. Daarnaast bieden de kern Azure Storage services programmeer bibliotheken aan voor verschillende populaire talen. Deze bibliotheken vereenvoudigen veel aspecten van het werken met Azure Storage door bewerkingen zoals synchrone en asynchrone aanroepafhandeling, batchverwerking van bewerkingen, uitzonderingsbeheer, automatische nieuwe pogingen, werking, enzovoort, voor hun rekening te nemen. Bibliotheken zijn momenteel beschikbaar voor de volgende talen en platformen (deze lijst wordt in de toekomst uitgebreid):

### <a name="azure-storage-data-api-and-library-references"></a>Azure Storage-gegevens-API en bibliotheekverwijzingen

- [Azure Storage REST API](/rest/api/storageservices/)
- [Azure Storage-client bibliotheek voor .NET](/dotnet/api/overview/azure/storage)
- [Azure Storage-client bibliotheek voor Java/Android](/java/api/overview/azure/storage)
- [Client bibliotheek Azure Storage voor Node.js](/javascript/api/overview/azure/storage-overview)
- [Client bibliotheek voor python Azure Storage](https://github.com/Azure/azure-storage-python)
- [Client bibliotheek voor PHP Azure Storage](https://github.com/Azure/azure-storage-php)
- [Azure Storage-client bibliotheek voor ruby](https://github.com/Azure/azure-storage-ruby)
- [Azure Storage-client bibliotheek voor C++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Azure Storage-beheer-API en bibliotheekverwijzingen

- [REST API van opslagresourceprovider](/rest/api/storagerp/)
- [Clientbibliotheek van opslagresourceprovider voor .NET](/dotnet/api/overview/azure/storage/management)
- [REST API van opslagservicebeheer (klassiek)](/previous-versions/azure/reference/ee460790(v=azure.100))

### <a name="azure-storage-data-movement-api-and-library-references"></a>Azure Storage-gegevensverplaatsing-API en bibliotheekverwijzingen

- [REST-API van Storage Import/Export-service](/rest/api/storageimportexport/)
- [Clientbibliotheek van opslaggegevensverplaatsing voor .NET](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>Hulpprogramma's

- [Azure PowerShell-cmdlets voor Storage](/powershell/module/az.storage)
- [Azure CLI-cmdlets voor Storage](/cli/azure/storage)
- [Hulp programma AzCopy Command-Line](https://aka.ms/downloadazcopy)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) is een gratis, zelfstandige app van Microsoft waarmee u visueel met Azure Storage-gegevens kunt werken in Windows, macOS en Linux.
- [Azure Resource Manager sjablonen voor Azure Storage](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)

## <a name="next-steps"></a>Volgende stappen

Zie [een opslag account maken](storage-account-create.md)om aan de slag te gaan met kern Azure Storage services.