---
title: Identificatie-entiteiten
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: a376b050d79709885e3542d330bb6b1eea48d046
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750645"
---
### <a name="financial-account-identification"></a>Id van financiële rekening

Deze entiteits categorie bevat financiële gegevens en officiële vormen van identificatie.

#### <a name="category-aba-routing-number"></a>Categorie: ABA-route nummer

Deze categorie bevat de volgende entiteit:

:::row:::
    :::column span="":::
        **Entiteit**

        ABA-route nummer

    :::column-end:::
    :::column span="2":::
        **Details**

        Transit routerings nummers van de Amerikaanse Bank (ABA).
      
    :::column-end:::
:::row-end:::

#### <a name="category-swift-code"></a>Categorie: SWIFT-code

Deze categorie bevat de volgende entiteit:

:::row:::
    :::column span="":::
        **Entiteit**

        SWIFT-code

    :::column-end:::
    :::column span="2":::
        **Details**

        SWIFT-codes voor informatie over de betalings instructie.
      
    :::column-end:::
:::row-end:::

#### <a name="category-credit-card"></a>Categorie: Credit Card

Deze categorie bevat de volgende entiteit:

:::row:::
    :::column span="":::
        **Entiteit**

        Creditcard

    :::column-end:::
    :::column span="2":::
        **Details**

        Creditcard nummers. 
      
    :::column-end:::
:::row-end:::

#### <a name="category-international-banking-account-number-iban"></a>Categorie: internationaal bankieren-account nummer (IBAN) 

Deze categorie bevat de volgende entiteit:

:::row:::
    :::column span="":::
        **Entiteit**

        Creditcard

    :::column-end:::
    :::column span="2":::
        **Details**

        IBAN-codes voor informatie over de betalings instructie.
      
    :::column-end:::
:::row-end:::

### <a name="government-and-countryregion-specific-identification"></a>Sofi en land/regio-specifieke identificatie

> [!NOTE]
> De volgende financiële en land afhankelijke entiteiten worden niet geretourneerd met de `domain=phi` para meter:
> * Paspoort nummers
> * BTW-Id's

De volgende entiteiten worden gegroepeerd en weer gegeven op land:

#### <a name="argentina"></a>Argentinië

:::row:::
    :::column span="":::
        **Entiteit**

        Sofi-nummer (Argentinië National Identity)

    :::column-end:::
:::row-end:::


#### <a name="austria"></a>Oostenrijk

:::row:::
    :::column span="":::
        **Entiteit**

        Identiteits kaart Oosten rijk

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Identificatie nummer voor Oosten Rijks tarief

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        BTW-nummer voor de toegevoegde waarde van de Oosten rijk

    :::column-end:::
:::row-end:::



#### <a name="australia"></a>Australië

:::row:::
    :::column span="":::
        **Entiteit**

        Nummer van Australië-Bank account

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Australisch bedrijfs nummer

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Bedrijfs nummer Australië

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Licentie van het Australia-stuur programma  

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Medisch account nummer in Australië

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Australië-paspoort nummer

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Australië-paspoort nummer

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Australia-BTW-bestands nummer

    :::column-end:::

:::row-end:::


#### <a name="belgium"></a>België

:::row:::
    :::column span="":::
        **Entiteit**

        België nationaal nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Het BTW-nummer van de Belgische toegevoegde waarde

    :::column-end:::

:::row-end:::


#### <a name="brazil"></a>Brazilië 

:::row:::
    :::column span="":::
        **Entiteit**

        Nummer van juridische entiteit in Brazilië (CNPJ)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Brazilië CPF-nummer

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        National ID-kaart (RG) Brazilië

    :::column-end:::
:::row-end:::

#### <a name="canada"></a>Canada

:::row:::
    :::column span="":::
        **Entiteit**

        Rekening nummer Canada

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Rijbewijs nummer van het Canada-stuur programma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Canada-Health-Service nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Canada paspoort nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Privé-identificatie nummer voor Canada (PHIN)

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Sofi-nummer Canada

    :::column-end:::
:::row-end:::

#### <a name="chile"></a>Chili 

:::row:::
    :::column span="":::
        **Entiteit**

        ID-kaart nummer Chili

    :::column-end:::
:::row-end:::

#### <a name="china"></a>China

:::row:::
    :::column span="":::
        **Entiteit**

        China-nummer van de residente identiteits kaart (China)

    :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>Europese Unie (EU)

:::row:::
    :::column span="":::
        **Entiteit**

        Nummer ICL-betaalpas

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Licentie nummer van het EU-stuur programma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sofi-nummer van de EU

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU-paspoort nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sofi-nummer van de EU of gelijkwaardige ID

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        BTW-identificatie nummer van de EU

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU GPS-coördinaten

    :::column-end:::
:::row-end:::

#### <a name="france"></a>Frankrijk

:::row:::
    :::column span="":::
        **Entiteit**

        Rijbewijs nummer van Frank rijk stuur programma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Frank rijk-medische-verzekerings nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Landse nationale ID-kaart (CNI)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Frank rijk paspoort nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sofi-nummer Frank rijk (INSEE)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Frank rijk-belasting identificatienummer (numéro SPI)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        BTW-nummer van toegevoegde waarde voor de Frank rijk

    :::column-end:::
:::row-end:::

#### <a name="germany"></a>Duitsland

