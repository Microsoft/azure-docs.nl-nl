---
title: Algemene benoemde entiteiten
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2021
ms.author: aahi
ms.openlocfilehash: c1ff099dd6dffe06e9707ff23fffd57ae753ab64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "99500315"
---
De NER-functie voor Text Analytics retourneert de volgende algemene (niet-identificerende) entiteits categorieën. bijvoorbeeld bij het verzenden van aanvragen naar het `/entities/recognition/general` eind punt.


| Categorie | Beschrijving                          |
|------------|-------------|--------------------------------------|-------------------------------------------------------------|--------------------------------------|
| [Person](#category-person)     | Namen van personen.  |
| [PersonType](#category-persontype) | Taak typen of-rollen die door een persoon worden beheerd. |
| [Locatie](#category-location)    | Natuurlijke en door de mens gemaakte bezienswaardigheden, structuren, geografische functies en geopolitieke entiteiten |
| [Organisatie](#category-organization)  | Bedrijven, politieke groepen, muziek banden, sport clubs, overheids instanties en open bare organisaties.  |
| [Gebeurtenis](#category-event)  | Historische, sociale en natuurlijke gebeurtenissen die optreden. |
| [Product](#category-product) | Fysieke objecten van verschillende categorieën. |
| [Vaardigheid](#category-skill) | Een mogelijkheid, vaardigheid of deskundigheid.  |
| [Adres](#category-address) | Volledige mailing adressen.  |
| [Telefoonnummer](#category-phonenumber) | Telefoon nummers. |
| [E-mail](#category-email) | E-mail adressen. |
| [URL](#category-url) | Url's naar websites. |
| [IP](#category-ip) | IP-adressen van het netwerk. |
| [Datum/tijd](#category-datetime) | Datums en tijden van de dag. |
| [Aantal](#category-quantity) | Numerieke metingen en eenheden. |


### <a name="category-person"></a>Categorie: persoon

Deze categorie bevat de volgende entiteit:

:::row:::
    :::column span="":::
        **Entiteit**

        Person

    :::column-end:::
    :::column span="2":::
        **Details**

        Namen van personen.
      
    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, <br> `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt`-`pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

### <a name="category-persontype"></a>Categorie: PersonType

Deze categorie bevat de volgende entiteit:


:::row:::
    :::column span="":::
        **Entiteit**

        PersonType

    :::column-end:::
    :::column span="2":::
        **Details**

        Taak typen of-rollen die worden beheerd door een persoon
      
    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-location"></a>Categorie: locatie

Deze categorie bevat de volgende entiteit:

:::row:::
    :::column span="":::
        **Entiteit**

        Locatie

    :::column-end:::
    :::column span="2":::
        **Details**

        Natuurlijke en door de mens gemaakte bezienswaardigheden, structuren, geografische functies en geopolitieke entiteiten.
      
    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Subcategorieën

De entiteit in deze categorie kan de volgende subcategorieën hebben.

:::row:::
    :::column span="":::
        **Subcategorie van entiteit**

        Geopolitieke entiteit (GPE)

    :::column-end:::
    :::column span="2":::
        **Details**

        Steden, landen/regio's, statussen.
      
    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Verwant

    :::column-end:::
    :::column span="2":::

        Manmade-structuren. 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Geografische

    :::column-end:::
    :::column span="2":::

        Geografische en natuurlijke functies, zoals rivieren, oceanen en woestijns.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-organization"></a>Categorie: organisatie

Deze categorie bevat de volgende entiteit:

:::row:::
    :::column span="":::
        **Entiteit**

        Organisatie

    :::column-end:::
    :::column span="2":::
        **Details**

        Bedrijven, politieke groepen, muziek banden, sport clubs, overheids instanties en open bare organisaties. Nationale en religions zijn niet opgenomen in dit entiteits type.
      
    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Subcategorieën

De entiteit in deze categorie kan de volgende subcategorieën hebben.

:::row:::
    :::column span="":::
        **Subcategorie van entiteit**

        Medisch

    :::column-end:::
    :::column span="2":::
        **Details**

        Medische bedrijven en groepen.
      
    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Beurs

    :::column-end:::
    :::column span="2":::

        Voorraad wisselkoers groepen. 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sporten

    :::column-end:::
    :::column span="2":::

        Sport organisaties.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-event"></a>Categorie: gebeurtenis

Deze categorie bevat de volgende entiteit:

:::row:::
    :::column span="":::
        **Entiteit**

        Gebeurtenis

    :::column-end:::
    :::column span="2":::
        **Details**

        Historische, sociale en natuurlijke gebeurtenissen die optreden.
      
    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**

      `en`,,,,,,, `es` `fr` `de` `it` `zh-hans` `ja` `ko` `pt-pt` en `pt-br`  
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Subcategorieën

De entiteit in deze categorie kan de volgende subcategorieën hebben.

:::row:::
    :::column span="":::
        **Subcategorie van entiteit**

        Culturele

    :::column-end:::
    :::column span="2":::
        **Details**

        Culturele evenementen en feest dagen.
      
    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Schrift

    :::column-end:::
    :::column span="2":::

        Natuurlijk optredende gebeurtenissen.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sporten

    :::column-end:::
    :::column span="2":::

        Sport gebeurtenissen.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-product"></a>Categorie: product

Deze categorie bevat de volgende entiteit:

:::row:::
    :::column span="":::
        **Entiteit**

        Product

    :::column-end:::
    :::column span="2":::
        **Details**

        Fysieke objecten van verschillende categorieën.
      
    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::


#### <a name="subcategories"></a>Subcategorieën

De entiteit in deze categorie kan de volgende subcategorieën hebben.

:::row:::
    :::column span="":::
        **Subcategorie van entiteit**

        Computing-producten
    :::column-end:::
    :::column span="2":::
        **Details**

        Computing-producten.
      
    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-skill"></a>Categorie: vaardigheid

Deze categorie bevat de volgende entiteit:

:::row:::
    :::column span="":::
        **Entiteit**

        Vaardigheid

    :::column-end:::
    :::column span="2":::
        **Details**

        Een mogelijkheid, vaardigheid of deskundigheid.
      
    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**

      `en`  
      
   :::column-end:::
:::row-end:::

### <a name="category-address"></a>Categorie: adres

Deze categorie bevat de volgende entiteit:

:::row:::
    :::column span="":::
        **Entiteit**

        Adres

    :::column-end:::
    :::column span="2":::
        **Details**

        Volledig post adres.
      
    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-phonenumber"></a>Categorie: PhoneNumber

Deze categorie bevat de volgende entiteit:

:::row:::
    :::column span="":::
        **Entiteit**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **Details**

        Telefoon nummers (alleen telefoon nummers voor VS en EU).
      
    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-email"></a>Categorie: E-mail

Deze categorie bevat de volgende entiteit:

:::row:::
    :::column span="":::
        **Entiteit**

        E-mail

    :::column-end:::
    :::column span="2":::
        **Details**

        E-mail adressen.
      
    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-url"></a>Categorie: URL

Deze categorie bevat de volgende entiteit:

:::row:::
    :::column span="":::
        **Entiteit**

        URL

    :::column-end:::
    :::column span="2":::
        **Details**

        Url's naar websites. 
      
    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-ip"></a>Categorie: IP

Deze categorie bevat de volgende entiteit:

:::row:::
    :::column span="":::
        **Entiteit**

        IP

    :::column-end:::
    :::column span="2":::
        **Details**

        IP-adressen van het netwerk. 
      
    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-datetime"></a>Categorie: datum tijd

Deze categorie bevat de volgende entiteiten:

:::row:::
    :::column span="":::
        **Entiteit**

        DateTime

    :::column-end:::
    :::column span="2":::
        **Details**

        Datums en tijden van de dag. 
      
    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

Entiteiten in deze categorie kunnen de volgende subcategorieën hebben

#### <a name="subcategories"></a>Subcategorieën

De entiteit in deze categorie kan de volgende subcategorieën hebben.

:::row:::
    :::column span="":::
        **Subcategorie van entiteit**

        Datum

    :::column-end:::
    :::column span="2":::
        **Details**

        Kalender datums.
      
    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Tijd

    :::column-end:::
    :::column span="2":::

        Tijdstippen van de dag.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        DateRange

    :::column-end:::
    :::column span="2":::

        Datumbereiken.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        TimeRange

    :::column-end:::
    :::column span="2":::

        Tijds bereik.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Duur

    :::column-end:::
    :::column span="2":::

        Duur.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Instellen

    :::column-end:::
    :::column span="2":::

        Set, herhaalde tijden.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-quantity"></a>Categorie: hoeveelheid

Deze categorie bevat de volgende entiteiten:

:::row:::
    :::column span="":::
        **Entiteit**

        Aantal

    :::column-end:::
    :::column span="2":::
        **Details**

        Cijfers en numerieke aantallen.
      
    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Subcategorieën

De entiteit in deze categorie kan de volgende subcategorieën hebben.

:::row:::
    :::column span="":::
        **Subcategorie van entiteit**

        Aantal

    :::column-end:::
    :::column span="2":::
        **Details**

        Rijnummers.
      
    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Percentage

    :::column-end:::
    :::column span="2":::

        Percentages
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Rang nummers

    :::column-end:::
    :::column span="2":::

        Ordinale getallen.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Leeftijd

    :::column-end:::
    :::column span="2":::

        Leeftijd.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Valuta

    :::column-end:::
    :::column span="2":::

        Gelden
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Dimensies

    :::column-end:::
    :::column span="2":::

        Dimensies en metingen.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Temperatuur

    :::column-end:::
    :::column span="2":::

        Waarbij.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
