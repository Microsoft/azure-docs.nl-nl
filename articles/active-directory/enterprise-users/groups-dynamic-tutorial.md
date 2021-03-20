---
title: Gebruikers toevoegen aan een dynamische groep - zelfstudie - Azure AD | Microsoft Docs
description: In deze zelfstudie gebruikt u groepen met gebruikerslidmaatschapsregels voor het automatisch toevoegen of verwijderen van gebruikers
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: tutorial
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc891a7b07305fc0ef55376d7b6fbb262d740bb5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96547488"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>Zelfstudie: Automatisch groepsleden toevoegen of verwijderen

In Azure Active Directory (Azure AD) kunt u automatisch gebruikers aan beveiligingsgroepen of Microsoft 365-groepen toevoegen of daaruit verwijderen, zodat u dit niet altijd handmatig hoeft te doen. Wanneer eigenschappen van een gebruiker of apparaat worden gewijzigd, worden alle dynamische groepsregels in uw Azure AD-organisatie door Microsoft Azure Active Directory geëvalueerd om te zien of er leden moeten worden toegevoegd of verwijderd.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een automatisch ingevulde groep gastgebruikers van een partnerbedrijf maken
> * Licenties aan de groep toewijzen voor de partnerspecifieke functies die toegankelijk moeten zijn voor de gastgebruikers
> * Optioneel: de groep **Alle gebruikers** beveiligen door gastgebruikers te verwijderen zodat bijvoorbeeld alleen lidgebruikers toegang hebben tot interne sites

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor deze functie moet u, als globale beheerder van de organisatie, beschikken over een Azure Active Directory Premium-licentie. Als u nog geen licentie hebt, selecteert u in Microsoft Azure Active Directory **Licenties** > **Producten** > **Proberen/kopen**.

U bent niet verplicht licenties aan de gebruikers toe te wijzen om ze op te nemen als leden in dynamische groepen. U hebt slechts het minimum aantal beschikbare licenties voor Azure Active Directory Premium P1 in de organisatie nodig voor dergelijke gebruikers. 

## <a name="create-a-group-of-guest-users"></a>Een groep gastgebruikers maken

Eerst maakt u een groep voor uw gastgebruikers, die alle afkomstig zijn van één partnerbedrijf. Omdat zij speciale licenties nodig hebben, is het vaak efficiënter om een groep te maken voor dit doel.

1. Meld u bij Azure Portal aan (https://portal.azure.com) ) met het account van de globale beheerder voor uw organisatie.
2. Selecteer **Azure Active Directory** > **Groepen** > **Nieuwe groep**.
   ![Een opdracht selecteren om een nieuwe groep te starten](./media/groups-dynamic-tutorial/new-group.png)
3. Ga als volgt te werk op de blade **Groep**:
  
   * Selecteer **Beveiliging** als het groepstype.
   * Voer `Guest users Contoso` in als de naam en beschrijving voor de groep.
   * Wijzig **Lidmaatschapstype** in **Dynamische gebruiker**.
   
4. Selecteer **Eigenaren** en zoek in de blade **Eigenaren toevoegen** naar de gewenste eigenaren. Klik op de gewenste eigenaren om ze aan de selectie toe te voegen.
5. Klik op **Selecteren** om de blade **Eigenaren toevoegen** te sluiten.  
6. Selecteer **Dynamische query bewerken** in het vak **Dynamische gebruikersleden**.
7. Ga als volgt te werk op de blade **Dynamisch-lidmaatschapregels**:

   * Klik in het veld **Eigenschap** op de bestaande waarde en selecteer **userType**. 
   * Controleer of voor het veld **Operator** **Is gelijk aan** is geselecteerd.  
   * Selecteer het veld **Waarde** veld en voer **Gast** in. 
   * Klik op de hyperlink **Expressie toevoegen** om een nieuwe regel toe te voegen.
   * In het veld **En/of** selecteert u **En**.
   * In het veld **Eigenschap** selecteert u **companyName**.
   * Controleer of voor het veld **Operator** **Is gelijk aan** is geselecteerd.
   * Voer in het veld **Waarde** **Contoso** in.
   * Klik op **Opslaan** om de blade **Dynamisch-lidmaatschapsregels** te sluiten.
   
8. Selecteer, op de blade **Groep**, **Maken** om de groep te maken.

## <a name="assign-licenses"></a>Licenties toewijzen

Nu u de nieuwe groep hebt gemaakt, kunt u de benodigde licenties aan deze partnergebruikers toewijzen.

1. Selecteer **Licenties** in Microsoft Azure Active Directory, selecteer een of meer licenties, en selecteer vervolgens **Toewijzen**.
2. Selecteer **Gebruikers en groepen**, selecteer de groep **Gastgebruikers Contoso** en sla uw wijzigingen op.
3. Bij **Toewijzingsopties** kunt u de abonnementen bij de geselecteerde licenties in- en uitschakelen. Wanneer u een wijziging aanbrengt, moet u op **OK** klikken uw wijzigingen op te slaan.
4. Voltooi de toewijzing door onder aan het deelvenster **Licentie toewijzen** op **Toewijzen** te klikken.

## <a name="remove-guests-from-all-users-group"></a>Gastgebruikers verwijderen uit de groep Alle gebruikers

Mogelijk wilt u als beheerder uiteindelijk alle gastgebruikers per bedrijf toewijzen aan een afzonderlijke groep. U kunt echter nu ook de groep **Alle gebruikers** zo wijzigen, dat deze is voorbehouden aan lidgebruikers in uw organisatie. Vervolgens kunt u aan deze groep apps en licenties toewijzen die specifiek zijn voor uw organisatie.

   ![De groep Alle gebruikers zo wijzigen dat er alleen leden overblijven](./media/groups-dynamic-tutorial/all-users-edit.png)

## <a name="clean-up-resources"></a>Resources opschonen

**De groep Gastgebruikers verwijderen**

1. Meld u bij [Azure Portal](https://portal.azure.com) aan met een account van de globale beheerder voor uw organisatie.
2. Selecteer **Azure Active Directory** > **Groepen**. Selecteer de groep **Gastgebruikers Contoso**, selecteer het weglatingsteken (...) en selecteer vervolgens **Verwijderen**. Wanneer u de groep verwijdert, worden alle toegewezen licenties verwijderd.

**De groep Alle gebruikers herstellen**
1. Selecteer **Azure Active Directory** > **Groepen**. Selecteer de naam van de groep **Alle gebruikers** om de groep te openen.
1. Selecteer **Dynamisch-lidmaatschapregels**, verwijder alle tekst in de regel en selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:
> [!div class="checklist"]
> * Een groep gastgebruikers maken
> * Licenties toewijzen aan uw nieuwe groep
> * De groep Alle gebruikers zo wijzigen dat er alleen leden overblijven

Ga naar het volgende artikel voor meer informatie over de basisprincipes van groepslicenties
> [!div class="nextstepaction"]
> [Basisprincipes van groepslicenties](../fundamentals/active-directory-licensing-whatis-azure-portal.md)