:::row:::
    :::column span="":::
        **Entiteit**

        Rijbewijs nummer van het Duitse stuur programma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Nummer van de Duitsland-identiteits kaart

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Duitsland-paspoort nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Nummer van de Duitse belasting-ID

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Omzetbelasting nummer van toegevoegde Duitse waarde

    :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>Hongkong

:::row:::
    :::column span="":::
        **Entiteit**

        HKID-nummer (Hong Kong Identity Card)

    :::column-end:::
:::row-end:::

#### <a name="hungary"></a>Hongarije

:::row:::
    :::column span="":::
        **Entiteit**

        Persoonlijk identificatie nummer Hongarije

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        BTW-identificatie nummer Hongarije

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Omzetbelasting nummer toegevoegde waarde

    :::column-end:::
:::row-end:::

#### <a name="india"></a>India

:::row:::
    :::column span="":::
        **Entiteit**

        Permanent account nummer India (PAN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Aadhaar-nummer (India-unieke identificatie)

    :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>Indonesië

:::row:::
    :::column span="":::
        **Entiteit**

        KTP-nummer (Indonesië-identiteits kaart)

    :::column-end:::
:::row-end:::

#### <a name="ireland"></a>Ierland

:::row:::
    :::column span="":::
        **Entiteit**

        Het PPS-nummer (Personal public service) in Ierland

    :::column-end:::
:::row-end:::

#### <a name="israel"></a>Israël

:::row:::
    :::column span="":::
        **Entiteit**

        Nationale ID Israël

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Israël-bankrekening nummer

    :::column-end:::
:::row-end:::

#### <a name="italy"></a>Italië

:::row:::
    :::column span="":::
        **Entiteit**

        Licentie-ID van het Italië-stuur programma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Italië-fiscale code

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Omzetbelasting nummer voor toegevoegde waarde van Italië

    :::column-end:::
:::row-end:::


#### <a name="japan"></a>Japan

:::row:::
    :::column span="":::
        **Entiteit**

        Japans bankrekening nummer

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Rijbewijs nummer van het Japanse stuur programma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japan "mijn nummer" (persoonlijk)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japan "mijn nummer" (zakelijk)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japan Resident registratie nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japans nummer van verblijfs kaart

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japans sociaal verzekerings nummer (SIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japans paspoort nummer

    :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>Luxemburg

:::row:::
    :::column span="":::
        **Entiteit**

        Luxemburgse nationale identificatie nummer (natuurlijke personen)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Luxemburgse nationale identificatie nummer (niet-natuurlijke personen)

    :::column-end:::
:::row-end:::

#### <a name="malta"></a>Malta

:::row:::
    :::column span="":::
        **Entiteit**

        Malta-ID-kaart nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Identificatie nummer Malta-belasting

    :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>Nieuw-Zeeland

:::row:::
    :::column span="":::
        **Entiteit**

        Bankrekening nummer Nieuw-Zeeland

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Rijbewijs nummer van Nieuw-Zeeland

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Nieuw-Zeeland land/-omzet nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Nieuw-Zeelandse ministerie van status nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Nieuw-Zeelandse sofinummer

    :::column-end:::
:::row-end:::


#### <a name="philippines"></a>Filipijnen

:::row:::
    :::column span="":::
        **Entiteit**

        Unified Multi-Purpose ID-nummer Filipijnen

    :::column-end:::
:::row-end:::

#### <a name="portugal"></a>Portugal 

:::row:::
    :::column span="":::
        **Entiteit**

        Portugal burger kaartnummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Portugal BTW-identificatie nummer

    :::column-end:::
:::row-end:::

#### <a name="singapore"></a>Singapore

:::row:::
    :::column span="":::
        **Entiteit**

        NRIC-nummer (National Registration ID) Singapore

    :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>Zuid-Afrika

:::row:::
    :::column span="":::
        **Entiteit**

        Zuid-Afrika-identificatie nummer

    :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>Zuid-Korea

:::row:::
    :::column span="":::
        **Entiteit**

        Geresident registratie nummer Zuid-Korea

    :::column-end:::
:::row-end:::

#### <a name="spain"></a>Spanje

:::row:::
    :::column span="":::
        **Entiteit**

        Spanje DNI

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sociaal-fiscaal nummer (SOFInummer) voor Spanje

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Spanje belasting identificatienummer

    :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>Zwitserland

:::row:::
    :::column span="":::
        **Entiteit**

        Sociaal-fiscaal nummer voor Zwitser land AHV

    :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>Taiwan 

:::row:::
    :::column span="":::
        **Entiteit**

        Taiwanese nationale ID

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Certificaat in Taiwan (ARC/TARC)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Taiwan Pass Port-nummer

    :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>Verenigd Koninkrijk

:::row:::
    :::column span="":::
        **Entiteit**

        Engelse Rijbewijs nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Engelse Aantal kiezers

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Engelse National Health Service (NHS)-nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Engelse Nationaal verzekerings nummer (NINO)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Engelse of Amerikaans paspoort nummer

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

       Engelse Uniek belastingplichtige-referentie nummer

    :::column-end:::

:::row-end:::


#### <a name="united-states"></a>Verenigde Staten

:::row:::
    :::column span="":::
        **Entiteit**

        Amerikaans sociaal-fiscaal nummer (SSN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Rijbewijs nummer van het Amerikaanse stuur programma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       V.S. of VK Paspoort nummer

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Amerikaans identificatie nummer voor de Amerikaanse belastingplichtige (ITIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       DEA-nummer (U.S. medicijn Enforcement Agency)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Amerikaans bankrekening nummer

    :::column-end:::
:::row-end:::
