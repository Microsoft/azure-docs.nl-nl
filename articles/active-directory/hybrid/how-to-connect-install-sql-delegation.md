---
title: Azure AD Connect installeren met behulp van SQL delegated Administrator Permissions | Microsoft Docs
description: In dit onderwerp wordt een update voor Azure AD Connect beschreven die kan worden geïnstalleerd met behulp van een account dat alleen SQL dbo-machtigingen heeft.
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: jparsons
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f082ec896bf0542b63c8c1d0257679681334050
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85358664"
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Azure AD Connect installeren met SQL-gedelegeerde beheerdersmachtigingen
Voorafgaand aan de nieuwste Azure AD Connect-build, werd beheer overdracht bij het implementeren van configuraties die vereist voor SQL, niet ondersteund.  Gebruikers die Azure AD Connect nodig hadden voor het installeren van de machtigingen van de server beheerder (SA) op de SQL-Server.

Met de meest recente versie van Azure AD Connect kan de inrichting van de data base nu buiten-band worden uitgevoerd door de SQL-beheerder en vervolgens door de Azure AD Connect beheerder worden geïnstalleerd met de rechten van de data base-eigenaar.

## <a name="before-you-begin"></a>Voordat u begint
Als u deze functie wilt gebruiken, moet u er rekening mee houden dat er verschillende bewegende onderdelen zijn en dat er voor elk item een andere beheerder in uw organisatie kan worden betrokken.  De volgende tabel bevat een overzicht van de afzonderlijke rollen en hun respectieve taken bij het implementeren van Azure AD Connect met deze functie.

|Rol|Beschrijving|
|-----|-----|
|Domein-of forest AD-beheerder|Hiermee maakt u het domein niveau service account dat wordt gebruikt door Azure AD Connect om de synchronisatie service uit te voeren.  Zie [accounts and permissions](reference-connect-accounts-permissions.md)voor meer informatie over service accounts.
|SQL-beheerder|Hiermee maakt u de ADSync-data base en verleent u aanmelding en toegang tot de Azure AD Connect beheerder en het service account dat is gemaakt door de domein-of forest-beheerder.|
Azure AD Connect beheerder|Installeert Azure AD Connect en geeft het service account op tijdens een aangepaste installatie.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>Stappen voor het installeren van Azure AD Connect met behulp van SQL-gedelegeerde machtigingen
Als u de data base buiten-band wilt inrichten en Azure AD Connect wilt installeren met machtigingen voor de eigenaar van de data base, gebruikt u de volgende stappen.

>[!NOTE]
>Hoewel het niet vereist is, wordt het **ten zeerste aangeraden** om de Latin1_General_CI_AS collatie te selecteren bij het maken van de data base.


1. Laat de SQL-beheerder de ADSync-data base maken met een niet-hoofdletter gevoelige sorterings reeks **(Latin1_General_CI_AS)**.  De data base moet de naam **ADSync**.  Het herstel model, het compatibiliteits niveau en het insluitings type worden bijgewerkt naar de juiste waarden wanneer Azure AD Connect is geïnstalleerd.  De sorteer volgorde moet echter wel correct worden ingesteld door de SQL-beheerder, anders Azure AD Connect de installatie wordt geblokkeerd.  Als u de SA wilt herstellen, moet u de data base verwijderen en opnieuw maken.
 
   ![Sortering](./media/how-to-connect-install-sql-delegation/sql4.png)
2. Ken de volgende machtigingen toe aan de Azure AD Connect Administrator en het domein service account:
   - SQL-aanmelding 
   - de rechten van de **Data Base-eigenaar (dbo)** .
 
   ![Machtigingen](./media/how-to-connect-install-sql-delegation/sql3a.png)

   >[!NOTE]
   >Azure AD Connect biedt geen ondersteuning voor aanmeldingen met een genest lidmaatschap.  Dit betekent dat uw Azure AD Connect beheerders account en domein service account moeten worden gekoppeld aan een aanmelding waaraan dbo-rechten worden verleend.  Het kan niet gewoon het lid zijn van een groep die is toegewezen aan een aanmelding met dbo-rechten.

3. Stuur een e-mail naar de beheerder van de Azure AD Connect om de SQL-Server en de exemplaar naam op te geven die moeten worden gebruikt bij het installeren van Azure AD Connect.

## <a name="additional-information"></a>Aanvullende informatie
Zodra de data base is ingericht, kan de beheerder van de Azure AD Connect on-premises synchronisatie op hun gemak installeren en configureren.

Als de SQL-beheerder ADSync-data base van een vorige Azure AD Connect back-up heeft teruggezet, moet u de nieuwe Azure AD Connect-server installeren met behulp van een bestaande data base. Zie [Azure AD Connect met een bestaande ADSync-data base installeren](how-to-connect-install-existing-database.md)voor meer informatie over het installeren van Azure AD Connect met een bestaande data base.

## <a name="next-steps"></a>Volgende stappen
- [Aan de slag met Azure AD Connect met expresinstellingen](how-to-connect-install-express.md)
- [Aangepaste installatie van Azure AD Connect](how-to-connect-install-custom.md)
- [Azure AD Connect installeren met behulp van een bestaande ADSync-database](how-to-connect-install-existing-database.md)  
