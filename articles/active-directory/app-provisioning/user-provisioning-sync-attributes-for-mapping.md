---
title: Kenmerken synchroniseren met Azure AD voor toewijzing
description: Meer informatie over het synchroniseren van kenmerken van uw on-premises Active Directory naar Azure AD. Bij het configureren van gebruikers inrichting voor SaaS-apps, gebruikt u de functie Directory-extensie om bron kenmerken toe te voegen die niet standaard worden gesynchroniseerd.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/13/2019
ms.author: kenwith
ms.openlocfilehash: 62d035b85850f8ac455a85fd93e4d081bbd386e1
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256082"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Een kenmerk van uw on-premises Active Directory naar Azure AD synchroniseren voor het inrichten van een toepassing

Bij het aanpassen van kenmerk toewijzingen voor het inrichten van gebruikers, is het mogelijk dat het kenmerk dat u wilt toewijzen niet wordt weer gegeven in de lijst met **bron kenmerken** . Dit artikel laat u zien hoe u het ontbrekende kenmerk kunt toevoegen door het te synchroniseren van uw on-premises Active Directory (AD) naar Azure Active Directory (Azure AD).

Azure AD moet alle gegevens bevatten die nodig zijn voor het maken van een gebruikers profiel bij het inrichten van gebruikers accounts van Azure AD naar een SaaS-app. In sommige gevallen kan het nodig zijn om de gegevens te synchroniseren van uw on-premises AD naar Azure AD. Azure AD Connect worden automatisch bepaalde kenmerken gesynchroniseerd met Azure AD, maar niet alle kenmerken. Bovendien worden sommige kenmerken (zoals SAMAccountName) die standaard worden gesynchroniseerd, mogelijk niet weer gegeven met de Microsoft Graph-API. In dergelijke gevallen kunt u de functie Directory-extensie Azure AD Connect gebruiken om het kenmerk te synchroniseren met Azure AD. Op die manier is het kenmerk zichtbaar voor de Microsoft Graph-API en de Azure AD-inrichtings service.

Als de gegevens die u nodig hebt voor het inrichten, zich in Active Directory bevinden, maar niet beschikbaar is om te worden ingericht vanwege de bovenstaande redenen, volgt u deze stappen.
 
## <a name="sync-an-attribute"></a>Een kenmerk synchroniseren 

1. Open de wizard Azure AD Connect, kies taken en kies vervolgens **synchronisatie opties aanpassen**.

   ![Pagina extra taken Azure Active Directory Connect wizard](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Meld u aan als een globale Azure AD-beheerder. 

3. Selecteer op de pagina **optionele functies** de optie **synchronisatie van Directory-extensie kenmerken**.
 
   ![Pagina optionele functies van Azure Active Directory Connect wizard](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Selecteer de kenmerk (en) die u wilt uitbreiden naar Azure AD.
   > [!NOTE]
   > De zoek opdracht onder **beschik bare kenmerken** is hoofdletter gevoelig.

   ![Scherm afbeelding van de selectie pagina Directory-extensies](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Voltooi de Azure AD Connect wizard en sta toe dat een volledige synchronisatie cyclus kan worden uitgevoerd. Wanneer de cyclus is voltooid, wordt het schema uitgebreid en worden de nieuwe waarden gesynchroniseerd tussen uw on-premises AD en Azure AD.
 
6. In de Azure Portal, terwijl u de [toewijzingen van gebruikers kenmerken bewerkt](customize-application-attributes.md), bevat de lijst met **bron kenmerken** nu het kenmerk toegevoegd in de indeling `<attributename> (extension_<appID>_<attributename>)` . Selecteer het kenmerk en wijs dit toe aan de doel toepassing voor inrichting.

   ![Pagina Azure Active Directory Connect Directory uitbreidingen selecteren](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> De mogelijkheid om referentie kenmerken in te richten vanuit een on-premises AD, zoals **managedby** of **DN/DN**, wordt momenteel niet ondersteund. U kunt deze functie aanvragen op de [gebruikers stem](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="next-steps"></a>Volgende stappen

* [Definiëren wie binnen het bereik van de inrichting valt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
