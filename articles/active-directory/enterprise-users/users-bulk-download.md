---
title: Een lijst met gebruikers in de Azure Active Directory portal downloaden | Microsoft Docs
description: Down load gebruikers records bulksgewijs in het Azure-beheer centrum in Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 01/04/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57e3a059a5dd846250ba162513ef118e084c4b87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97861602"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Een lijst met gebruikers in Azure Active Directory portal downloaden

Azure Active Directory (Azure AD) biedt ondersteuning voor het importeren van bulk gebruikers (maken).

## <a name="required-permissions"></a>Vereiste machtigingen

Als u de lijst met gebruikers wilt downloaden vanuit het Azure AD-beheercentrum, moet u zijn aangemeld met een gebruiker die is toegewezen aan een of meer beheerdersrollen op organisatieniveau in Azure AD (gebruikersbeheerder is de minimale vereiste rol). Afzender van gastuitnodigingen en toepassingsontwikkelaar worden niet beschouwd als beheerdersrollen.

## <a name="to-download-a-list-of-users"></a>Een lijst met gebruikers downloaden

1. [Meld u aan bij uw Azure AD-organisatie](https://aad.portal.azure.com) met een Administrator-account van de gebruiker in de organisatie.
2. Ga naar Azure Active Directory > gebruikers. Selecteer vervolgens de gebruikers die u wilt opnemen in de download door het vak in de linkerkolom naast elke gebruiker te selecteren. Opmerking: Op dit moment is het niet mogelijk om alle gebruikers te selecteren om te exporteren. Elke gebruiker moet afzonderlijk worden geselecteerd.
3. Selecteer **gebruikers**  >  **downloaden gebruikers** in azure AD.
4. Selecteer op de pagina **gebruikers downloaden** de optie **Start** om een CSV-bestand met gebruikers profiel eigenschappen te ontvangen. Als er fouten zijn, kunt u het bestand met resultaten downloaden en weergeven op de pagina met resultaten van de bulkbewerking. Het bestand bevat de reden voor elke fout.

   ![Selecteer waar u de lijst wilt opnemen met de gebruikers die u wilt downloaden](./media/users-bulk-download/bulk-download.png)

   Het downloadbestand bevat de gefilterde lijst met gebruikers.

   De volgende gebruikerskenmerken zijn opgenomen:

   - userPrincipalName
   - displayName
   - surname
   - mail
   - givenName
   - objectId
   - userType
   - jobTitle
   - department
   - accountEnabled
   - usageLocation
   - streetAddress
   - staat
   - country
   - physicalDeliveryOfficeName
   - city
   - postalCode
   - telephoneNumber
   - mobiel
   - authenticationAlternativePhoneNumber
   - authenticationEmail
   - alternateEmailAddress
   - ageGroup
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Status controleren

U kunt de status van uw bulk aanvragen in behandeling bekijken op de pagina **resultaten van bulk bewerking** .

[![Controleer de status op de pagina resultaten van bulk bewerking.](./media/users-bulk-download/bulk-center.png)](./media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Service limieten bulksgewijs downloaden

Elke bulk activiteit voor het maken van een lijst met gebruikers kan Maxi maal één uur worden uitgevoerd. Hiermee kunt u een lijst van ten minste 500.000 gebruikers maken en downloaden.

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers bulksgewijs toevoegen](users-bulk-add.md)
- [Gebruikers bulksgewijs verwijderen](users-bulk-delete.md)
- [Gebruikers bulksgewijs herstellen](users-bulk-restore.md)
