---
title: Gegevens versleutelings modellen in Microsoft Azure
description: Dit artikel bevat een overzicht van gegevens versleutelings modellen in Microsoft Azure.
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
ms.openlocfilehash: 9194f38ddea5570d94ba6c87ea6a537cb41fb98c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051375"
---
# <a name="data-encryption-models"></a>Gegevensversleutelingmodellen

Het is belang rijk dat u begrijpt hoe de verschillende versleutelings modellen en hun voor-en nadelen essentieel zijn om te zien hoe de verschillende resource providers in azure versleuteling in rust implementeren. Deze definities worden gedeeld door alle resource providers in azure om een gemeen schappelijke taal en taxonomie te garanderen.

Er zijn drie scenario's voor versleuteling aan server zijde:

- Versleuteling aan de server zijde met Service-Managed sleutels
  - Azure-resource providers voeren de bewerkingen voor versleuteling en ontsleuteling uit
  - Micro soft beheert de sleutels
  - Volledige Cloud functionaliteit

- Versleuteling aan de server zijde met door de klant beheerde sleutels in Azure Key Vault
  - Azure-resource providers voeren de bewerkingen voor versleuteling en ontsleuteling uit
  - De besturings elementen van de klant worden via Azure Key Vault
  - Volledige Cloud functionaliteit

- Versleuteling aan de server zijde met door de klant beheerde sleutels op door de klant bestuurde hardware
  - Azure-resource providers voeren de bewerkingen voor versleuteling en ontsleuteling uit
  - Klant besturings elementen sleutels voor door de klant beheerde hardware
  - Volledige Cloud functionaliteit

Versleutelings modellen aan de server zijde verwijzen naar versleuteling die wordt uitgevoerd door de Azure-service. In dat model voert de resource provider de bewerkingen versleutelen en ontsleutelen uit. Azure Storage kunnen bijvoorbeeld gegevens ontvangen in bewerkingen met tekst zonder opmaak en de versleuteling en ontsleuteling intern uitvoeren. De resource provider kan gebruikmaken van versleutelings sleutels die door micro soft of door de klant worden beheerd, afhankelijk van de ingestelde configuratie.

![Server](./media/encryption-models/azure-security-encryption-atrest-fig3.png)

Elk van de versleuteling aan de server zijde bij rest modellen impliceert de onderscheidende kenmerken van sleutel beheer. Dit omvat waar en hoe versleutelings sleutels worden gemaakt en opgeslagen, evenals de toegangs modellen en de procedures voor sleutel rotatie.  

Voor versleuteling aan de client zijde moet u rekening houden met het volgende:

- De gedecodeerde gegevens kunnen niet worden weer gegeven in Azure-Services
- Klanten beheren en bewaren sleutels on-premises (of in andere beveiligde winkels). Sleutels zijn niet beschikbaar voor Azure-Services
- Gereduceerde Cloud functionaliteit

De ondersteunde versleutelings modellen in Azure worden onderverdeeld in twee hoofd groepen: ' client versleuteling ' en ' versleuteling aan de server zijde ' zoals eerder beschreven. Onafhankelijk van de versleutelings methode voor het gebruikte rest model, raadt Azure-Services altijd het gebruik van een beveiligd Trans Port, zoals TLS of HTTPS, aan. Versleuteling in Trans Port moet daarom worden verholpen door het transport protocol en mag niet een belang rijke factor zijn bij het bepalen van de versleuteling in het rest model dat moet worden gebruikt.

## <a name="client-encryption-model"></a>Client versleutelings model

Het client versleutelings model verwijst naar de versleuteling die buiten de resource provider of Azure wordt uitgevoerd door de service of de toepassing die u aanroept. De versleuteling kan worden uitgevoerd door de service toepassing in azure of door een toepassing die wordt uitgevoerd in het Data Center van de klant. Bij het gebruik van dit versleutelings model ontvangt de Azure-resource provider een versleutelde blob van gegevens zonder de mogelijkheid om de gegevens op enigerlei wijze te ontsleutelen of toegang te geven tot de versleutelings sleutels. In dit model wordt het sleutel beheer uitgevoerd door de aanroepende service/toepassing en is deze ondoorzichtig voor de Azure-service.

