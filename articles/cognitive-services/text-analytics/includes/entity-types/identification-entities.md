---
title: Identificatie-entiteiten
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 352b81bf2dfeca1d7413e7cac131264d06c7b92e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599298"
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

        Als u deze entiteits categorie wilt ophalen, `ABARoutingNumber` moet u deze toevoegen aan de `pii-categories` para meter. `ABARoutingNumber` wordt ook geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**

      `en`
      
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

        Als u deze entiteits categorie wilt ophalen, `SWIFTCode` moet u deze toevoegen aan de `pii-categories` para meter. `SWIFTCode` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`
      
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

        Als u deze entiteits categorie wilt ophalen, `CreditCardNumber` moet u deze toevoegen aan de `pii-categories` para meter. `CreditCardNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.

    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`, `zh-hans`, `ja`, `ko`
      
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

        Als u deze entiteits categorie wilt ophalen, `InternationlBankingAccountNumber` moet u deze toevoegen aan de `pii-categories` para meter. `InternationlBankingAccountNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="2":::
      **Ondersteunde document talen**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`
      
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
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `ARNationalIdentityNumber` moet u deze toevoegen aan de `pii-categories` para meter. `ARNationalIdentityNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `en`, `es`
      
   :::column-end:::
:::row-end:::


#### <a name="austria"></a>Oostenrijk

:::row:::
    :::column span="":::
        **Entiteit**

        Identiteits kaart Oosten rijk

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `ATIdentityCard` moet u deze toevoegen aan de `pii-categories` para meter. `ATIdentityCard` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Identificatie nummer voor Oosten Rijks tarief

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `ATTaxIdentificationNumber` moet u deze toevoegen aan de `pii-categories` para meter. `ATTaxIdentificationNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        BTW-nummer voor de toegevoegde waarde van de Oosten rijk

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `ATValueAddedTaxNumber` moet u deze toevoegen aan de `pii-categories` para meter. `ATValueAddedTaxNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::



#### <a name="australia"></a>Australië

:::row:::
    :::column span="":::
        **Entiteit**

        Nummer van Australië-Bank account

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `AUDriversLicenseNumber` moet u deze toevoegen aan de `pii-categories` para meter. `AUDriversLicenseNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Australisch bedrijfs nummer

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `AUBusinessNumber` moet u deze toevoegen aan de `pii-categories` para meter. `AUBusinessNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Bedrijfs nummer Australië

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `AUCompanyNumber` moet u deze toevoegen aan de `pii-categories` para meter. `AUCompanyNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Licentie van het Australia-stuur programma  

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `AUDriversLicense` moet u deze toevoegen aan de `pii-categories` para meter. `AUDriversLicense` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Medisch account nummer in Australië

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `AUMedicalAccountNumber` moet u deze toevoegen aan de `pii-categories` para meter. `AUMedicalAccountNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Australië-paspoort nummer

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `ATPassportNumber` moet u deze toevoegen aan de `pii-categories` para meter. `ATPassportNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Australia-BTW-bestands nummer

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `ATTaxIdentificationNumber` moet u deze toevoegen aan de `pii-categories` para meter. `ATTaxIdentificationNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="belgium"></a>België

:::row:::
    :::column span="":::
        **Entiteit**

        België nationaal nummer

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `BENationalNumber` moet u deze toevoegen aan de `pii-categories` para meter. `BENationalNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Het BTW-nummer van de Belgische toegevoegde waarde

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `BEValueAddedTaxNumber` moet u deze toevoegen aan de `pii-categories` para meter. `BEValueAddedTaxNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::


#### <a name="brazil"></a>Brazilië 

:::row:::
    :::column span="":::
        **Entiteit**

        Nummer van juridische entiteit in Brazilië (CNPJ)

        

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `BRLegalEntityNumber` moet u deze toevoegen aan de `pii-categories` para meter. `BRLegalEntityNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Brazilië CPF-nummer

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `BRCPFNumber` moet u deze toevoegen aan de `pii-categories` para meter. `BRCPFNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        National ID-kaart (RG) Brazilië

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `BRNationalIDRG` moet u deze toevoegen aan de `pii-categories` para meter. `BRNationalIDRG` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="canada"></a>Canada

