---
title: Gegevensversleutelingsmodellen in Microsoft Azure
description: In dit artikel vindt u een overzicht van gegevensversleutelingsmodellen in Microsoft Azure.
services: security
documentationcenter: na
author: msmbaldwin
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mbaldwin
ms.openlocfilehash: f76b2811531b49c9312a02a581e876f9ef569a2a
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750964"
---
# <a name="data-encryption-models"></a>Gegevensversleutelingmodellen

Een goed begrip van de verschillende versleutelingsmodellen en hun voor- en nadelen is essentieel om te begrijpen hoe de verschillende resourceproviders in Azure versleuteling-at-rest implementeren. Deze definities worden gedeeld door alle resourceproviders in Azure om gemeenschappelijke taal en taxonomie te garanderen.

Er zijn drie scenario's voor versleuteling aan de serverzijde:

- Versleuteling aan de serverzijde met behulp Service-Managed sleutels
  - Azure-resourceproviders voeren de versleutelings- en ontsleutelingsbewerkingen uit
  - Microsoft beheert de sleutels
  - Volledige cloudfunctionaliteit

- Versleuteling aan de serverzijde met behulp van door de klant beheerde sleutels in Azure Key Vault
  - Azure-resourceproviders voeren de versleutelings- en ontsleutelingsbewerkingen uit
  - Klant beheert sleutels via Azure Key Vault
  - Volledige cloudfunctionaliteit

- Versleuteling aan de serverzijde met behulp van door de klant beheerde sleutels op door de klant beheerde hardware
  - Azure-resourceproviders voeren de versleutelings- en ontsleutelingsbewerkingen uit
  - Klant beheert sleutels op door de klant beheerde hardware
  - Volledige cloudfunctionaliteit

Versleutelingsmodellen aan de serverzijde verwijzen naar versleuteling die wordt uitgevoerd door de Azure-service. In dat model voert de resourceprovider de versleutelings- en ontsleutelingsbewerkingen uit. Zo kunnen Azure Storage gegevens ontvangen in tekst zonder tekst en worden de versleuteling en ontsleuteling intern uitgevoerd. De resourceprovider kan gebruikmaken van versleutelingssleutels die worden beheerd door Microsoft of door de klant, afhankelijk van de opgegeven configuratie.

![Server](./media/encryption-models/azure-security-encryption-atrest-fig3.png)

Elk van de versleuteling-at-rest-modellen aan de serverzijde impliceert duidelijke kenmerken van sleutelbeheer. Dit omvat waar en hoe versleutelingssleutels worden gemaakt en opgeslagen, evenals de toegangsmodellen en de procedures voor sleutelrotatie.  

Houd rekening met het volgende voor versleuteling aan de clientzijde:

- Azure-services kunnen ontsleutelde gegevens niet zien
- Klanten beheren en opslaan sleutels on-premises (of in andere beveiligde winkels). Sleutels zijn niet beschikbaar voor Azure-services
- Beperkte cloudfunctionaliteit

De ondersteunde versleutelingsmodellen in Azure zijn opgesplitst in twee hoofdgroepen: 'Clientversleuteling' en 'Versleuteling aan serverzijde', zoals eerder vermeld. Onafhankelijk van het gebruikte versleutelings-at-rest-model, raden Azure-services altijd het gebruik aan van een beveiligd transport, zoals TLS of HTTPS. Versleuteling in transport moet daarom worden aangepakt door het transportprotocol en mag geen belangrijke factor zijn bij het bepalen welk versleuteling-at-rest-model moet worden gebruikt.

## <a name="client-encryption-model"></a>Clientversleutelingsmodel

Clientversleutelingsmodel verwijst naar versleuteling die buiten de resourceprovider of Azure wordt uitgevoerd door de service of aanroepingstoepassing. De versleuteling kan worden uitgevoerd door de servicetoepassing in Azure of door een toepassing die wordt uitgevoerd in het datacenter van de klant. In beide gevallen ontvangt de Azure Resource Provider bij het gebruik van dit versleutelingsmodel een versleutelde blob met gegevens zonder de mogelijkheid om de gegevens op welke manier dan ook te ontsleutelen of toegang te hebben tot de versleutelingssleutels. In dit model wordt het sleutelbeheer uitgevoerd door de aanroepende service/toepassing en is deze ondoorzichtig voor de Azure-service.