![Client](./media/encryption-models/azure-security-encryption-atrest-fig2.png)

## <a name="server-side-encryption-using-service-managed-keys"></a>Versleuteling aan de server zijde met door service beheerde sleutels

Voor veel klanten is de essentiële vereiste om ervoor te zorgen dat de gegevens worden versleuteld wanneer deze in rust zijn. Versleuteling aan de server zijde met door service beheerde sleutels maakt dit model mogelijk door klanten toe te staan om de specifieke resource (opslag account, SQL-data base, enzovoort) te markeren voor versleuteling en alle aspecten van het sleutel beheer, zoals sleutel uitgifte, rotatie en back-up naar micro soft, te laten staan. De meeste Azure-Services die ondersteuning bieden voor versleuteling in rust, bieden doorgaans ondersteuning voor dit model van het beheer van de versleutelings sleutels naar Azure. De Azure-resource provider maakt de sleutels, plaatst deze in beveiligde opslag en haalt ze op wanneer dat nodig is. Dit betekent dat de service volledige toegang heeft tot de sleutels en dat de service volledige controle heeft over het beheer van de referentie levenscyclus.

![bijgehouden](./media/encryption-models/azure-security-encryption-atrest-fig4.png)

Versleuteling aan de server zijde met door service beheerde sleutels maakt daarom snel de nood zaak om versleuteling op rest te hebben met lage overhead voor de klant. Als er een klant beschikbaar is, wordt doorgaans de Azure Portal voor het doel abonnement en de resource provider geopend en wordt een vak gecontroleerd waarin wordt aangegeven dat de gegevens moeten worden versleuteld. In sommige bron beheer server versleuteling met door service beheerde sleutels is standaard ingeschakeld.

Versleuteling aan de server zijde met door micro soft beheerde sleutels betekent dat de service volledige toegang heeft tot de sleutels op te slaan en te beheren. Hoewel sommige klanten de sleutels willen beheren omdat ze ervan overtuigd zijn dat ze betere beveiliging krijgen, moeten de kosten en risico's die zijn gekoppeld aan een oplossing voor aangepaste sleutel opslag worden overwogen bij het evalueren van dit model. In veel gevallen kan een organisatie bepalen dat resource beperkingen of Risico's van een on-premises oplossing groter zijn dan het risico van Cloud beheer van de versleuteling bij rust sleutels.  Dit model is echter mogelijk niet voldoende voor organisaties die vereisten hebben om het maken of de levens cyclus van de versleutelings sleutels te beheren of om te beschikken over verschillende mede werkers van de versleutelings sleutels van een service te laten beheer (dat wil zeggen, schei ding van sleutel beheer van het algehele beheer model voor de service).

### <a name="key-access"></a>Sleutel toegang

Wanneer versleuteling aan de server zijde met door service beheerde sleutels wordt gebruikt, worden de sleutel maken, opslag en toegang tot de service allemaal beheerd door de service. Normaal gesp roken worden de gegevens versleutelings sleutels in een archief opgeslagen die dicht bij de gegevens staan en snel beschikbaar en toegankelijk zijn wanneer de sleutel versleutelings sleutels worden opgeslagen in een veilige interne opslag.

**Voordelen**

- Eenvoudige installatie
- Micro soft beheert de belangrijkste draaiing, back-up en redundantie
- De klant heeft niet de kosten die zijn gekoppeld aan de implementatie of het risico van een aangepast sleutel beheer schema.

**Nadelen**

- Geen klant controle over de versleutelings sleutels (sleutel specificatie, levens cyclus, intrekken, enz.)
- Het is niet mogelijk om sleutel beheer te scheiden van het algehele beheer model voor de service

## <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Versleuteling aan de server zijde met door de klant beheerde sleutels in Azure Key Vault

