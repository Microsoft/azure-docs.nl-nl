---
title: Quickstart voor verloopbeleid voor groepen - Azure AD | Microsoft Docs
description: Verloop voor Microsoft 365-groepen - Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4eeaa98cb19b119e4da2af7bee39b5aebd303485
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063148"
---
# <a name="quickstart-set-microsoft-365-groups-to-expire-in-azure-active-directory"></a>Microsoft 365-groepen voor verloop instellen in Azure Active Directory

In deze quickstart stelt u het verloopbeleid in voor uw Microsoft 365-groepen. Wanneer gebruikers hun eigen groepen kunnen instellen, kunnen niet-gebruikte groepen zich vermenigvuldigen. Eén manier om niet-gebruikte groepen te beheren, is door verloopbeleid voor deze groepen in te stellen om het handmatig verwijderen van groepen te verminderen.

Verloopbeleid maken is eenvoudig:

- Groepen met gebruikersactiviteiten worden automatisch vernieuwd als de verloopdatum nadert
- Groepseigenaren worden op de hoogte gesteld als een groep moet worden vernieuwd die op het punt staat te verlopen
- Een groep die niet wordt vernieuwd, wordt verwijderd
- Een verwijderde Microsoft 365-groep kan binnen dertig dagen door een groepseigenaar of een Azure AD-beheerder worden hersteld

> [!NOTE]
> Voor groepen wordt nu Azure AD Intelligence gebruikt voor automatische verlenging op basis van het feit of ze recent zijn gebruikt. Deze verlengingsbeslissing is gebaseerd op gebruikersactiviteit in groepen in Microsoft 365-services als Outlook, SharePoint, Teams, Yammer en andere.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisite"></a>Vereiste

 De minst bevoorrechte rol die nodig is om een groepsverloop in te stellen is de gebruikersbeheerder in de organisatie.

## <a name="turn-on-user-creation-for-groups"></a>Maken van gebruikers voor groepen inschakelen

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een gebruikersbeheerdersaccount.

2. Selecteer **Groepen** en vervolgens **Algemeen**.
  
   ![De pagina Groepsinstellingen voor selfservice](./media/groups-quickstart-expiration/self-service-settings.png)

3. Stel **Gebruikers kunnen Microsoft 365-groepen maken** in op **Ja**.

4. Selecteer **Opslaan** om de groepsinstellingen op te slaan als u klaar bent.

## <a name="set-group-expiration"></a>Verloopdatum van de groep instellen

1. Selecteer in [Azure Portal](https://portal.azure.com) de opties **Azure Active Directory** > **Groepen** > **Verlooptijd** om de instellingen voor het verloop te openen.
  
   ![De pagina Verloopinstellingen voor groep](./media/groups-quickstart-expiration/expiration-settings.png)

2. Stel het verloopinterval in. Selecteer een vooraf ingestelde waarde of voer een aangepaste waarde van meer dan 31 dagen in. 

3. Geef een e-mailadres op waar meldingen over verlooptijden naartoe moeten worden gestuurd als een groep geen eigenaar heeft.

4. Stel voor deze quickstart **Verloop inschakelen voor deze Microsoft 365-groepen** in op **Alle**.

5. Selecteer **Opslaan** om de instellingen voor het verloop op te slaan als u klaar bent.

Dat is alles. In deze quickstart hebt u het verloopbeleid ingesteld voor de geselecteerde Microsoft 365-groepen.

## <a name="clean-up-resources"></a>Resources opschonen

### <a name="to-remove-the-expiration-policy"></a>Verloopbeleid verwijderen

1. Zorg dat u bent aangemeld bij [Azure Portal](https://portal.azure.com) met een account van een globale beheerder voor uw Azure AD-organisatie.
2. Selecteer **Azure Active Directory** > **Groepen** > **Verlooptijd**.
3. Stel **Verloopdatum voor deze Microsoft 365-groepen inschakelen** in op **Geen**.

### <a name="to-turn-off-user-creation-for-groups"></a>Maken van gebruikers voor groepen uitschakelen

1. Selecteer **Azure Active Directory** > **Groepen** > **Algemeen**. 
2. Stel **Gebruikers kunnen Microsoft 365-groepen maken in Azure Portals** in op **Nee**.

## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel voor meer informatie over de verloopdatum, inclusief instructies voor PowerShell en technische beperkingen:

> [!div class="nextstepaction"]
> [Verloopbeleid maken voor PowerShell](groups-lifecycle.md)
