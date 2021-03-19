---
title: Versleuteling van de rest in Azure Cosmos DB
description: Meer informatie over het Azure Cosmos DB versleutelen van gegevens in rust en hoe deze worden geïmplementeerd.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: seodec18
ms.openlocfilehash: abe4bb3a53e3177857cb8041fd255ec916985054
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93089862"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Gegevensversleuteling in Azure Cosmos DB 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Versleuteling op rest is een zin die doorgaans verwijst naar de versleuteling van gegevens op niet-vluchtige opslag apparaten, zoals Solid-state drives (Ssd's) en harde schijven (Hdd's). Cosmos DB zijn de primaire data bases opgeslagen op Ssd's. De media bijlagen en back-ups worden opgeslagen in Azure Blob-opslag, waarvan een back-up wordt gemaakt door Hdd's. Met de release van versleuteling in rust voor Cosmos DB, worden al uw data bases, media bijlagen en back-ups versleuteld. Uw gegevens zijn nu versleuteld in door Voer (via het netwerk) en op rest (niet-vluchtige opslag), waardoor u end-to-end-versleuteling krijgt.

Als PaaS-service is Azure Cosmos DB heel eenvoudig te gebruiken. Omdat alle gebruikers gegevens die zijn opgeslagen in Azure Cosmos DB, zijn versleuteld op rest en in Trans Port, hoeft u geen actie te ondernemen. Een andere manier om dit te doen is dat versleuteling op rest standaard is. Er zijn geen besturings elementen om deze in of uit te scha kelen. Azure Cosmos DB maakt gebruik van AES-256-versleuteling voor alle regio's waar het account wordt uitgevoerd. We bieden deze functie terwijl we kunnen voldoen aan onze [Beschik baarheid en prestaties](https://azure.microsoft.com/support/legal/sla/cosmos-db). Gegevens die zijn opgeslagen in uw Azure Cosmos-account, worden automatisch en naadloos versleuteld met sleutels die worden beheerd door micro soft (door service beheerde sleutels). U kunt desgewenst een tweede laag versleuteling toevoegen met uw eigen sleutels zoals beschreven in het artikel door de [klant beheerde sleutels](how-to-setup-cmk.md) .

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Implementatie van versleuteling in rust voor Azure Cosmos DB

Versleuteling op rest wordt geïmplementeerd met behulp van een aantal beveiligings technologieën, waaronder systemen voor beveiligde sleutel opslag, versleutelde netwerken en cryptografische Api's. Systemen die gegevens ontsleutelen en verwerken, moeten communiceren met systemen die sleutels beheren. In het diagram ziet u hoe de opslag van versleutelde gegevens en het beheer van sleutels wordt gescheiden. 

:::image type="content" source="./media/database-encryption-at-rest/design-diagram.png" alt-text="Diagram ontwerpen" border="false":::

De basis stroom van een gebruikers aanvraag is als volgt:
- Het account van de gebruikers database wordt gereed gemaakt en opslag sleutels worden opgehaald via een aanvraag aan de provider van de Management service-resource.
- Een gebruiker maakt een verbinding met Cosmos DB via HTTPS/beveiligd Trans Port. (De Sdk's abstracten de details.)
- De gebruiker verzendt een JSON-document dat wordt opgeslagen via de eerder gemaakte beveiligde verbinding.
- Het JSON-document wordt geïndexeerd tenzij de gebruiker de indexering heeft uitgeschakeld.
- Zowel het JSON-document als de index gegevens worden naar beveiligde opslag geschreven.
- Periodiek worden gegevens uit de beveiligde opslag gelezen en wordt er een back-up gemaakt naar het versleutelde BLOB-archief van Azure.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>V: Hoeveel kost meer Azure Storage kosten als Storage Service Encryption is ingeschakeld?
A: er zijn geen extra kosten.

### <a name="q-who-manages-the-encryption-keys"></a>V: wie beheert de versleutelings sleutels?
A: de sleutels worden beheerd door micro soft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>V: hoe vaak versleutelings sleutels zijn gedraaid?
A: micro soft heeft een aantal interne richt lijnen voor het draaien van de versleutelings sleutel, die Cosmos DB volgt. De specifieke richt lijnen worden niet gepubliceerd. Micro soft publiceert de [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx), die wordt beschouwd als een subset van interne richt lijnen en heeft nuttige aanbevolen procedures voor ontwikkel aars.

### <a name="q-can-i-use-my-own-encryption-keys"></a>V: kan ik mijn eigen versleutelings sleutels gebruiken?
A: Ja, deze functie is nu beschikbaar voor nieuwe Azure Cosmos DB accounts en dit moet worden gedaan op het moment dat het account wordt gemaakt. Ga naar door de [klant beheerde sleutels](./how-to-setup-cmk.md) document voor meer informatie.

### <a name="q-what-regions-have-encryption-turned-on"></a>V: voor welke regio's is versleuteling ingeschakeld?
A: voor alle Azure Cosmos DB regio's is versleuteling ingeschakeld voor alle gebruikers gegevens.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>V: heeft versleuteling invloed op de prestatie latentie en door Voer-Sla's?
A: er is geen invloed op of wijzigingen in de functie voor het uitvoeren van prestaties, nu de versleuteling bij rest is ingeschakeld voor alle bestaande en nieuwe accounts. Meer informatie vindt u op de pagina [Sla voor Cosmos DB voor](https://azure.microsoft.com/support/legal/sla/cosmos-db) de meest recente garanties.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>V: de lokale emulator ondersteunt versleuteling op rest?
A: de emulator is een zelfstandig hulp programma voor ontwikkelen en testen en maakt geen gebruik van de services voor sleutel beheer die door de beheerde Cosmos DB-service worden gebruikt. Onze aanbeveling is om BitLocker in te scha kelen op stations waar u gevoelige emulator test gegevens opslaat. De [emulator ondersteunt het wijzigen van de standaardmap voor gegevens](local-emulator.md) en het gebruik van een bekende locatie.

## <a name="next-steps"></a>Volgende stappen

* U kunt ervoor kiezen om een tweede laag versleuteling toe te voegen met uw eigen sleutels voor meer informatie, raadpleegt u het artikel door de [klant beheerde sleutels](how-to-setup-cmk.md) .
* Zie [Azure Cosmos data base Security](database-security.md)(Engelstalig) voor een overzicht van Cosmos DB beveiliging en de nieuwste verbeteringen.
* Zie de [Vertrouwenscentrum van Azure](https://azure.microsoft.com/support/trust-center/)voor meer informatie over micro soft-certificeringen.