Voor scenario's waarbij de vereiste is om de gegevens in rust te versleutelen en de versleutelings sleutels te beheren, kunnen klanten versleuteling aan de server zijde gebruiken met door de klant beheerde sleutels in Key Vault. Sommige services kunnen alleen de sleutel voor de sleutel van de basis sleutel opslaan in Azure Key Vault en de versleutelde gegevens versleutelings sleutel opslaan op een interne locatie dichter bij de gegevens. In dat geval kunnen klanten hun eigen sleutels naar Key Vault (Bring Your Own Key BYOK) brengen, of nieuwe codes genereren en ze gebruiken om de gewenste resources te versleutelen. Terwijl de resource provider de bewerkingen voor versleuteling en ontsleuteling uitvoert, wordt de geconfigureerde sleutel versleutelings sleutel gebruikt als basis sleutel voor alle versleutelings bewerkingen.

Verlies van sleutel versleutelings sleutels betekent gegevens verlies. Daarom mogen sleutels niet worden verwijderd. Er moet een back-up van sleutels worden gemaakt wanneer u deze maakt of roteert. [Zacht verwijderen](../../key-vault/general/soft-delete-overview.md) moet zijn ingeschakeld op een kluis die sleutel versleutelings sleutels opslaat. In plaats van een sleutel te verwijderen, stelt u ingeschakeld in op ONWAAR of stelt u de verval datum in.

### <a name="key-access"></a>Sleutel toegang

Het versleutelings model aan de server zijde met door de klant beheerde sleutels in Azure Key Vault omvat de service die toegang heeft tot de sleutels voor versleutelen en ontsleutelen, indien nodig. Versleuteling bij rest sleutels wordt via een toegangscontrole beleid toegankelijk gemaakt voor een service. Dit beleid verleent de service identiteit toegang om de sleutel te ontvangen. Een Azure-service die namens een gekoppeld abonnement wordt uitgevoerd, kan worden geconfigureerd met een identiteit in dat abonnement. De service kan Azure Active Directory verificatie uitvoeren en een verificatie token ontvangen dat zichzelf identificeert als die service namens het abonnement. Dit token kan vervolgens worden weer gegeven aan Key Vault om een sleutel te verkrijgen waaraan toegang is verleend.

Voor bewerkingen die gebruikmaken van versleutelings sleutels, kan aan een service-identiteit toegang worden verleend voor een van de volgende bewerkingen: ontsleutelen, versleutelen, sleutel uitpakken, wrapKey, verifiëren, ondertekenen, ophalen, weer geven, bijwerken, maken, importeren, verwijderen, back-up en herstellen.

Voor het verkrijgen van een sleutel voor gebruik bij het versleutelen of ontsleutelen van gegevens in rust de service-identiteit die door het service-exemplaar van de resource manager moet worden uitgevoerd sleutel uitpakken (om de sleutel voor ontsleuteling op te halen) en WrapKey (om een sleutel in te voegen in de sleutel kluis bij het maken van een nieuwe sleutel).

>[!NOTE]
>Zie de pagina uw sleutel kluis beveiligen in de [Azure Key Vault documentatie](../../key-vault/general/secure-your-key-vault.md)voor meer informatie over Key Vault autorisatie.

**Voordelen**

- Volledige controle over de gebruikte sleutels: coderings sleutels worden beheerd in de Key Vault van de klant onder het besturings element van de klant.
- Mogelijkheid om meerdere services te versleutelen op één Master
- Kan sleutel beheer scheiden van het algehele beheer model voor de service
- Kan de locatie van de service en de sleutel in verschillende regio's bepalen

**Nadelen**

- De klant heeft volledige verantwoordelijkheid voor belangrijkste toegangs beheer
- De klant heeft volledige verantwoordelijkheid voor het beheer van de levens cyclus van sleutels
- Extra installatie & configuratie overhead

## <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Versleuteling aan de server zijde met door de klant beheerde sleutels in door de klant bestuurde hardware

