---
title: Benoemde entiteiten voor gezondheids zorg
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 805c726d33f2050f6f2797c0689069aa5ec4ee71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599299"
---
[Text Analytics voor status](../../how-tos/text-analytics-for-health.md) processen en extraheer inzichten uit ongestructureerde medische gegevens. Met de service worden medische concepten gedetecteerd en geoppereerd, worden bevestigingen aan concepten toegewezen, worden er semantische relaties tussen concepten afgedaan en worden ze gekoppeld aan algemene medische Ontologies.

In Text Analytics status worden medische concepten in de volgende categorieën gedetecteerd. Alleen Engelse tekst wordt ondersteund in deze preview en er is slechts één model versie beschikbaar.

| Categorie  | Beschrijving  |
|---------|---------|
| [STRUCTUUR](#anatomy) | concepten die informatie vastleggen over hoofd-en Anatomic-systemen,-sites,-locaties of-regio's. |
 | [DEMOGRAFISCHE gegevens](#demographics) | concepten die informatie over geslacht en leeftijd vastleggen. |
 | [BESTUDEREN](#examinations) | concepten die informatie vastleggen over diagnostische procedures en tests. |
 | [ALGEMENE KENMERKEN](#general-attributes) | concepten die meer informatie geven over andere concepten uit de bovenstaande categorieën. |
 | [GENOMICS](#genomics) | concepten die informatie over genen en varianten vastleggen. |
 | [GEZONDHEIDS zorg](#healthcare) | concepten die informatie vastleggen over beheer gebeurtenissen, careische omgevingen en beroeps beoefenaars in de gezondheids zorg. |
 | [MEDISCHE VOOR WAARDE](#medical-condition) | concepten die informatie vastleggen over diagnoses, symptomen of symptomen. |
 | [GENEES](#medication) | concepten voor het vastleggen van informatie over medicijnen, waaronder medicijnen namen, klassen, dosering en route van beheer. |
 | [BURGER](#social) | concepten die informatie vastleggen over medische relevante sociale aspecten, zoals familie relatie. |
 | [GERICHT](#treatment) | concepten die informatie over therapeutische procedures vastleggen. |

Hieronder vindt u meer informatie en voor beelden.

## <a name="anatomy"></a>Structuur

### <a name="entities"></a>Entiteiten

**BODY_STRUCTURE** -hoofd systemen, Anatomic locaties of regio's en hoofd sites. Bijvoorbeeld arm, knie, abdomen, neus, lever, hoofd, ademhalings systeem, lymphocytes.

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="Een voor beeld van de entiteit body-structuur.":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="Een uitgebreid voor beeld van de entiteit body-structuur.":::

## <a name="demographics"></a>Demografische gegevens

### <a name="entities"></a>Entiteiten

**Leeftijd** : alle leeftijds voorwaarden en zinsdelen, met inbegrip van patiënten, gezins leden en anderen. Bijvoorbeeld 40-jaar-oud, 51 yo, 3 maanden oud, volwassene, zuigelingen, oude leeftijd, Young, secundair, Midden-verouderd.

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="Een voor beeld van een leeftijds entiteit.":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="Een ander voor beeld van een leeftijds entiteit.":::


**Gender** -termen die het geslacht van het onderwerp vermelden. Bijvoorbeeld mannelijk, vrouwelijk, vrouw, zachtman, vrouw.

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="Een voor beeld van een gender-entiteit.":::

## <a name="examinations"></a>Onderzoek

### <a name="entities"></a>Entiteiten

**EXAMINATION_NAME** : diagnostische procedures en tests, met inbegrip van vitale tekens en lichaams metingen. Bijvoorbeeld, MRI, ECG, HIV test, HEMOGLOBIN, aantal Trombocyten, schaal systemen zoals Bristol van de *stoel schalen*.

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="Een voor beeld van een examen-entiteit.":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="Een ander voor beeld van een naam entiteit voor onderzoek.":::

## <a name="general-attributes"></a>Algemene kenmerken

### <a name="entities"></a>Entiteiten

**Datum** : volledige datum voor een medische situatie, onderzoek, behandeling, medicijnen of administratieve gebeurtenis.

**Richting** : richtings termen die mogelijk betrekking hebben op een carrosserie, een medische voor waarde, een onderzoek of behandeling, zoals: Left, zijdelings, Upper, posterior.

**Frequentie** -geeft aan hoe vaak een medische voor waarde, een onderzoek, een behandeling of medicijnen heeft plaatsgevonden, zich voordoet of zich moet voordoen.

**MEASUREMENT_VALUE** : de waarde met betrekking tot een onderzoek of een medische voor waarden meting.

**MEASUREMENT_UNIT** : de maat eenheid die is gerelateerd aan een onderzoek of een medische voor waarden meting.

**RELATIONAL_OPERATOR** -zinnen waarmee de kwantitatieve relatie tussen een entiteit en enige aanvullende informatie wordt uitgedrukt.

**Tijdgebonden voor** waarden met betrekking tot het begin en/of de lengte (duur) van een medische toestand, onderzoek, behandeling, medicijnen of administratieve gebeurtenis. 

## <a name="genomics"></a>Genomics

### <a name="entities"></a>Entiteiten

**GENE_OR_PROTEIN** : alle vermeldingen van namen en symbolen van menselijke genen, alsmede chromosoom en delen van chromosomen en eiwitten. Bijvoorbeeld, MTRR, F2.

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="Een voor beeld van een gen-entiteit.":::

**Variant** : alle vermeldingen van gen en mutaties van de voors Republiek. Bijvoorbeeld `c.524C>T` , `(MTRR):r.1462_1557del96`
  
## <a name="healthcare"></a>Gezondheidszorg

### <a name="entities"></a>Entiteiten
  
**ADMINISTRATIVE_EVENT** : gebeurtenissen die betrekking hebben op het gezondheids systeem, maar van een administratieve/semi-administratieve aard. Bijvoorbeeld registratie, toelating, proef versie, studie vermelding, overdracht, kwijting, hospitalization, ziekenhuis verblijf. 

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="Een voor beeld van een gezondheids gebeurtenis entiteit.":::

**CARE_ENVIRONMENT** : een omgeving of locatie waar patiënten zorg voor zijn. Bijvoorbeeld: Emergency Room, kantoor van artsen, cardio unit, Hospice, zieken huis.

:::image type="content" source="../../media/ta-for-health/healthcare-environment-entity.png" alt-text="Deze scherm afbeelding toont een voor beeld van een gezondheids omgeving.":::

**HEALTHCARE_PROFESSION** : een gezondheids zorg waarbij een licentie wordt verleend of waarvoor de licentie niet is toegestaan. Bijvoorbeeld tand arts, pathologist, neurologist, radiologist, pharmacist, voeding, fysieke Therapist, chiropractor.

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity.png" alt-text="Deze scherm afbeelding toont een ander voor beeld van een gezondheids omgeving.":::

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="Een ander voor beeld van een gezondheids omgeving.":::

## <a name="medical-condition"></a>Medische voor waarde

### <a name="entities"></a>Entiteiten

**Diagnose** : ziekte, Syndrome, poisoning. Bijvoorbeeld, Alzheimer, HTN, CHF, de snelheid van het ruggen merg.

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="Een voor beeld van een entiteit met medische voor waarden.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-entity-2.png" alt-text="Een ander voor beeld van een entiteit voor medische voor waarden.":::

**SYMPTOM_OR_SIGN** : onderhevige of objectieve bewijzen van ziekte of andere onderzoeken. Een voor beeld: een borst pijn, pijn, dizziness, Rash, SOB, abdomen was zacht, goede bowel sounds en goed nourished.

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity.png" alt-text="Een voor beeld van een teken-of symptoom entiteit van de medische voor waarde.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity-2.png" alt-text="Een ander voor beeld van een teken of symptoom entiteit van de medische voor waarde.":::

**CONDITION_QUALIFIER** -kwalitatieve voor waarden die worden gebruikt om een medische voor waarde te beschrijven. Alle volgende subcategorieën worden beschouwd als kwalificaties:

1.  Time-gerelateerde expressies: Dit zijn termen die de tijd dimensie kwalitatief beschrijven, zoals plotseling, accent aigu, chronische, spant. 
2.  Kwaliteits expressies: Dit zijn termen die de aard van de medische voor waarde beschrijven, zoals verbranding, scherp.
3.  Ernst expressies: ernstig, mild, een beetje, niet-beheerd.
4.  Extensivity expressies: Local, brand, diffuus.
5.  Stralings expressies: elektromagnetische, straling.
6.  Schaal van voor waarde: in sommige gevallen wordt een voor waarde gekenmerkt door een schaal, wat een eindige, geordende lijst met waarden is. Bijvoorbeeld patiënten met fase III pancreatic kanker.
7.  Voorwaarde cursus: een term die betrekking heeft op de cursus of de voortgang van een voor waarde, zoals verbetering, verslechtering, oplossing, kwijt schel ding. 

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis.png" alt-text="Een voor beeld van een voorwaarde kwalificatie kenmerk en een controle-entiteit.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-2.png" alt-text="Een ander voor beeld van een voorwaarde kwalificatie kenmerk en een controle-entiteit.":::

:::image type="content" source="../../media/ta-for-health/conditional-qualifier-symptom-medication.png" alt-text="Een voor beeld van een voorwaarde kwalificatie kenmerk met symptoom-en medicijnen-entiteiten.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="Deze scherm afbeelding toont een ander voor beeld van een voorwaarde kwalificatie kenmerk met een controle-entiteit.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-symptom.png" alt-text="Deze scherm afbeelding toont een extra voor beeld van een voorwaarde kwalificatie kenmerk met een controle-entiteit.":::

## <a name="medication"></a>Genees

### <a name="entities"></a>Entiteiten

**MEDICATION_CLASS** : een reeks medicijnen met een soortgelijk actie mechanisme, een gerelateerde werk wijze, een vergelijk bare chemische structuur en/of worden gebruikt om dezelfde ziekte te behandelen. Bijvoorbeeld ACE-remmen, Opioid, antibiotica, pijn ontlasters.

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="Een voor beeld van een medicijnen klasse-entiteit.":::

**MEDICATION_NAME** : medicijn vermeldingen, waaronder merk namen van auteurs rechten en niet-merk namen. Bijvoorbeeld ibuprofen.

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="Een voor beeld van een medicijnen naam entiteit.":::

**Dosering** : de hoeveelheid medicijnen die is besteld. Bijvoorbeeld: natriumhydroxide natrium chloride-oplossing *1000 ml*.

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="Een voor beeld van een medicijnen doserings kenmerk.":::

**MEDICATION_FORM** : de vorm van de medicijnen. Bijvoorbeeld oplossing, Pill, capsule, Tablet, patch, gel, paste, schuim, spuit, drup pels, room, stroop.

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="Een voor beeld van een medicijnen formulier kenmerk.":::

**MEDICATION_ROUTE** : de administratieve methode van medicijnen. Bijvoorbeeld, oraal, Vaginal, IV, epidural, onderwerpf, inademing.

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="Een voor beeld van een medicijn route-kenmerk.":::

## <a name="social"></a>Sociaal netwerken

### <a name="entities"></a>Entiteiten

**FAMILY_RELATION** – vermeldingen van familie leden van het onderwerp. Bijvoorbeeld: vader, dochter, item op hetzelfde niveau, ouders.

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="Scherm afbeelding toont een ander voor beeld van een kenmerk voor een verwerkings tijd.":::

## <a name="treatment"></a>Gericht

### <a name="entities"></a>Entiteiten

**TREATMENT_NAME** : therapeutische procedures. Bijvoorbeeld, chirurgische, beenmerg Transplant, TAVI, dieet.

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="Een voor beeld van een entiteit met een behandelings naam.":::
