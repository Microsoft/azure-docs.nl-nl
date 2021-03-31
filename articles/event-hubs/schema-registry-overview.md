---
title: Azure Schema Registry in Event Hubs (preview)
description: In dit artikel wordt een overzicht gegeven van de ondersteuning voor schemaregisters van Azure Event Hubs (preview).
ms.topic: overview
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: a876651b76aa259754623854b8fc4a7c6c8a939e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92330492"
---
# <a name="azure-schema-registry-in-event-hubs-preview"></a>Azure Schema Registry in Event Hubs (preview)
In veel scenario's voor het streamen van gebeurtenissen en voor het verzenden van berichten bevat de gebeurtenis of het bericht een nettolading d die gestructureerde gegevens bevat die worden geserialiseerd of gedeserialiseerd met behulp van een indeling die is gebaseerd op een schema, zoals Apache Avro. Zowel afzenders als ontvangers willen mogelijk de integriteit van de gegevens valideren met een schemadocument, zoals bij het JSON-schema. Voor indelingen die zijn gebaseerd op een schema is het een vereiste dat het schema voor de gebruiker van berichten beschikbaar wordt gemaakt zodat deze de gegevens kan deserialiseren. 

Het **Azure-schemaregister** is een functie van Event Hubs die een centrale opslagplaats biedt voor schemadocumenten voor apps die op gebeurtenissen zijn gebaseerd en op berichten zijn toegespitst. Het biedt de flexibiliteit voor uw producenten- en consumenten-apps om gegevens uit te wisselen zonder dat het schema onderling hoeft te worden beheerd en gedeeld. Daarnaast kunnen ze zich ook op verschillende snelheden ontwikkelen. Het schemaregister biedt ook een eenvoudig beheerframework voor herbruikbare schema's en definieert de relatie tussen schema's via een groeperingsconstructie (schemagroepen).

> [!NOTE]
> - De **schemaregister** functie is momenteel beschikbaar in een **preview-versie** en wordt niet aanbevolen voor productieworkloads.
> - De functie is alleen beschikbaar in de lagen **Standard** en **Dedicated**, maar niet in de laag **Basic**.

Met serialisatieframeworks die zijn gebaseerd op een schema, zoals Apache AVRO, kan het externaliseren van serialisatiemetagegevens in gedeelde schema's ook helpen bij het aanzienlijk reduceren van de overhead per bericht van typegegevens en veldnamen die zijn opgenomen in elke gegevensset, zoals het geval is met indelingen met tags zoals JSON. Als u schema's opslaat naast de gebeurtenissen en in de infrastructuur voor gebeurtenissen, zorgt u ervoor dat de metagegevens die nodig zijn voor serialisatie/deserialisatie altijd binnen bereik zijn en de schema's niet verkeerd kunnen worden geplaatst. 

## <a name="event-hubs-namespace"></a>Event Hubs-naamruimte
Een Event Hubs-naamruimte kan nu fungeren als host voor schemagroepen naast Event Hubs (of Kafka-onderwerpen). De naamruimte werkt als host voor een schemaregister en kan meerdere schemagroepen bevatten. Ondanks het feit dat het schemaregister wordt gehost in Azure Event Hubs, kan het universeel worden gebruikt met alle Azure-berichtenservices en alle andere berichten- of gebeurtenissenbrokers. Elk van deze schemagroepen is een afzonderlijk beveiligbare opslagplaats voor een reeks schema's. Groepen kunnen worden afgestemd op een bepaalde app of een organisatie-eenheid. 

## <a name="schema-groups"></a>Schemagroepen
Een schemagroep is een logische groep van gelijksoortige schema's op basis van uw bedrijfscriteria. Een schemagroep kan meerdere versies van een schema bevatten. U kunt er met de instelling voor het afdwingen van compatibiliteit voor een schemagroep voor zorgen dat nieuwere schemaversies achterwaarts compatibel zijn.

De beveiligingsgrens die wordt opgelegd door het groeperingsmechanisme zorgt ervoor dat handelsgeheimen niet per ongeluk via metagegevens worden gelekt in situaties waarbij de naamruimte wordt gedeeld met meerdere partners. Ook kunnen eigenaren van apps op deze manier schema's beheren, onafhankelijk van andere apps die dezelfde naamruimte delen.