Met sommige Azure-Services wordt de host van uw eigen sleutel (HYOK) Key Management model ingeschakeld. Deze beheer modus is handig in scenario's waarin de gegevens in rust moeten worden versleuteld en de sleutels in een eigen opslag plaats buiten de controle van micro soft moeten worden beheerd. In dit model moet de service de sleutel ophalen van een externe site. De prestaties en de beschik baarheid van de garantie worden beïnvloed en de configuratie is ingewik kelder. Omdat de service echter toegang heeft tot de DEK tijdens de versleutelings-en ontsleutelings bewerkingen, zijn de algemene beveiligings garanties van dit model vergelijkbaar met wanneer de sleutels door de klant worden beheerd in Azure Key Vault.  Als gevolg hiervan is dit model niet geschikt voor de meeste organisaties, tenzij ze specifieke vereisten voor sleutel beheer hebben. Als gevolg van deze beperkingen bieden de meeste Azure-Services geen ondersteuning voor versleuteling aan de server zijde met behulp van door de server beheerde sleutels in door de klant bewaakte hardware.

### <a name="key-access"></a>Sleutel toegang

Wanneer versleuteling aan de server zijde met door service beheerde sleutels in door de klant beheerde hardware wordt gebruikt, worden de sleutels bewaard op een systeem dat door de klant is geconfigureerd. Azure-Services die dit model ondersteunen, bieden een manier om een beveiligde verbinding tot stand te brengen met een door de klant geleverd sleutel archief.

**Voordelen**

- Volledige controle over de gebruikte basis sleutel: coderings sleutels worden beheerd door een door de klant verschafte winkel
- Mogelijkheid om meerdere services te versleutelen op één Master
- Kan sleutel beheer scheiden van het algehele beheer model voor de service
- Kan de locatie van de service en de sleutel in verschillende regio's bepalen

**Nadelen**

- Volledige verantwoordelijkheid voor sleutel opslag, beveiliging, prestaties en beschik baarheid
- Volledige verantwoordelijkheid voor belangrijkste toegangs beheer
- Volledige verantwoordelijkheid voor het beheer van de levens cyclus van sleutels
- Aanzienlijke kosten voor installatie, configuratie en onderhouds werkzaamheden
- Toename van de beschik baarheid van het netwerk tussen het Data Center van de klant en Azure-data centers.

## <a name="supporting-services"></a>Ondersteunende services
De Azure-Services die elk versleutelings model ondersteunen:

