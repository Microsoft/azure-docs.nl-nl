---
title: Zelf beoordeling van een toegangs pakket in het beheer van rechten van Azure AD
description: Meer informatie over het controleren van de gebruikers toegang van rechten voor toegangs pakketten in Azure Active Directory-toegangs beoordelingen (preview).
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31c44f2423cdc5c43638fe2515757bcb11a9814c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87798440"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Zelf beoordeling van een toegangs pakket in het beheer van rechten van Azure AD

Het beheer van rechten van Azure AD vereenvoudigt de manier waarop bedrijven de toegang tot groepen, toepassingen en share point-sites beheren. In dit artikel wordt beschreven hoe een gebruiker zelf de toegewezen toegangs pakket (en) kunt controleren.

## <a name="open-the-access-review"></a>Open de toegangs beoordeling

Als u een toegangs beoordeling wilt uitvoeren, moet u eerst de toegangs beoordeling openen. Gebruik de volgende procedure om de toegangs beoordeling te zoeken en te openen:

1. U ontvangt mogelijk een e-mail van micro soft waarin u wordt gevraagd de toegang te controleren. Zoek het e-mail bericht om de toegangs beoordeling te openen. Hier volgt een voor beeld van een e-mail bericht met een verzoek om een beoordeling van de toegang: 
    
    ![E-mail adres voor de Self-revisor van toegangs controle](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. Klik op de koppeling **toegang controleren** .

1. U kunt ook rechtstreeks naar https://myaccess.microsoft.com uw beoordelingen over openstaande toegang zoeken als u geen e-mail ontvangt.  (Gebruik `https://myaccess.microsoft.us` in plaats daarvan voor Amerikaanse overheid.)

1. Klik op **toegangs beoordelingen** op de linkernavigatiebalk om een lijst weer te geven met openstaande toegangs beoordelingen die aan u zijn toegewezen.


1.  Klik op het beoordeling dat u wilt beginnen.

## <a name="perform-the-access-review"></a>De toegangs beoordeling uitvoeren

Zodra u de toegangs beoordeling hebt geopend, ziet u uw toegang. Gebruik de volgende procedure om de toegangs beoordeling uit te voeren:

1.  Bepaal of u nog steeds toegang tot het toegangs pakket nodig hebt. Het project waaraan u werkt, is bijvoorbeeld niet voltooid, dus u hebt nog steeds toegang nodig om verder te kunnen werken aan het project.

1.  Klik op **Ja** om uw toegang te beperken of op **Nee** om uw toegang te verwijderen.
    >[!NOTE]
    >Als u hebt opgegeven dat u geen toegang meer nodig hebt, wordt u niet direct uit het toegangs pakket verwijderd. Wanneer de controle eindigt of als een beheerder de controle stopt, wordt het toegangs pakket verwijderd.

1.  Als u op **Ja** hebt geklikt, moet u mogelijk een instructie voor de motivering in het vak **reden** toevoegen.

1.  Klik op **Submit**

U kunt teruggaan naar de beoordeling als u van gedachten verandert en besluit uw antwoord te wijzigen voor het einde van de beoordeling.

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot toegangs pakketten controleren](entitlement-management-access-reviews-review-access.md) 