## <a name="schemas"></a>Schema 's
Met schema's wordt het contract tussen de producenten en de consumenten gedefinieerd. Met een schema dat is gedefinieerd in een Event Hubs-schemaregister kan het contract worden beheerd buiten gebeurtenisgegevens, waardoor de overhead van de nettolading teniet wordt gedaan. Een schema heeft een naam, type (bijvoorbeeld record, matrix enzovoort), compatibiliteitsmodus (geen, voorwaarts, achterwaarts, volledig) en serialisatietype (momenteel alleen Avro). U kunt meerdere versies van een schema maken en een specifieke versie van een schema ophalen en gebruiken. 

## <a name="client-sdks"></a>Client-SDK 's
U kunt een van de volgende bibliotheken gebruiken die een AVRO-serialisatieprogramma bevatten, waarmee u nettoladingen met schema-id's van het schemaregister en met Avro gecodeerde gegevens kunt serialiseren en deserialiseren.

- [.NET: Microsoft.Azure.Data.SchemaRegistry.ApacheAvro](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro)
- [Java: azure-data-schemaregistry-avro](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/schemaregistry/azure-data-schemaregistry-avro/)
- [Python: azure-schemaregistry-avroserializer](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer)
- [JavaScript: @azure/schema-registry-avro](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro)
- [Apache Kafka](https://github.com/Azure/azure-schema-registry-for-kafka/): voer met Kafka geïntegreerde Apache Avro-programma's voor serialisatie en deserialisatie uit die worden ondersteund door het Azure-schemaregister. Het Apache Kafka-clientserialisatieprogramma van de Java-client voor het Azure-schemaregister kan in elk Apache Kafka-scenario en met elke Apache Kafka®-implementatie of cloudservice worden gebruikt. 

In de volgende afbeelding ziet u de informatiestroom van het schemaregister bij Event Hubs: 

:::image type="content" source="./media/schema-registry-overview/flow-diagram.png" alt-text="Stroomdiagram":::

## <a name="standard-vs-dedicated-limits"></a>Limieten voor de Standard- en Dedicated-laag
Raadpleeg [Limieten voor het schemaregister](../azure-resource-manager/management/azure-subscription-service-limits.md#schema-registry-limitations) voor limieten (bijvoorbeeld het aantal schemagroepen in een naamruimte) die hetzelfde zijn of verschillen voor de Standard- en Dedicated-laag van Event Hubs

## <a name="azure-role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer voor Azure
Wanneer u via programmacode toegang wilt verkrijgen tot het schemaregister, moet u een app registreren in Microsoft Azure Active Directory (Azure AD) en de beveiligingsprincipal van de app toevoegen aan een van de op rollen gebaseerde Azure-rollen (Azure RBAC):

| Rol | Beschrijving | 
| ---- | ----------- | 
| Eigenaar | Schemaregistergroepen en schema's lezen, schrijven en verwijderen. |
| Inzender | Schemaregistergroepen en schema's lezen, schrijven en verwijderen. |
| [Lezer van schemaregisters (preview)](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Schemaregistergroepen en schema's lezen en weergeven. |
| [Inzender van schemaregisters (preview)](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Schemaregistergroepen en schema's lezen, schrijven en verwijderen. |

Zie [Een app registreren bij Microsoft Azure Active Directory](../active-directory/develop/quickstart-register-app.md) voor instructies voor het registreren van een app met behulp van Azure Portal. Noteer de client-id (app-id), tenant-id en het geheim die in de code moeten worden gebruikt. 

## <a name="next-steps"></a>Volgende stappen

- Zie [Een Event Hubs-schemaregister maken met behulp van Azure Portal](create-schema-registry.md) voor meer informatie over het maken van een schemaregister met behulp van Azure Portal.
- Zie de volgende voorbeelden van de **Avro-clientbibliotheek voor schemaregisters**.
    - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro/tests/Samples)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/schemaregistry/azure-data-schemaregistry-avro/src/samples)
    - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro/samples )
    - [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer/samples )
    - [Kafka Avro-integratie voor het Azure-schemaregister](https://github.com/Azure/azure-schema-registry-for-kafka/tree/master/csharp/avro/samples)
