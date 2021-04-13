---
title: Verklarende woorden lijst voor het programma Azure Certified Device
description: Een lijst met algemene termen die worden gebruikt in het Azure Certified Device-programma
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: conceptual
ms.date: 03/03/2021
ms.custom: template-concept
ms.openlocfilehash: f58c29c6bd2c22f37d2285ac6e93c6f54e47c4e0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305086"
---
# <a name="azure-certified-device-program-glossary"></a>Verklarende woorden lijst voor het programma Azure Certified Device

Deze hand leiding bevat definities van termen die vaak worden gebruikt in het Azure Certified Device-programma en de portal. Raadpleeg deze verklarende woorden lijst voor uitleg bij het certificerings proces. Voor uw gemak wordt deze verklarende woorden lijst gecategoriseerd op basis van belang rijke concepten voor certificering waarover u mogelijk vragen hebt.

## <a name="device-class"></a>Apparaatklasse

Wanneer u een certificerings project maakt, wordt u gevraagd een apparaatklasse op te geven. Apparaatklasse verwijst naar de vorm factor of classificatie die het meest geschikt is voor uw apparaat.

- **Gateway**

    Een apparaat dat gegevens verwerkt die via een IoT-netwerk worden verzonden.

- **Sensoren**

    Een apparaat dat wijzigingen in een omgeving detecteert en beantwoordt en verbinding maakt met gateways om de wijzigingen te verwerken.

- **Overige**

    Als u overige selecteert, voegt u een beschrijving van uw apparaatklasse toe in uw eigen woorden. Na verloop van tijd kunnen we nieuwe waarden blijven toevoegen aan deze lijst, met name omdat we de feedback van onze partners blijven bewaken.

## <a name="device-type"></a>Apparaattype

U wordt ook gevraagd om een van de twee apparaattypen te selecteren tijdens het certificerings proces.

- **Voltooid product**

    Een apparaat met een oplossing die gereed is voor productie-implementatie. Normaal gesp roken in een voltooide vorm factor met firmware en een besturings systeem. Dit kunnen algemene apparaten zijn die extra aanpassingen of gespecialiseerde apparaten vereisen waarvoor geen wijzigingen nodig zijn voor gebruik.
- **Oplossing-gereed dev kit**

    Een Development Kit met hardware en software die ideaal is voor het eenvoudig maken van prototypen, meestal niet in een voltooide vorm factor. Bevat meestal voorbeeld code en zelf studies om snelle prototypen in te scha kelen.

## <a name="component-type"></a>Onderdeeltype

In de sectie apparaatgegevens gaat u uw apparaat beschrijven door onderdelen op onderdeel type op te geven. U kunt [hier](./how-to-using-the-components-feature.md)meer hulp voor onderdelen weer geven.

- **Product dat klaar is voor de klant**

    Een onderdeel weergave van het hele of primaire apparaat. Dit wijkt af van een **voltooid product**. Dit is een classificatie van het apparaat, dat klaar is voor gebruik door de klant zonder verdere ontwikkeling. Een voltooid product bevat een product onderdeel dat klaar is voor de klant.
- **Ontwikkel bord**

    Een geïntegreerde of afneem bare kaart met de micro processor voor eenvoudige aanpassing.
- **Rand apparatuur**

    Een geïntegreerde of afneem bare toevoeging aan het product (zoals een accessoire). Dit zijn doorgaans apparaten die verbinding maken met het hoofd apparaat, maar die geen bijdrage levert aan de primaire functies van het apparaat. In plaats daarvan beschikt u over extra functies. Geheugen, RAM, opslag, harde schijven en Cpu's worden niet beschouwd als rand apparaten (deze worden in plaats daarvan vermeld onder aanvullende specificaties van het product onderdeel van de klant).
- **Systeem-on-module**  

    Een circuit op kaart niveau waarmee een systeem functie in één module wordt geïntegreerd.

## <a name="component-attachment-method"></a>Component Attachment-methode

Een component Attachment-methode is een andere component details die de klant informeert over de manier waarop het onderdeel is geïntegreerd in het hele product.

- **Geïntegreerd**
 
    Verwijst naar wanneer een apparaat onderdeel onderdeel is van het hoofd chassis van het product. Dit verwijst meestal naar een type Peripheral Component dat niet van het apparaat kan worden verwijderd.  
    Voor beeld: een geïntegreerde temperatuur sensor in een gateway-chassis.

- **Afzonderlijk**

    Verwijst naar wanneer een onderdeel **geen** deel uitmaakt van het hoofd chassis van het product.  
    Voor beeld: een externe temperatuur sensor die op het apparaat moet worden aangesloten.


## <a name="next-steps"></a>Volgende stappen

Deze verklarende woorden lijst leidt u door het proces van het certificeren van uw project op de portal. U bent nu klaar om te beginnen met het project.
- [Zelf studie: uw project maken](./tutorial-01-creating-your-project.md)