:::row:::
    :::column span="":::
        **Entiteit**

        Rekening nummer Canada

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `CABankAccountNumber` moet u deze toevoegen aan de `pii-categories` para meter. `CABankAccountNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Rijbewijs nummer van het Canada-stuur programma

    :::column-end:::

    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `CADriversLicenseNumber` moet u deze toevoegen aan de `pii-categories` para meter. `CADriversLicenseNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Canada-Health-Service nummer

        
    :::column-end:::

    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `CAHealthServiceNumber` moet u deze toevoegen aan de `pii-categories` para meter. `CAHealthServiceNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Canada paspoort nummer

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `CAPassportNumber` moet u deze toevoegen aan de `pii-categories` para meter. `CAPassportNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Privé-identificatie nummer voor Canada (PHIN)

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `CAPersonalHealthIdentification` moet u deze toevoegen aan de `pii-categories` para meter. `CAPersonalHealthIdentification` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sofi-nummer Canada

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `CASocialInsuranceNumber` moet u deze toevoegen aan de `pii-categories` para meter. `CASocialInsuranceNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::

#### <a name="chile"></a>Chili 

:::row:::
    :::column span="":::
        **Entiteit**

        ID-kaart nummer Chili

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `CLIdentityCardNumber` moet u deze toevoegen aan de `pii-categories` para meter. `CLIdentityCardNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `es`
      
   :::column-end:::
:::row-end:::

#### <a name="china"></a>China

:::row:::
    :::column span="":::
        **Entiteit**

        China-nummer van de residente identiteits kaart (China)

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `CNResidentIdentityCardNumber` moet u deze toevoegen aan de `pii-categories` para meter. `CNResidentIdentityCardNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>Europese Unie (EU)

:::row:::
    :::column span="":::
        **Entiteit**

        Nummer ICL-betaalpas

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `EUDebitCardNumber` moet u deze toevoegen aan de `pii-categories` para meter. `EUDebitCardNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Licentie nummer van het EU-stuur programma

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `EUDriversLicenseNumber` moet u deze toevoegen aan de `pii-categories` para meter. `EUDriversLicenseNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU GPU-coördinaten

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `EUGPSCoordinates` moet u deze toevoegen aan de `pii-categories` para meter. `EUGPSCoordinates` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sofi-nummer van de EU

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `EUNationalIdentificationNumber` moet u deze toevoegen aan de `pii-categories` para meter. `EUNationalIdentificationNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU-paspoort nummer

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `EUPassportNumber` moet u deze toevoegen aan de `pii-categories` para meter. `EUPassportNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sofi-nummer van de EU of gelijkwaardige ID

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `EUSocialSecurityNumber` moet u deze toevoegen aan de `pii-categories` para meter. `EUSocialSecurityNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        BTW-identificatie nummer van de EU

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `EUTaxIdentificationNumber` moet u deze toevoegen aan de `pii-categories` para meter. `EUTaxIdentificationNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::

#### <a name="france"></a>Frankrijk

:::row:::
    :::column span="":::
        **Entiteit**

        Rijbewijs nummer van Frank rijk stuur programma

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `FRDriversLicenseNumber` moet u deze toevoegen aan de `pii-categories` para meter. `FRDriversLicenseNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Frank rijk-medische-verzekerings nummer

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `FRHealthInsuranceNumber` moet u deze toevoegen aan de `pii-categories` para meter. `FRHealthInsuranceNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Landse nationale ID-kaart (CNI)

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `FRNationalID` moet u deze toevoegen aan de `pii-categories` para meter. `FRNationalID` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Frank rijk paspoort nummer

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `FRPassportNumber` moet u deze toevoegen aan de `pii-categories` para meter. `FRPassportNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sofi-nummer Frank rijk (INSEE)

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `FRSocialSecurityNumber` moet u deze toevoegen aan de `pii-categories` para meter. `FRSocialSecurityNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Frank rijk-belasting identificatienummer (numéro SPI)

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `FRTaxIdentificationNumber` moet u deze toevoegen aan de `pii-categories` para meter. `FRTaxIdentificationNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        BTW-nummer van toegevoegde waarde voor de Frank rijk

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `FRValueAddedTaxNumber` moet u deze toevoegen aan de `pii-categories` para meter. `FRValueAddedTaxNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::

#### <a name="germany"></a>Duitsland

:::row:::
    :::column span="":::
        **Entiteit**

        Rijbewijs nummer van het Duitse stuur programma

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `DEDriversLicenseNumber` moet u deze toevoegen aan de `pii-categories` para meter. `DEDriversLicenseNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Nummer van de Duitsland-identiteits kaart

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `DEIdentityCardNumber` moet u deze toevoegen aan de `pii-categories` para meter. `DEIdentityCardNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Duitsland-paspoort nummer

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `DEPassportNumber` moet u deze toevoegen aan de `pii-categories` para meter. `DEPassportNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Nummer van de Duitse belasting-ID

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `DETaxIdentificationNumber` moet u deze toevoegen aan de `pii-categories` para meter. `DETaxIdentificationNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Omzetbelasting nummer van toegevoegde Duitse waarde

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `DEValueAddedNumber` moet u deze toevoegen aan de `pii-categories` para meter. `DEValueAddedNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>Hongkong