![Client](./media/encryption-models/azure-security-encryption-atrest-fig2.png)

## <a name="server-side-encryption-using-service-managed-keys"></a>Versleuteling aan de serverzijde met behulp van door de service beheerde sleutels

Voor veel klanten is het essentieel om ervoor te zorgen dat de gegevens worden versleuteld wanneer ze in rust zijn. Versleuteling aan de serverzijde met behulp van door de service beheerde sleutels maakt dit model mogelijk doordat klanten de specifieke resource (opslagaccount, SQL DB, enzovoort) kunnen markeren voor versleuteling en alle belangrijke beheeraspecten, zoals sleuteluitgifte, rotatie en back-ups, kunnen laten staan aan Microsoft. De meeste Azure-services die versleuteling in rust ondersteunen, ondersteunen doorgaans dit model voor het offloaden van het beheer van de versleutelingssleutels naar Azure. De Azure-resourceprovider maakt de sleutels, plaatst ze in beveiligde opslag en haalt ze op wanneer dat nodig is. Dit betekent dat de service volledige toegang heeft tot de sleutels en dat de service volledige controle heeft over het levenscyclusbeheer van referenties.

![Beheerd](./media/encryption-models/azure-security-encryption-atrest-fig4.png)

Versleuteling aan de serverzijde met behulp van door de service beheerde sleutels lost daarom snel de noodzaak aan van versleuteling in rust met lage overhead voor de klant. Wanneer deze beschikbaar is, opent een klant doorgaans de Azure Portal voor het doelabonnement en de doelresourceprovider en controleert deze een selectievakje waarin wordt aangegeven dat de gegevens moeten worden versleuteld. In sommige resourcemanagers is versleuteling aan de serverzijde met door de service beheerde sleutels standaard ingeschakeld.

Versleuteling aan de serverzijde met door Microsoft beheerde sleutels impliceert dat de service volledige toegang heeft om de sleutels op te slaan en te beheren. Hoewel sommige klanten de sleutels willen beheren omdat ze denken dat ze meer beveiliging krijgen, moeten de kosten en het risico van een aangepaste oplossing voor sleutelopslag worden overwogen bij het evalueren van dit model. In veel gevallen kan een organisatie bepalen dat resourcebeperkingen of risico's van een on-premises oplossing groter kunnen zijn dan het risico van cloudbeheer van de versleuteling-at-rest-sleutels.  Dit model is echter mogelijk niet voldoende voor organisaties die vereisten hebben om het maken of de levenscyclus van de versleutelingssleutels te beheren of om andere medewerkers de versleutelingssleutels van een service te laten beheren dan de medewerkers die de service beheren (dat wil zeggen scheiding van sleutelbeheer van het algemene beheermodel voor de service).

### <a name="key-access"></a>Toegang tot sleutel

Wanneer versleuteling aan de serverzijde met door de service beheerde sleutels wordt gebruikt, worden het maken, opslaan en openen van sleutels allemaal beheerd door de service. Normaal gesproken slaan de basisproviders van Azure-resources de gegevensversleutelingssleutels op in een opslag die zich dicht bij de gegevens en snel beschikbaar en toegankelijk is terwijl de sleutelversleutelingssleutels worden opgeslagen in een beveiligd intern opslag.

**Voordelen**

- Eenvoudige installatie
- Microsoft beheert sleutelrotatie, back-up en redundantie
- De klant heeft niet de kosten die zijn gekoppeld aan de implementatie of het risico van een aangepast sleutelbeheerschema.

**Nadelen**

- Geen klantcontrole over de versleutelingssleutels (sleutelspecificatie, levenscyclus, intrekking, enzovoort)
- Er is geen mogelijkheid om sleutelbeheer te scheiden van het algemene beheermodel voor de service

## <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Versleuteling aan de serverzijde met behulp van door de klant beheerde sleutels in Azure Key Vault

