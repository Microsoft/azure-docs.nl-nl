---
title: 'Concepten: gegevens stroom in azure IoT connector voor FHIR-functie (preview) van Azure API voor FHIR'
description: Meer informatie over de gegevens stroom van Azure IoT connector voor FHIR (preview). Met de Azure IoT-connector voor FHIR (preview) worden gegevens opgenomen, genormaliseerd, gegroepeerd, getransformeerd en persistent gemaakt met de Azure-API voor FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: punagpal
ms.openlocfilehash: dc20d4cc54824fc65eba6669eab9a1c32d7ef9eb
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018057"
---
# <a name="azure-iot-connector-for-fhir-preview-data-flow"></a>Gegevensstroom van Azure IoT Connector for FHIR (preview)

In dit artikel vindt u een overzicht van de gegevens stroom in azure IoT connector voor snelle bronnen voor de gezondheids zorg (FHIR&#174;) *. Meer informatie over de verschillende fasen voor gegevens verwerking binnen Azure IoT connector voor FHIR waarmee apparaatgegevens worden omgezet in op FHIR gebaseerde [observatie](https://www.hl7.org/fhir/observation.html) resources.

![Gegevensstroom van Azure IoT Connector for FHIR](media/concepts-iot-data-flow/iot-connector-data-flow.png)

In het bovenstaande diagram ziet u algemene gegevens stromen met behulp van Azure IoT connector voor FHIR. 

Hieronder ziet u verschillende fasen die de gegevens gaan passeren zodra deze zijn ontvangen door Azure IoT connector voor FHIR.

## <a name="ingest"></a>Opnemen
Opname is de eerste fase waarin apparaatgegevens worden ontvangen in azure IoT connector voor FHIR. Het opname-eind punt voor apparaatgegevens wordt gehost op een [Azure Event hub](../../event-hubs/index.yml). Het Azure Event hub-platform ondersteunt hoge schaal en door Voer met de mogelijkheid om miljoenen berichten per seconde te ontvangen en verwerken. Daarnaast kan Azure IoT connector voor FHIR berichten asynchroon gebruiken, waardoor apparaten niet hoeven te worden gewacht wanneer de gegevens van het apparaat worden verwerkt.

> [!NOTE]
> JSON is op dit moment de enige indeling die wordt ondersteund voor apparaatgegevens.

## <a name="normalize"></a>Normaliseren
Normaliseren is de volgende fase waarin apparaatgegevens worden opgehaald uit de bovenstaande Azure Event hub en worden verwerkt met toewijzings sjablonen voor apparaten. Dit toewijzings proces resulteert in het transformeren van apparaatgegevens in een genormaliseerd schema. 

Het normalisatie proces vereenvoudigt gegevens verwerking in latere fasen, maar biedt ook de mogelijkheid om één invoer bericht te projecteren in meerdere genormaliseerde berichten. Een apparaat kan bijvoorbeeld meerdere vitale tekens voor de lichaams temperatuur, het Pulse-Rate, de bloed druk en het respiration-tempo per bericht verzenden. Dit invoer bericht zou vier afzonderlijke FHIR-resources maken. Elke resource zou een ander vitale teken vertegenwoordigen, waarbij het invoer bericht is geprojecteerd in vier verschillende genormaliseerde berichten.

## <a name="group"></a>Groep
Groep is de volgende fase waarin de genormaliseerde berichten die beschikbaar zijn in de vorige fase worden gegroepeerd met drie verschillende para meters: apparaat-id, metings type en tijds periode.

De groepering van de apparaat-id en het meet type maken gebruik van het meting type [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) . Dit type biedt een beknopte manier om een op tijd gebaseerde reeks metingen van een apparaat in FHIR weer te geven. En de tijd periode bepaalt de latentie waarmee waarnemingen die zijn gegenereerd door de Azure IoT-connector voor FHIR, worden geschreven naar Azure API voor FHIR.

> [!NOTE]
> De waarde van de tijds periode wordt standaard ingesteld op 15 minuten en kan niet worden geconfigureerd voor de preview-versie.

## <a name="transform"></a>Transformeren
In het transformatie stadium worden gegroepeerde genormaliseerde berichten verwerkt via FHIR-toewijzings sjablonen. Berichten die overeenkomen met een sjabloon type worden getransformeerd in FHIR-waarnemingen die zijn opgegeven via de toewijzing.

Op dit moment wordt de bron van het [apparaat](https://www.hl7.org/fhir/device.html) , samen met de bijbehorende [patiënt](https://www.hl7.org/fhir/patient.html) resource, ook opgehaald van de FHIR-server met behulp van de apparaat-id die aanwezig is in het bericht. Deze resources worden toegevoegd als een verwijzing naar de observatie resource die wordt gemaakt.

> [!NOTE]
> Alle identiteits kijken ups worden opgeslagen in de cache zodra deze zijn opgelost om de belasting op de FHIR-server te verlagen. Als u van plan bent apparaten met meerdere patiënten te gebruiken, wordt u geadviseerd om een bron van een virtueel apparaat te maken die specifiek is voor de patiënt en de id van het virtuele apparaat in de bericht lading te verzenden. Het virtuele apparaat kan worden gekoppeld aan de werkelijke apparaatnaam als bovenliggend item.

Als er geen apparaatnaam voor een bepaalde apparaat-id in de FHIR-server aanwezig is, is de uitkomst afhankelijk van de waarde die is `Resolution Type` ingesteld op het moment dat deze wordt gemaakt. Als deze instelling `Lookup` is ingesteld op, wordt het specifieke bericht genegeerd en blijven andere inkomende berichten door de pijp lijn worden verwerkt. Als deze is ingesteld op `Create` , maakt Azure IOT connector voor FHIR een bare bones-apparaat en patiënten-resources op de FHIR-server.  

## <a name="persist"></a>Behouden
Zodra de FHIR-resource wordt gegenereerd in het transformatie stadium, wordt de resource opgeslagen in de Azure-API voor FHIR. Als de FHIR-resource nieuw is, wordt deze op de server gemaakt. Als de FHIR-resource al bestaat, wordt deze bijgewerkt.

## <a name="next-steps"></a>Volgende stappen

Klik hieronder op de volgende stap voor meer informatie over het maken van FHIR.

>[!div class="nextstepaction"]
>[Toewijzingssjablonen in Azure IoT Connector for FHIR](iot-mapping-templates.md)

*In Azure Portal wordt Azure IoT Connector for FHIR aangeduid als IoT Connector (preview). FHIR is een gedeponeerd handelsmerk van HL7 en wordt gebruikt met de toestemming van HL7. 