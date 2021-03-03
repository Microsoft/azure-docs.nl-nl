---
title: Benoemde entiteiten voor persoonlijke gegevens
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: 97167485dae155670f0eb83fc3ef9cb658952251
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750934"
---
> [!NOTE]
> Gebruik de `domain=phi` para meter en model versie of hoger om de beschermde status informatie te detecteren (Phi) `2020-04-01` .
>
> Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii?domain=phi&model-version=2021-01-15`
 
De volgende entiteits categorieën worden geretourneerd wanneer u aanvragen naar het `/v3.1-preview.3/entities/recognition/pii` eind punt verzendt.


| Categorie   |  Beschrijving                          |
|------------|-------------|
| [Person](#category-person)      |  Namen van personen.  |
| [PersonType](#category-persontype) | Taak typen of-rollen die door een persoon worden beheerd. |
| [Telefoonnummer](#category-phonenumber) |Telefoon nummers (alleen telefoon nummers voor VS en EU). |
| [Organisatie](#category-organization) |  Bedrijven, groepen, overheids instanties en andere organisaties.  |
| [Adres](#category-address) | Volledige mailing adressen.  |
| [E-mail](#category-email) | E-mail adressen.   |
| [URL](#category-url) | Url's naar websites.  |
| [IP](#category-ip) | IP-adressen van het netwerk.  |
| [Datum/tijd](#category-datetime) | Datums en tijden van de dag. | 
| [Aantal](#category-quantity) | Cijfers en numerieke aantallen.  |
| [Azure-gegevens](#azure-information) | Identificeer bare Azure-informatie, zoals verificatie-informatie.  |
| [Identificatie](#identification) | Specifieke identificatie van financiële en land.  |

### <a name="category-person"></a>Categorie: persoon

Deze categorie bevat de volgende entiteit:

:::row:::
    :::column span="":::
        **Entiteit**

        Person

    :::column-end:::
    :::column span="2":::
        **Details**

        Namen van personen. Ook geretourneerd met `domain=phi` .
      
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

        Taak typen of-rollen die door een persoon worden beheerd.
      
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

        Telefoon nummers (alleen telefoon nummers voor VS en EU). Ook geretourneerd met `domain=phi` .
      
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
:::row-end:::
:::row:::
    :::column span="":::

        Beurs

    :::column-end:::
    :::column span="2":::

        Voorraad wisselkoers groepen. 
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sporten

    :::column-end:::
    :::column span="2":::

        Sport organisaties.
      
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
:::row-end:::

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
:::row-end:::

#### <a name="subcategories"></a>Subcategorieën

De entiteit in deze categorie kan de volgende subcategorieën hebben.

:::row:::
    :::column span="":::
        **Subcategorie van entiteit**

        Leeftijd

    :::column-end:::
    :::column span="2":::
        **Details**

        Leeftijd.
      
    :::column-end:::
:::row-end:::

### <a name="azure-information"></a>Azure-gegevens

Deze entiteits categorieën bevatten Identificeer bare Azure-informatie, inclusief verificatie gegevens en verbindings reeksen. Niet geretourneerd met de `domain=phi` para meter.

:::row:::
    :::column span="":::
        **Entiteit**

        Verificatie sleutel voor Azure DocumentDB 

    :::column-end:::
    :::column span="2":::
        **Details**

        Autorisatie sleutel voor een Azure Cosmos DB-server.   
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Verbindings reeks voor de Azure IAAS-data base en de Azure SQL-verbindings reeks

    :::column-end:::
    :::column span="2":::

        Verbindings reeks voor een Azure Infrastructure as a service-data base (IaaS) en SQL connection string.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure SQL-verbindings reeks

    :::column-end:::
    :::column span="2":::

        Verbindings reeks voor een data base in Azure SQL Database.
      
    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Azure IoT-verbindings reeks  

    :::column-end:::
    :::column span="2":::

        Verbindings reeks voor Azure IoT. 
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Wacht woord voor de publicatie-instelling van Azure  

    :::column-end:::
    :::column span="2":::

        Wacht woord voor Azure Publish-instellingen.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Verbindings reeks Azure Redis Cache 

    :::column-end:::
    :::column span="2":::

        Verbindings reeks voor een redis-cache.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure SAS 

    :::column-end:::
    :::column span="2":::

        De verbindings reeks voor de Azure-software als een service (SaaS).
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Verbindings reeks Azure Service Bus

    :::column-end:::
    :::column span="2":::

        Verbindings reeks voor een Azure service bus.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sleutel van Azure Storage-account 

    :::column-end:::
    :::column span="2":::

       Account sleutel voor een Azure-opslag account. 
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sleutel van Azure Storage-account (algemeen)

    :::column-end:::
    :::column span="2":::

       Algemene account sleutel voor een Azure-opslag account.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Verbindings reeks SQL Server 

    :::column-end:::
    :::column span="2":::

       Verbindings reeks voor een computer met SQL Server.
      
    :::column-end:::
:::row-end:::

### <a name="identification"></a>Identificatie

[!INCLUDE [supported identification entities](./identification-entities.md)]