Voor scenario's waarin het nodig is om de data-at-rest te versleutelen en de versleutelingssleutels te beheren, kunnen klanten versleuteling aan de serverzijde gebruiken met behulp van door de klant beheerde sleutels in Key Vault. Sommige services kunnen alleen de hoofdsleutelversleutelingssleutel opslaan in Azure Key Vault en de versleutelde gegevensversleutelingssleutel opslaan op een interne locatie dichter bij de gegevens. In dat scenario kunnen klanten hun eigen sleutels meenemen naar Key Vault (BYOK - Bring Your Own Key) of nieuwe sleutels genereren en deze gebruiken om de gewenste resources te versleutelen. De resourceprovider voert de versleutelings- en ontsleutelingsbewerkingen uit, maar gebruikt de geconfigureerde sleutelversleutelingssleutel als basissleutel voor alle versleutelingsbewerkingen.

Verlies van sleutelversleutelingssleutels betekent verlies van gegevens. Daarom moeten sleutels niet worden verwijderd. Er moet een back-up van sleutels worden gemaakt wanneer deze worden gemaakt of geroteerd. [De functie Voor het verwijderen](../../key-vault/general/soft-delete-overview.md) van sleutels moet zijn ingeschakeld voor elke kluis die sleutelversleutelingssleutels opslaat. In plaats van een sleutel te verwijderen, stelt u ingeschakeld in op onwaar of stelt u de vervaldatum in.

### <a name="key-access"></a>Sleuteltoegang

Het versleutelingsmodel aan de serverzijde met door de klant beheerde sleutels in Azure Key Vault omvat de service die toegang heeft tot de sleutels die naar behoefte moeten worden versleuteld en ontsleuteld. Versleuteling in rustsleutels worden toegankelijk gemaakt voor een service via een toegangscontrolebeleid. Dit beleid verleent de service-id toegang tot het ontvangen van de sleutel. Een Azure-service die wordt uitgevoerd namens een gekoppeld abonnement, kan worden geconfigureerd met een identiteit in dat abonnement. De service kan Azure Active Directory verificatie uitvoeren en een verificatie-token ontvangen dat zichzelf identificeert als die service die namens het abonnement optreedt. Dat token kan vervolgens worden weergegeven Key Vault om een sleutel te verkrijgen die toegang heeft gekregen.

Voor bewerkingen met behulp van versleutelingssleutels kan een service-identiteit toegang krijgen tot een van de volgende bewerkingen: ontsleutelen, versleutelen, uitpakkenSleutel, wrapKey, verifiëren, ondertekenen, ophalen, lijst, bijwerken, maken, importeren, verwijderen, back-up maken en herstellen.

Als u een sleutel wilt verkrijgen voor het versleutelen of ontsleutelen van data-at-rest, moet de service-id die het Resource Manager-service-exemplaar wordt uitgevoerd, unwrapKey (om de sleutel voor ontsleuteling op te halen) en WrapKey (om een sleutel in te voegen in de sleutelkluis bij het maken van een nieuwe sleutel) hebben.

>[!NOTE]
>Zie de pagina Uw sleutelkluis beveiligen in Key Vault documentatie voor meer informatie [over Azure Key Vault verificatie.](../../key-vault/general/security-overview.md)

**Voordelen**

- Volledige controle over de gebruikte sleutels: versleutelingssleutels worden beheerd in de Key Vault van de klant.
- Mogelijkheid om meerdere services naar één master te versleutelen
- Kan sleutelbeheer scheiden van het algemene beheermodel voor de service
- Kan service- en sleutellocatie tussen regio's definiëren

**Nadelen**

- De klant is volledig verantwoordelijk voor sleuteltoegangsbeheer
- De klant is volledig verantwoordelijk voor het beheer van de levenscyclus van sleutels
- Extra configuratiekosten & configuratieoverhead

## <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Versleuteling aan de serverzijde met behulp van door de klant beheerde sleutels in door de klant beheerde hardware

Sommige Azure-services maken het hyok-sleutelbeheermodel (Host Your Own Key) mogelijk. Deze beheermodus is handig in scenario's waarin het nodig is om de data-at-rest te versleutelen en de sleutels te beheren in een eigen opslagplaats buiten het beheer van Microsoft. In dit model moet de service de sleutel ophalen van een externe site. De garanties voor prestaties en beschikbaarheid worden beïnvloed en de configuratie is complexer. Omdat de service wel toegang heeft tot de DEK tijdens de versleutelings- en ontsleutelingsbewerkingen, zijn de algemene beveiligingsgaranties van dit model vergelijkbaar met wanneer de sleutels door de klant worden beheerd in Azure Key Vault.  Als gevolg hiervan is dit model niet geschikt voor de meeste organisaties, tenzij ze specifieke vereisten voor sleutelbeheer hebben. Vanwege deze beperkingen bieden de meeste Azure-services geen ondersteuning voor versleuteling aan de serverzijde met behulp van door de server beheerde sleutels in door de klant beheerde hardware.

