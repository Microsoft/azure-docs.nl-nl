---
title: De functie onderdelen in de Azure Certified Device Portal gebruiken
description: Een hand leiding voor het beste gebruik van de functie onderdelen van de sectie Details van apparaat om uw apparaat nauw keurig te beschrijven
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 220a6c2107063734201064115898611c20cab650
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304457"
---
# <a name="add-components-on-the-portal"></a>Onderdelen toevoegen op de portal

Bij het volt ooien van de [zelf studie voor het toevoegen van apparaatgegevens](tutorial-02-adding-device-details.md) aan het certificerings project, wordt u naar verwachting de hardwarespecificaties van uw apparaat beschrijven. Om dit te doen, kunnen gebruikers meerdere, afzonderlijke hardwareproducten ( **onderdelen** genoemd) die het apparaat vormen, markeren. Op die manier kunt u beter apparaten met extra hardware promoten en kunnen klanten het juiste product vinden door te zoeken in de catalogus op basis van deze functies.

## <a name="prerequisites"></a>Vereisten

- U moet zijn aangemeld en een project voor uw apparaat hebben gemaakt in de [Azure-portal voor gecertificeerde apparaten](https://certify.azure.com). Raadpleeg de [zelf studie](tutorial-01-creating-your-project.md)voor meer informatie.

## <a name="how-to-add-components"></a>Onderdelen toevoegen

Elk project dat voor certificering wordt verzonden, bevat een product onderdeel dat is voltooid voor de **klant** (in veel gevallen is het holistische product zelf). Bekijk onze [certificerings woordenlijst](./resources-glossary.md)voor meer informatie over het onderscheid van een product onderdeel type van de klant. Alle extra onderdelen zijn op uw keuze om uw apparaat nauw keurig vast te leggen.

1. Selecteer `Add a component` op het tabblad product details.

    ![Een onderdeel koppeling toevoegen](./media/images/add-a-component-link.png)

1. Vul relevante formulier velden voor het onderdeel in.

    ![Sectie Details van onderdeel](./media/images/component-details-section.png)

1. Sla uw gegevens op met behulp `Save Product Details` van de knop aan de onderkant van de pagina:  

    ![Knop product details opslaan](./media/images/save-product-details-button.png)

1. Zodra u uw onderdeel hebt opgeslagen, kunt u de hardware-functionaliteit die het ondersteunt, verder aanpassen. Selecteer de `Edit` koppeling met de naam van het onderdeel.  

    ![Knop onderdeel bewerken](./media/images/component-edit.png)

1. Geef waar nodig relevante informatie over de hardware-capaciteit op.  

    ![Afbeelding van Bewerk bare onderdeel secties](./media/images/component-selection-area.png)  

    De velden voor het Bewerk bare onderdeel (zie hierboven) zijn:

    - **Algemeen**: Details over hardware, zoals processors en beveiligde hardware
    - **Connectiviteit**: connectiviteits opties, protocollen en interfaces, zoals radio (s) en GPIO
    - **Accelerators**: Geef een hardwareversnelling op zoals GPU en VPU
    - **Sens oren**: Geef beschik bare Sens oren zoals GPS en trillingen op
    - **Aanvullende specificaties**: aanvullende informatie over het apparaat, zoals fysieke dimensies en opslag/accu-informatie

1. Selecteer aan `Save Product Details` de onderkant van de pagina Product Details.

## <a name="component-use-requirements-and-recommendations"></a>Vereisten en aanbevelingen voor onderdeel gebruik

Mogelijk hebt u vragen over hoeveel onderdelen u moet opnemen, of welk type onderdeel u moet gebruiken. Hieronder vindt u voor beelden van een aantal voorbeeld scenario's van apparaten die u kunt certificeren en over hoe u de functie onderdelen kunt gebruiken.

| Producttype                                       | Nee. Onderdelen | Onderdeel 1/bijlage type      | Onderdelen 2 +/bijlage type                    |
|----------------------------------------------------|------------|----------------------------------|--------------------------------------------------|
| Voltooid product                                   | 1          | Klant Ready-product, discrete | N.v.t.                                              |
| Voltooid product met **Demonteer bare rand (s)** | 2 of meer  | Klant Ready-product, discrete | Peripheral/discrete of geïntegreerd              |
| Voltooid product met **geïntegreerde onderdelen**  | 2 of meer  | Klant Ready-product, discrete | Selecteer het juiste type/discrete of geïntegreerde |
| Solution-Ready dev kit                             | 2 of meer  | Klant Ready-product, discrete | Selecteer het juiste type/discrete of geïntegreerde |

## <a name="example-component-usage"></a>Voor beeld van onderdeel gebruik

Hieronder vindt u voor beelden van hoe een OEM met de naam Contoso de functie onderdelen gebruikt voor het certificeren van hun product, Falcon.

1. Falcon is een volledig zelfstandig apparaat dat niet in een groter product kan worden geïntegreerd.
    1. Nee. van onderdelen: 1
    1. Type onderdeel apparaat: klant gereed product
    1. Bijlage type: afzonderlijk

     ![Afbeelding van product dat klaar is voor de klant](./media/images/customer-ready-product.png)

1. Falcon is een apparaat met een geïntegreerde Peripheral camera module die is vervaardigd door INC-elektronica en die via USB verbinding maakt met Falcon.
    1. Nee. van onderdelen: 2
    1. Type onderdeel apparaat: klant Ready product, Peripheral
    1. Bijlage type: discrete, geïntegreerde
    
    > [!Note]
    > Het onderdeel Peripheral wordt beschouwd als geïntegreerd omdat het niet verwisselbaar is.

     ![Afbeelding van het onderdeel voor beeld van een rand apparaat](./media/images/peripheral.png)

1. Falcon is een apparaat dat een geïntegreerd systeem bevat op module van INC-elektronica dat gebruikmaakt van een ingebouwde processor Apollo52 van Espressif van het bedrijf en een ARM64-architectuur heeft.
    1. Nee. van onderdelen: 2
    1. Type onderdeel apparaat: klant Ready product, systeem on module
    1. Bijlage type: discrete, geïntegreerde

    > [!Note]
    > Het onderdeel Peripheral wordt beschouwd als geïntegreerd omdat het niet verwisselbaar is. Het SoM-onderdeel zou ook processor informatie bevatten.

     ![Afbeelding van een voor beeld van een System in module-onderdeel ](./media/images/system-on-module.png)

## <a name="additional-tips"></a>Aanvullende tips

Hieronder vindt u meer uitleg over het gebruiks beleid voor componenten. Als u vragen hebt over het gebruik van de juiste onderdelen, neemt u contact op met ons team om te [iotcert@microsoft.com](mailto:iotcert@microsoft.com) helpen!

1. Een project mag **slechts** één product onderdeel van de klant bevatten. Als u een project met twee onafhankelijke apparaten wilt certificeren, moeten die apparaten afzonderlijk worden gecertificeerd.
1. Het is in de eerste plaats bedoeld om onderdelen (of niet gebruiken) te gebruiken om de mogelijkheden van uw apparaat voor potentiële klanten te verhogen.
1. Tijdens onze beoordeling van uw apparaat vereist het Azure-certificerings team alleen dat er ten minste één product onderdeel van de klant kan worden weer gegeven. We kunnen echter wijzigingen aanbrengen aan de onderdeel gegevens als de details niet duidelijk zijn of niet worden weer gegeven (bijvoorbeeld: de fabrikant van het onderdeel wordt niet geleverd voor een product type dat geschikt is voor klanten).

## <a name="next-steps"></a>Volgende stappen

Nu u klaar bent om onze onderdelen functie te gebruiken, bent u klaar om de details van uw apparaat te volt ooien of uw project te bewerken voor meer duidelijkheid.

- [Zelf studie: Details van apparaten toevoegen](tutorial-02-adding-device-details.md)
- [Uw gepubliceerde apparaat bewerken](how-to-edit-published-device.md)