:::row:::
    :::column span="":::
        **Entiteit**

        HKID-nummer (Hong Kong Identity Card)

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `HKIdentityCardNumber` moet u deze toevoegen aan de `pii-categories` para meter. `HKIdentityCardNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="hungary"></a>Hongarije

:::row:::
    :::column span="":::
        **Entiteit**

        Persoonlijk identificatie nummer Hongarije

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `HUPersonalIdentificationNumber` moet u deze toevoegen aan de `pii-categories` para meter. `HUPersonalIdentificationNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        BTW-identificatie nummer Hongarije

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `HUTaxIdentificationNumber` moet u deze toevoegen aan de `pii-categories` para meter. `HUTaxIdentificationNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Omzetbelasting nummer toegevoegde waarde

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `HUValueAddedNumber` moet u deze toevoegen aan de `pii-categories` para meter. `HUValueAddedNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="india"></a>India

:::row:::
    :::column span="":::
        **Entiteit**

        Permanent account nummer India (PAN)

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `INPermanentAccount` moet u deze toevoegen aan de `pii-categories` para meter. `INPermanentAccount` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Aadhaar-nummer (India-unieke identificatie)

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `INUniqueIdentificationNumber` moet u deze toevoegen aan de `pii-categories` para meter. `INUniqueIdentificationNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>Indonesië

:::row:::
    :::column span="":::
        **Entiteit**

        KTP-nummer (Indonesië-identiteits kaart)

    :::column-end:::
    :::column span="2":::

        **Details**

        Als u deze entiteits categorie wilt ophalen, `IDIdentityCardNumber` moet u deze toevoegen aan de `pii-categories` para meter. `IDIdentityCardNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="ireland"></a>Ierland

:::row:::
    :::column span="":::
        **Entiteit**

        Het PPS-nummer (Personal public service) in Ierland

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `IEPersonalPublicServiceNumber` moet u deze toevoegen aan de `pii-categories` para meter. `IEPersonalPublicServiceNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
 
        Het PPS-nummer (Personal public service) van Ierland v2

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `IEPersonalPublicServiceNumberV2` moet u deze toevoegen aan de `pii-categories` para meter. `IEPersonalPublicServiceNumberV2` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="israel"></a>Israël

:::row:::
    :::column span="":::
        **Entiteit**

        Nationale ID Israël

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `ILNationalID` moet u deze toevoegen aan de `pii-categories` para meter. `ILNationalID` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Israël-bankrekening nummer

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `ILBankAccountNumber` moet u deze toevoegen aan de `pii-categories` para meter. `ILBankAccountNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="italy"></a>Italië

:::row:::
    :::column span="":::
        **Entiteit**

        Licentie-ID van het Italië-stuur programma

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `ITDriversLicenseNumber` moet u deze toevoegen aan de `pii-categories` para meter. `ITDriversLicenseNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Italië-fiscale code

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `ITFiscalCode` moet u deze toevoegen aan de `pii-categories` para meter. `ITFiscalCode` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Omzetbelasting nummer voor toegevoegde waarde van Italië

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `ITValueAddedTaxNumber` moet u deze toevoegen aan de `pii-categories` para meter. `ITValueAddedTaxNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::


#### <a name="japan"></a>Japan