### <a name="key-access"></a>Sleuteltoegang

Wanneer versleuteling aan de serverzijde met behulp van door de service beheerde sleutels in door de klant beheerde hardware wordt gebruikt, worden de sleutels onderhouden op een systeem dat door de klant is geconfigureerd. Azure-services die dit model ondersteunen, bieden een manier om een beveiligde verbinding tot stand te brengen met een door de klant geleverd sleutelopslag.

**Voordelen**

- Volledige controle over de gebruikte hoofdsleutel: versleutelingssleutels worden beheerd door een door de klant geleverde opslag
- Mogelijkheid om meerdere services voor één master te versleutelen
- Kan sleutelbeheer scheiden van het algemene beheermodel voor de service
- Kan service- en sleutellocatie tussen regio's definiëren

**Nadelen**

- Volledige verantwoordelijkheid voor sleutelopslag, beveiliging, prestaties en beschikbaarheid
- Volledige verantwoordelijkheid voor sleuteltoegangsbeheer
- Volledige verantwoordelijkheid voor sleutellevenscyclusbeheer
- Aanzienlijke installatie-, configuratie- en lopende onderhoudskosten
- Verhoogde afhankelijkheid van netwerkbeschikbaarheid tussen het datacenter van de klant en Azure-datacenters.

## <a name="supporting-services"></a>Ondersteunende services
De Azure-services die elk versleutelingsmodel ondersteunen:

