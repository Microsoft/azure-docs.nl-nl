---
title: De privacygegevens van uw organisatie toevoegen-Azure Active Directory | Microsoft Docs
description: Instructies voor het toevoegen van de privacygegevens van uw organisatie aan het gedeelte met Azure Active Directory eigenschappen.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 04/17/2018
ms.author: ajburnle
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f47ae9b087615a77f6bd4d3f14f1b0052037cba6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996748"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>De privacygegevens van uw organisatie toevoegen met behulp van Azure Active Directory
In dit artikel wordt uitgelegd hoe een Tenant beheerder via de Azure Portal aan privacy gerelateerde informatie kan toevoegen aan de Azure Active Directory van een organisatie (Azure AD).

We raden u ten zeerste aan om zowel uw wereld wijde privacy-contact persoon als de privacyverklaring van uw organisatie toe te voegen, zodat uw interne mede werkers en externe gasten uw beleid kunnen controleren. Omdat privacyverklaringen uniek zijn gemaakt en aangepast voor elk bedrijf, raden we u ten zeerste aan om contact op te nemen met een advocaat voor hulp.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Uw privacy-informatie toevoegen aan Azure AD
U kunt de privacygegevens van uw organisatie toevoegen in het gedeelte **Eigenschappen** van Azure AD.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>Voor toegang tot het gedeelte Eigenschappen en uw privacy-informatie toevoegen

1. Meld u aan bij de Azure Portal als een Tenant beheerder.

2. Selecteer **Azure Active Directory** op de navigatie balk aan de linkerkant en selecteer vervolgens **Eigenschappen**.

    Het gebied **Eigenschappen** wordt weer gegeven.

    ![Azure AD-eigenschappen gebied met markeren van het gebied met privacy-informatie](media/active-directory-properties-area/properties-area.png)

3. Uw privacy-informatie voor uw werk nemers toevoegen:

    - **Technische contact persoon.** Typ het e-mail adres van de persoon die contact moet opnemen voor technische ondersteuning binnen uw organisatie.
    
    - **Wereld wijde privacy-contact persoon.** Typ het e-mail adres van de contact persoon voor informatie over privacy van persoonlijke gegevens. Deze persoon is ook wie micro soft contact maakt als er een inbreuk op de gegevens is. Als hier geen persoon wordt vermeld, neemt micro soft contact op met uw globale beheerders.

    - **URL van privacyverklaring.** Typ de koppeling naar het document van uw organisatie waarin wordt beschreven hoe uw organisatie de privacy van de interne en externe gegevens verhandelt.

        >[!Important]
        >Als u geen van uw eigen privacyverklaring of uw privacy-contact persoon opneemt, wordt in uw externe gasten tekst weer gegeven in het vak **machtigingen controleren** . **< _de naam van uw organisatie_> geen koppelingen naar de voor waarden die u kunt door geven**. Een gast gebruiker krijgt bijvoorbeeld dit bericht te zien wanneer ze een uitnodiging ontvangen om toegang te krijgen tot een organisatie via B2B-samen werking.

        ![Het vak Machtigingen voor B2B-samen werking controleren met een bericht](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen
- [Uitnodigingsinwisseling voor Azure Active Directory B2B-samenwerking](../external-identities/redemption-experience.md)
- [Profiel gegevens voor een gebruiker toevoegen of wijzigen in Azure Active Directory](active-directory-users-profile-azure-portal.md)