| Product, functie of service | Server-Side Service-Managed sleutel gebruiken   | Server-Side Customer-Managed sleutel gebruiken | Client-Side Client-Managed sleutel gebruiken  |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
| **AI en Machine Learning**      |                    |                    |                    |
| Azure Cognitive Search           | Ja                | Ja                | -                  |
| Azure Cognitive Services         | Ja                | Ja                | -                  |
| Azure Machine Learning           | Ja                | Ja                | -                  |
| Azure Machine Learning Studio (klassiek) | Ja         | Preview, RSA 2048-bits | -               |
| Content Moderator                | Ja                | Ja                | -                  |
| Face                             | Ja                | Ja                | -                  |
| Taalbegrip           | Ja                | Ja                | -                  |
| Personalizer                     | Ja                | Ja                | -                  |
| QnA Maker                        | Ja                | Ja                | -                  |
| Spraakservices                  | Ja                | Ja                | -                  |
| Translator Text                  | Ja                | Ja                | -                  |
| Power BI                         | Ja                | Ja, RSA 4096-bits  | -                  |
| **Analyse**                    |                    |                    |                    |
| Azure Stream Analytics           | Ja                | Klikt\*\*            | -                  |
| Event Hubs                       | Ja                | Ja                | -                  |
| Functions                        | Ja                | Ja                | -                  |
| Azure Analysis Services          | Ja                | -                  | -                  |
| Azure Data Catalog               | Ja                | -                  | -                  |
| Azure HDInsight                  | Ja                | Alles                | -                  |
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
| Schaalset voor virtuele machines        | Ja                | Ja                | -                  |
| SAP HANA                         | Ja                | Ja                | -                  |
| App Service                      | Ja                | Klikt\*\*            | -                  |
| Automation                       | Ja                | Klikt\*\*            | -                  |
| Azure Functions                  | Ja                | Klikt\*\*            | -                  |
| Azure Portal                     | Ja                | Klikt\*\*            | -                  |
| Logic Apps                       | Ja                | Ja                | -                  |
| Door Azure beheerde toepassingen       | Ja                | Klikt\*\*            | -                  |
| Service Bus                      | Ja                | Ja                | -                  |
| Siteherstel                    | Ja                | Ja                | -                  |
| **Databases**                    |                    |                    |                    |
| SQL Server on Virtual Machines   | Ja                | Ja                | Ja                |
| Azure SQL Database               | Ja                | Ja, RSA 3072-bits  | Ja                |
| Azure SQL Database voor MariaDB   | Ja                | -                  | -                  |
| Azure SQL Database voor MySQL     | Ja                | Ja                | -                  |
| Azure SQL Database voor PostgreSQL | Ja               | Ja                | -                  |
| Azure Synapse Analytics          | Ja                | Ja, RSA 3072-bits  | -                  |
| SQL Server Stretch Database      | Ja                | Ja, RSA 3072-bits  | Ja                |
| Table Storage                    | Ja                | Ja                | Ja                |
| Azure Cosmos DB                  | Ja                | Ja                | -                  |
| Azure Databricks                 | Ja                | Ja                | -                  |
| Azure Database Migration-service | Ja                | n.v.t.\*              | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps Services            | Ja                | -                  | -                  |
| Azure-opslagplaatsen                      | Ja                | -                  | -                  |
| **Identiteit**                     |                    |                    |                    |
| Azure Active Directory           | Ja                | -                  | -                  |
| Azure Active Directory Domain Services | Ja          | Ja                | -                  |
| **Integratie**                  |                    |                    |                    |
| Service Bus                      | Ja                | Ja                | Ja                |
| Event Grid                       | Ja                | -                  | -                  |
| API Management                   | Ja                | -                  | -                  |
| **IoT-services**                 |                    |                    |                    |
| IoT Hub                          | Ja                | Ja                | Ja                |
| IoT Hub Device Provisioning      | Ja                | Ja                | -                  |
| **Beheer en governance**    |                    |                    |                    |
| Azure Site Recovery              | Ja                | -                  | -                  |
| Azure Migrate                    | Ja                | Ja                | -                  |
| **Media**                        |                    |                    |                    |
| Media Services                   | Ja                | Ja                | Ja                |
| **Beveiliging**                     |                    |                    |                    |
| Azure Security Center voor IoT    | Ja                | Ja                | -                  |
| Azure Sentinel                   | Ja                | Ja                | -                  |
| **Storage**                      |                    |                    |                    |
| Blob Storage                     | Ja                | Ja                | Ja                |
| Premium-Blob Storage             | Ja                | Ja                | Ja                |
| Disk Storage                     | Ja                | Ja                | -                  |
| Ultra Disk Storage               | Ja                | Ja                | -                  |
| Beheerde Disk Storage             | Ja                | Ja                | -                  |
| File Storage                     | Ja                | Ja                | -                  |
| Bestands Premium Storage             | Ja                | Ja                | -                  |
| File Sync                        | Ja                | Ja                | -                  |
| Queue Storage                    | Ja                | Ja                | Ja                |
| Avere vFXT                       | Ja                | -                  | -                  |
| Azure Cache voor Redis            | Ja                | n.v.t.\*              | -                  |
| Azure NetApp Files               | Ja                | Ja                | -                  |
| Archive Storage                  | Ja                | Ja                | -                  |
| StorSimple                       | Ja                | Ja                | Ja                |
| Azure Backup                     | Ja                | Ja                | Ja                |
| Data Box                         | Ja                | -                  | Ja                |
| Data Box Edge                    | Ja                | Ja                | -                  |

\* Met deze service worden gegevens niet persistent gemaakt. Tijdelijke caches, indien aanwezig, worden versleuteld met een micro soft-sleutel.

\*\* Deze service biedt ondersteuning voor het opslaan van gegevens in uw eigen Key Vault, opslag account of een andere service voor gegevens persistentie die al Server-Side versleuteling met Customer-Managed sleutel ondersteunt.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over hoe [versleuteling wordt gebruikt in azure](encryption-overview.md).
- Meer informatie over hoe Azure gebruikmaakt van [dubbele versleuteling](double-encryption.md) om bedreigingen te verhelpen die worden gebruikt voor het versleutelen van gegevens.