| Product, Functie of Service | Server-Side met behulp Service-Managed sleutel   | Server-Side met behulp Customer-Managed sleutel | Client-Side met behulp Client-Managed sleutel  |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
| **AI en Machine Learning**      |                    |                    |                    |
| Azure Cognitive Search           | Ja                | Ja                | -                  |
| Azure Cognitive Services         | Ja                | Ja                | -                  |
| Azure Machine Learning           | Ja                | Ja                | -                  |
| Azure Machine Learning Studio (klassiek) | Yes         | Preview, RSA 2048-bits | -               |
| Content Moderator                | Ja                | Ja                | -                  |
| Face                             | Ja                | Ja                | -                  |
| Taalbegrip           | Ja                | Ja                | -                  |
| Personalizer                     | Ja                | Ja                | -                  |
| QnA Maker                        | Ja                | Ja                | -                  |
| Spraakservices                  | Ja                | Ja                | -                  |
| Translator Text                  | Ja                | Ja                | -                  |
| Power BI                         | Yes                | Ja, RSA 4096-bits  | -                  |
| **Analyse**                    |                    |                    |                    |
| Azure Stream Analytics           | Yes                | Ja\*\*            | -                  |
| Event Hubs                       | Ja                | Ja                | -                  |
| Functions                        | Ja                | Ja                | -                  |
| Azure Analysis Services          | Ja                | -                  | -                  |
| Azure Data Catalog               | Yes                | -                  | -                  |
| Azure HDInsight                  | Yes                | Alles                | -                  |
| Azure Monitor Application Insights | Ja                | Ja                | -                  |
| Azure Monitor Log Analytics      | Ja                | Ja                | -                  |
| Azure Data Explorer              | Ja                | Ja                | -                  |
| Azure Data Factory               | Ja                | Ja                | -                  |
| Azure Data Lake Store            | Ja                | Ja, RSA 2048-bits  | -                  |
| **Containers**                   |                    |                    |                    |
| Azure Kubernetes Service         | Ja                | Ja                | -                  |
| Container Instances              | Ja                | Ja                | -                  |
| Container Registry               | Ja                | Ja                | -                  |
| **Compute**                      |                    |                    |                    |
| Virtual Machines                 | Ja                | Ja                | -                  |
| Virtuele-machineschaalset        | Ja                | Ja                | -                  |
| SAP HANA                         | Ja                | Ja                | -                  |
| App Service                      | Yes                | Ja\*\*            | -                  |
| Automation                       | Yes                | Ja\*\*            | -                  |
| Azure Functions                  | Yes                | Ja\*\*            | -                  |
| Azure Portal                     | Yes                | Ja\*\*            | -                  |
| Logic Apps                       | Ja                | Ja                | -                  |
| Door Azure beheerde toepassingen       | Yes                | Ja\*\*            | -                  |
| Service Bus                      | Ja                | Ja                | -                  |
| Siteherstel                    | Ja                | Ja                | -                  |
| **Databases**                    |                    |                    |                    |
| SQL Server on Virtual Machines   | Ja                | Ja                | Ja                |
| Azure SQL Database               | Ja                | Ja, RSA 3072-bits  | Yes                |
| Azure SQL Database for MariaDB   | Yes                | -                  | -                  |
| Azure SQL Database voor MySQL     | Ja                | Ja                | -                  |
| Azure SQL Database voor PostgreSQL | Ja               | Ja                | -                  |
| Azure Synapse Analytics          | Yes                | Ja, RSA 3072-bits  | -                  |
| SQL Server Stretch Database      | Yes                | Ja, RSA 3072-bits  | Yes                |
| Table Storage                    | Ja                | Ja                | Ja                |
| Azure Cosmos DB                  | Ja                | Ja                | -                  |
| Azure Databricks                 | Ja                | Ja                | -                  |
| Azure Database Migration-service | Yes                | N/a\*              | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps Services            | Yes                | -                  | -                  |
| Azure-opslagplaatsen                      | Yes                | -                  | -                  |
| **Identiteit**                     |                    |                    |                    |
| Azure Active Directory           | Yes                | -                  | -                  |
| Azure Active Directory Domain Services | Ja          | Ja                | -                  |
| **Integratie**                  |                    |                    |                    |
| Service Bus                      | Ja                | Ja                | Ja                |
| Event Grid                       | Yes                | -                  | -                  |
| API Management                   | Yes                | -                  | -                  |
| **IoT-services**                 |                    |                    |                    |
| IoT Hub                          | Ja                | Ja                | Ja                |
| IoT Hub Device Provisioning      | Ja                | Ja                | -                  |
| **Beheer en governance**    |                    |                    |                    |
| Azure Site Recovery              | Yes                | -                  | -                  |
| Azure Migrate                    | Ja                | Ja                | -                  |
| **Media**                        |                    |                    |                    |
| Media Services                   | Ja                | Ja                | Ja                |
| **Beveiliging**                     |                    |                    |                    |
| Azure Security Center voor IoT    | Ja                | Ja                | -                  |
| Azure Sentinel                   | Ja                | Ja                | -                  |
| **Storage**                      |                    |                    |                    |
| Blob Storage                     | Ja                | Ja                | Ja                |
| Premium Blob Storage             | Ja                | Ja                | Ja                |
| Disk Storage                     | Ja                | Ja                | -                  |
| Ultra Disk Storage               | Ja                | Ja                | -                  |
| Beheerde Disk Storage             | Ja                | Ja                | -                  |
| File Storage                     | Ja                | Ja                | -                  |
| Bestandsbestands Premium Storage             | Ja                | Ja                | -                  |
| File Sync                        | Ja                | Ja                | -                  |
| Queue Storage                    | Ja                | Ja                | Ja                |
| Avere vFXT                       | Yes                | -                  | -                  |
| Azure Cache voor Redis            | Yes                | N/a\*              | -                  |
| Azure NetApp Files               | Ja                | Ja                | -                  |
| Archive Storage                  | Ja                | Ja                | -                  |
| StorSimple                       | Ja                | Ja                | Ja                |
| Azure Backup                     | Ja                | Ja                | Ja                |
| Data Box                         | Ja                | -                  | Ja                |
| Data Box Edge                    | Ja                | Ja                | -                  |

\* Met deze service worden geen gegevens persistent. Tijdelijke caches, indien van belang, worden versleuteld met een Microsoft-sleutel.

\*\* Deze service ondersteunt het opslaan van gegevens in uw eigen Key Vault, opslagaccount of andere service voor persistentie van gegevens die al ondersteuning biedt voor Server-Side-versleuteling met Customer-Managed-sleutel.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie [over hoe versleuteling wordt gebruikt in Azure](encryption-overview.md).
- Meer informatie over hoe Azure dubbele [versleuteling gebruikt](double-encryption.md) om bedreigingen met versleutelingsgegevens te beperken.
