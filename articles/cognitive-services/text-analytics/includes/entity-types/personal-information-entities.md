---
title: Benoemde entiteiten voor persoonlijke gegevens
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/15/2021
ms.author: aahi
ms.openlocfilehash: 7484b49ed3c868a1ad3e0f97dffa346f350e127f
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097734"
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

        Als u deze entiteits categorie wilt ophalen, `Person` moet u deze toevoegen aan de `pii-categories` para meter. `Person` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    
    :::column span="":::
      **Ondersteunde document talen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
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

        Als u deze entiteits categorie wilt ophalen, `PersonType` moet u deze toevoegen aan de `pii-categories` para meter. `PersonType` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::

    :::column span="":::
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

        Telefoon nummers (alleen telefoon nummers voor VS en EU). Ook geretourneerd met `domain=phi` .

        Als u deze entiteits categorie wilt ophalen, `PhoneNumber` moet u deze toevoegen aan de `pii-categories` para meter. `PhoneNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::

    :::column span="":::
      **Ondersteunde document talen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
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

        Bedrijven, politieke groepen, muziek banden, sport clubs, overheids instanties en open bare organisaties. Nationale en religions zijn niet opgenomen in dit entiteits type. Ook geretourneerd met `domain=phi` .

        Als u deze entiteits categorie wilt ophalen, `Organization` moet u deze toevoegen aan de `pii-categories` para meter. `Organization` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::

    :::column span="":::
      **Ondersteunde document talen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
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

        Als u deze entiteits categorie wilt ophalen, `OrganizationMedical` moet u deze toevoegen aan de `pii-categories` para meter. `OrganizationMedical` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::

    :::column span="":::
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

        Als u deze entiteits categorie wilt ophalen, `OrganizationStockExchange` moet u deze toevoegen aan de `pii-categories` para meter. `OrganizationStockExchange` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::

    :::column span="":::

      `en`   
      
   :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Sporten

    :::column-end:::
    :::column span="2":::

        Sport organisaties.

        Als u deze entiteits categorie wilt ophalen, `OrganizationSports` moet u deze toevoegen aan de `pii-categories` para meter. `OrganizationSports` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::

    :::column span="":::

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

        Volledig post adres. Ook geretourneerd met `domain=phi` .

        Als u deze entiteits categorie wilt ophalen, `Address` moet u deze toevoegen aan de `pii-categories` para meter. `Address` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::

    :::column span="":::
      **Ondersteunde document talen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
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

        E-mail adressen. Ook geretourneerd met `domain=phi` .
      
        Als u deze entiteits categorie wilt ophalen, `Email` moet u deze toevoegen aan de `pii-categories` para meter. `Email` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.

    :::column-end:::
    :::column span="":::
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

        Url's naar websites. Ook geretourneerd met `domain=phi` .

        Als u deze entiteits categorie wilt ophalen, `URL` moet u deze toevoegen aan de `pii-categories` para meter. `URL` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::

    :::column span="":::
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

        IP-adressen van het netwerk. Ook geretourneerd met `domain=phi` .

        Als u deze entiteits categorie wilt ophalen, `IP` moet u deze toevoegen aan de `pii-categories` para meter. `IP` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::

    :::column span="":::
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

        Als u deze entiteits categorie wilt ophalen, `DateTime` moet u deze toevoegen aan de `pii-categories` para meter. `DateTime` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
:::column span="":::
      **Ondersteunde document talen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
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

        Kalender datums. Ook geretourneerd met `domain=phi` .

        Als u deze entiteits categorie wilt ophalen, `Date` moet u deze toevoegen aan de `pii-categories` para meter. `Date` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**
      
      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
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

        Als u deze entiteits categorie wilt ophalen, `Quantity` moet u deze toevoegen aan de `pii-categories` para meter. `Quantity` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
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

        Leeftijd

    :::column-end:::
    :::column span="2":::
        **Details**

        Leeftijd. 

        Als u deze entiteits categorie wilt ophalen, `Age` moet u deze toevoegen aan de `pii-categories` para meter. `Age` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="2":::
        **Ondersteunde document talen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
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

        Als u deze entiteits categorie wilt ophalen, `AzureDocumentDBAuthKey` moet u deze toevoegen aan de `pii-categories` para meter. `AzureDocumentDBAuthKey` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Verbindings reeks voor de Azure IAAS-data base en de Azure SQL-verbindings reeks.
        

    :::column-end:::
    :::column span="2":::

        Verbindings reeks voor een Azure Infrastructure as a service-data base (IaaS) en SQL connection string.

        Als u deze entiteits categorie wilt ophalen, `AzureIAASDatabaseConnectionAndSQLString` moet u deze toevoegen aan de `pii-categories` para meter. `AzureIAASDatabaseConnectionAndSQLString` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure IoT-verbindings reeks  

    :::column-end:::
    :::column span="2":::

        Verbindings reeks voor Azure IoT. 
      
        Als u deze entiteits categorie wilt ophalen, `AzureIoTConnectionString` moet u deze toevoegen aan de `pii-categories` para meter. `AzureIoTConnectionString` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.

    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Wacht woord voor de publicatie-instelling van Azure  

    :::column-end:::
    :::column span="2":::

        Wacht woord voor Azure Publish-instellingen.

        Als u deze entiteits categorie wilt ophalen, `AzurePublishSettingPassword` moet u deze toevoegen aan de `pii-categories` para meter. `AzurePublishSettingPassword` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Verbindings reeks Azure Redis Cache 

    :::column-end:::
    :::column span="2":::

        Verbindings reeks voor een redis-cache.

        Als u deze entiteits categorie wilt ophalen, `AzureRedisCacheString` moet u deze toevoegen aan de `pii-categories` para meter. `AzureRedisCacheString` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure SAS 

    :::column-end:::
    :::column span="2":::

        De verbindings reeks voor de Azure-software als een service (SaaS).

        Als u deze entiteits categorie wilt ophalen, `AzureSAS` moet u deze toevoegen aan de `pii-categories` para meter. `AzureSAS` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Verbindings reeks Azure Service Bus

    :::column-end:::
    :::column span="2":::

        Verbindings reeks voor een Azure service bus.

        Als u deze entiteits categorie wilt ophalen, `AzureServiceBusString` moet u deze toevoegen aan de `pii-categories` para meter. `AzureServiceBusString` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sleutel van Azure Storage-account 

    :::column-end:::
    :::column span="2":::

        Account sleutel voor een Azure-opslag account. 

        Als u deze entiteits categorie wilt ophalen, `AzureStorageAccountKey` moet u deze toevoegen aan de `pii-categories` para meter. `AzureStorageAccountKey` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sleutel van Azure Storage-account (algemeen)

    :::column-end:::
    :::column span="2":::

        Algemene account sleutel voor een Azure-opslag account.

        Als u deze entiteits categorie wilt ophalen, `AzureStorageAccountGeneric` moet u deze toevoegen aan de `pii-categories` para meter. `AzureStorageAccountGeneric` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Verbindings reeks SQL Server 

    :::column-end:::
    :::column span="2":::

        Verbindings reeks voor een computer met SQL Server.

        Als u deze entiteits categorie wilt ophalen, `SQLServerConnectionString` moet u deze toevoegen aan de `pii-categories` para meter. `SQLServerConnectionString` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::

### <a name="identification"></a>Identificatie

[!INCLUDE [supported identification entities](./identification-entities.md)]