:::row:::
    :::column span="":::
        **Entiteit**

        Japans bankrekening nummer

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `JPBankAccountNumber` moet u deze toevoegen aan de `pii-categories` para meter. `JPBankAccountNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Rijbewijs nummer van het Japanse stuur programma

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `JPDriversLicenseNumber` moet u deze toevoegen aan de `pii-categories` para meter. `JPDriversLicenseNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japan "mijn nummer" (persoonlijk)

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `JPMyNumberPersonal` moet u deze toevoegen aan de `pii-categories` para meter. `JPMyNumberPersonal` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japan "mijn nummer" (zakelijk)

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `JPMyNumberCorporate` moet u deze toevoegen aan de `pii-categories` para meter. `JPMyNumberCorporate` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japan Resident registratie nummer

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `ITValueAddedTaxNumber` moet u deze toevoegen aan de `pii-categories` para meter. `ITValueAddedTaxNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

     `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japans nummer van verblijfs kaart

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `JPResidenceCardNumber` moet u deze toevoegen aan de `pii-categories` para meter. `JPResidenceCardNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japans sociaal verzekerings nummer (SIN)

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `JPSocialInsuranceNumber` moet u deze toevoegen aan de `pii-categories` para meter. `JPSocialInsuranceNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japans paspoort nummer

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `JPPassportNumber` moet u deze toevoegen aan de `pii-categories` para meter. `JPPassportNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>Luxemburg

:::row:::
    :::column span="":::
        **Entiteit**

        Luxemburgse nationale identificatie nummer (natuurlijke personen)

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `LUNationalIdentificationNumberNatural` moet u deze toevoegen aan de `pii-categories` para meter. `LUNationalIdentificationNumberNatural` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Luxemburgse nationale identificatie nummer (niet-natuurlijke personen)

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `LUNationalIdentificationNumberNonNatural` moet u deze toevoegen aan de `pii-categories` para meter. `LUNationalIdentificationNumberNonNatural` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::

#### <a name="malta"></a>Malta

:::row:::
    :::column span="":::
        **Entiteit**

        Malta-ID-kaart nummer

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `MTIdentityCardNumber` moet u deze toevoegen aan de `pii-categories` para meter. `MTIdentityCardNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Identificatie nummer Malta-belasting

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `MTTaxIDNumber` moet u deze toevoegen aan de `pii-categories` para meter. `MTTaxIDNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>Nieuw-Zeeland

:::row:::
    :::column span="":::
        **Entiteit**

        Bankrekening nummer Nieuw-Zeeland

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `NZBankAccountNumber` moet u deze toevoegen aan de `pii-categories` para meter. `NZBankAccountNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Rijbewijs nummer van Nieuw-Zeeland

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `NZDriversLicenseNumber` moet u deze toevoegen aan de `pii-categories` para meter. `NZDriversLicenseNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Nieuw-Zeeland land/-omzet nummer

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `NZInlandRevenueNumber` moet u deze toevoegen aan de `pii-categories` para meter. `NZInlandRevenueNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Nieuw-Zeelandse ministerie van status nummer

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `NZMinistryOfHealthNumber` moet u deze toevoegen aan de `pii-categories` para meter. `NZMinistryOfHealthNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Nieuw-Zeelandse sofinummer

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `NZSocialWelfareNumber` moet u deze toevoegen aan de `pii-categories` para meter. `NZSocialWelfareNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="philippines"></a>Filipijnen

:::row:::
    :::column span="":::
        **Entiteit**

        Unified Multi-Purpose ID-nummer Filipijnen

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `PHUnifiedMultiPurposeIDNumber` moet u deze toevoegen aan de `pii-categories` para meter. `PHUnifiedMultiPurposeIDNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="portugal"></a>Portugal 

:::row:::
    :::column span="":::
        **Entiteit**

        Portugal burger kaartnummer

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `PTCitizenCardNumber` moet u deze toevoegen aan de `pii-categories` para meter. `PTCitizenCardNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `pt-pt`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Portugal BTW-identificatie nummer

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `PTTaxIdentificationNumber` moet u deze toevoegen aan de `pii-categories` para meter. `PTTaxIdentificationNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`
      
   :::column-end:::
:::row-end:::

#### <a name="singapore"></a>Singapore

:::row:::
    :::column span="":::
        **Entiteit**

        NRIC-nummer (National Registration ID) Singapore

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `PTTaxIdentificationNumber` moet u deze toevoegen aan de `pii-categories` para meter. `PTTaxIdentificationNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `en`, `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>Zuid-Afrika

:::row:::
    :::column span="":::
        **Entiteit**

        Zuid-Afrika-identificatie nummer

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `ZAIdentificationNumber` moet u deze toevoegen aan de `pii-categories` para meter. `ZAIdentificationNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>Zuid-Korea

:::row:::
    :::column span="":::
        **Entiteit**

        Geresident registratie nummer Zuid-Korea

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `KRResidentRegistrationNumber` moet u deze toevoegen aan de `pii-categories` para meter. `KRResidentRegistrationNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `ko`
      
   :::column-end:::
