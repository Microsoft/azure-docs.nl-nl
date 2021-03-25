---
title: Gerelateerde GitHub-projecten voor Azure API voor FHIR
description: Alle open source-opslag plaatsen (GitHub) voor Azure API voor FHIR weer geven.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/01/2021
ms.author: ginle
ms.openlocfilehash: 9977765183bd567377592631d65f3e548bef4b34
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105097631"
---
# <a name="related-github-projects"></a>Gerelateerde GitHub-projecten

We hebben veel open-source projecten op GitHub die u de bron code en instructies bieden voor het implementeren van services voor verschillende toepassingen. U bent altijd welkom bij onze GitHub-opslag plaatsen voor meer informatie over en experimenteren met onze functies en producten. 

## <a name="fhir-server"></a>FHIR-server
* [micro soft/fhir-server](https://github.com/microsoft/fhir-server/): open source fhir server, die de basis vormt voor Azure API voor fhir
* Zie [release opmerkingen](https://github.com/microsoft/fhir-server/releases) voor de meest recente releases.
* [micro soft/fhir-server-voor beelden](https://github.com/microsoft/fhir-server-samples): een voorbeeld omgeving

## <a name="data-conversion--anonymization"></a>Gegevens conversie & anoniem maken

#### <a name="fhir-converter"></a>FHIR-conversie programma
* [micro soft/FHIR-Converter](https://github.com/microsoft/FHIR-Converter): een conversie programma voor het vertalen van verouderde gegevens indelingen in FHIR
* Geïntegreerd met de Azure-API voor FHIR en FHIR-server voor Azure in de vorm van $convert-gegevens bewerking
* Voortdurende verbeteringen in OSS en continue integratie met de FHIR-servers
 
#### <a name="fhir-converter---vs-code-extension"></a>FHIR-Converter-VS-code-extensie
* [micro soft/FHIR-tools-for-anoniem maken](https://github.com/microsoft/FHIR-Tools-for-Anonymization): een set hulpprogram ma's voor het helpen met gegevens (in FHIR-indeling) anoniem maken
* Geïntegreerd met de Azure-API voor FHIR en FHIR-server voor Azure in de vorm van ' de geïdentificeerde export '

#### <a name="fhir-tools-for-anonymization"></a>FHIR-Hulpprogram Ma's voor anoniem maken
* [micro soft/vscode-azurehealthcareapis-hulpprogram ma's](https://github.com/microsoft/vscode-azurehealthcareapis-tools): een VS code-extensie die een verzameling hulpprogram ma's bevat die u kunt gebruiken met Azure-gezondheids zorg-api's
* Uitgebracht voor Visual Studio Marketplace
* Gebruikt voor het ontwerpen van liquide sjablonen die moeten worden gebruikt in het FHIR-conversie programma

## <a name="iot-connector"></a>IoT-connector

#### <a name="integration-with-iot-hub-and-iot-central"></a>Integratie met IoT Hub en IoT Central
* [micro soft/iomt-fhir](https://github.com/microsoft/iomt-fhir): integratie met IoT Hub of IOT Central naar fhir met gegevens normalisatie en fhir conversie van de genormaliseerde gegevens
* Normalisatie: gegevens over de apparaatgegevens worden geëxtraheerd in een algemene indeling voor verdere verwerking
* FHIR-conversie: genormaliseerde en gegroepeerde gegevens worden toegewezen aan FHIR. Waarnemingen worden gemaakt of bijgewerkt op basis van geconfigureerde sjablonen en gekoppeld aan het apparaat en de patiënt.
* [Hulp middelen voor het bouwen van de conversatie kaart](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper): visualiseren van de toewijzings configuratie voor het normaliseren van de invoer gegevens van het apparaat en het transformeren ervan naar de FHIR-resources. Ontwikkel aars kunnen dit hulp programma gebruiken om de toewijzingen, apparaattoewijzing en FHIR-toewijzing te bewerken en te testen, en ze te exporteren voor uploaden naar de IoT-connector in de Azure Portal.

#### <a name="healthkit-and-fhir-integration"></a>Integratie van HealthKit en FHIR
* [micro soft/healthkit-on-fhir](https://github.com/microsoft/healthkit-on-fhir): een Swift-bibliotheek waarmee het exporteren van Apple Healthkit-gegevens naar een Fhir-server wordt geautomatiseerd

 