:::row-end:::

#### <a name="spain"></a>Spanje

:::row:::
    :::column span="":::
        **Entiteit**

        Spanje DNI

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `ESDNI` moet u deze toevoegen aan de `pii-categories` para meter. `ESDNI` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sociaal-fiscaal nummer (SOFInummer) voor Spanje

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `ESSocialSecurityNumber` moet u deze toevoegen aan de `pii-categories` para meter. `ESSocialSecurityNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Spanje belasting identificatienummer

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `ESTaxIdentificationNumber` moet u deze toevoegen aan de `pii-categories` para meter. `ESTaxIdentificationNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>Zwitserland

:::row:::
    :::column span="":::
        **Entiteit**

        Sociaal-fiscaal nummer voor Zwitser land AHV

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `CHSocialSecurityNumber` moet u deze toevoegen aan de `pii-categories` para meter. `CHSocialSecurityNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `fr`, `de`, `it`
      
   :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>Taiwan 

:::row:::
    :::column span="":::
        **Entiteit**

        Taiwanese nationale ID

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `TWNationalID` moet u deze toevoegen aan de `pii-categories` para meter. `TWNationalID` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Certificaat in Taiwan (ARC/TARC)

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `TWResidentCertificate` moet u deze toevoegen aan de `pii-categories` para meter. `TWResidentCertificate` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Taiwan Pass Port-nummer

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `TWPassportNumber` moet u deze toevoegen aan de `pii-categories` para meter. `TWPassportNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>Verenigd Koninkrijk

:::row:::
    :::column span="":::
        **Entiteit**

        Engelse Rijbewijs nummer

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `UKDriversLicenseNumber` moet u deze toevoegen aan de `pii-categories` para meter. `UKDriversLicenseNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `en`
      
    :::column-end:::
    
:::row-end:::
:::row:::
    :::column span="":::

       Engelse Aantal kiezers

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `UKNationalInsuranceNumber` moet u deze toevoegen aan de `pii-categories` para meter. `UKNationalInsuranceNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Engelse National Health Service (NHS)-nummer

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `UKNationalHealthNumber` moet u deze toevoegen aan de `pii-categories` para meter. `UKNationalHealthNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Engelse Nationaal verzekerings nummer (NINO)

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `UKNationalInsuranceNumber` moet u deze toevoegen aan de `pii-categories` para meter. `UKNationalInsuranceNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Engelse of Amerikaans paspoort nummer

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `USUKPassportNumber` moet u deze toevoegen aan de `pii-categories` para meter. `USUKPassportNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Engelse Uniek belastingplichtige-referentie nummer

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `UKUniqueTaxpayerNumber` moet u deze toevoegen aan de `pii-categories` para meter. `UKUniqueTaxpayerNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="united-states"></a>Verenigde Staten

:::row:::
    :::column span="":::
        **Entiteit**

        Amerikaans sociaal-fiscaal nummer (SSN)

    :::column-end:::
    :::column span="2":::
        **Details**

        Als u deze entiteits categorie wilt ophalen, `USSocialSecurityNumber` moet u deze toevoegen aan de `pii-categories` para meter. `USSocialSecurityNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::
      **Ondersteunde document talen**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Rijbewijs nummer van het Amerikaanse stuur programma

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `USDriversLicenseNumber` moet u deze toevoegen aan de `pii-categories` para meter. `USDriversLicenseNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       V.S. of VK Paspoort nummer

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `USUKPassportNumber` moet u deze toevoegen aan de `pii-categories` para meter. `USUKPassportNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Amerikaans identificatie nummer voor de Amerikaanse belastingplichtige (ITIN)

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `USIndividualTaxpayerIdentification` moet u deze toevoegen aan de `pii-categories` para meter. `USIndividualTaxpayerIdentification` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       DEA-nummer (U.S. medicijn Enforcement Agency)

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `DrugEnforcementAgencyNumber` moet u deze toevoegen aan de `pii-categories` para meter. `DrugEnforcementAgencyNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Amerikaans bankrekening nummer

    :::column-end:::
    :::column span="2":::

        Als u deze entiteits categorie wilt ophalen, `USBankAccountNumber` moet u deze toevoegen aan de `pii-categories` para meter. `USBankAccountNumber` wordt geretourneerd in de API-reactie als deze wordt gedetecteerd.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
