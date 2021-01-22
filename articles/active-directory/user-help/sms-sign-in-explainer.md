---
title: SMS-aanmeld gebruikers ervaring voor telefoon nummer-Azure AD
description: Meer informatie over de gebruikers ervaring voor SMS-aanmelding voor nieuwe of bestaande telefoon nummers
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/21/2021
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 1a50f2032a978a552205d1bba602249f34f0478a
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661586"
---
# <a name="use-your-phone-number-as-a-user-name"></a>Je telefoon nummer gebruiken als gebruikers naam

Wanneer u een apparaat registreert, krijgt u toegang tot de services van uw organisatie en kunt u uw organisatie geen toegang geven tot uw telefoon. Als u een beheerder bent, kunt u meer informatie vinden in [configureren en gebruikers inschakelen voor verificatie op basis van SMS](../authentication/howto-authentication-sms-signin.md).

Als uw organisatie geen SMS-aanmelding beschikbaar heeft gemaakt, wordt er geen optie voor weer gegeven wanneer u een telefoon registreert bij uw account.  

## <a name="when-you-have-a-new-phone-number"></a>Wanneer u een nieuw telefoon nummer hebt

Als u een nieuwe telefoon of nieuw nummer krijgt en u registreert bij een organisatie waarvoor SMS-aanmelding beschikbaar is, wordt het normale registratie proces voor de telefoon uitgevoerd:

1. Selecteer **methode toevoegen**.
1. Selecteer **telefoon**.
1. Voer het telefoon nummer in en selecteer **SMS me een code**.
1. Nadat u de code hebt ingevoerd, selecteert u **volgende**.
1. Er wordt een prompt weer gegeven met de tekst "SMS geverifieerd. De registratie van uw telefoon is voltooid. "

> [!Important]
> Als gevolg van een bekend probleem kan het toevoegen van het telefoon nummer voor de korte tijd niet het aantal voor SMS-aanmelding registreren. U moet zich aanmelden met het toegevoegde nummer en vervolgens de prompts volgen om het nummer voor SMS-aanmelding te registreren.

### <a name="when-the-phone-number-is-in-use"></a>Wanneer het telefoon nummer wordt gebruikt

Als u probeert een telefoon nummer te gebruiken dat iemand anders in uw organisatie gebruikt, wordt het volgende bericht weer gegeven:

![Fout bericht wanneer uw telefoon nummer al wordt gebruikt](media/sms-sign-in-explainer/sms-sign-in-error.png)

Neem contact op met uw beheerder om het probleem te verhelpen.

## <a name="when-you-have-an-existing-number"></a>Wanneer u een bestaand nummer hebt

Als u al een telefoon nummer met een organisatie gebruikt en u uw telefoon nummer gebruikt terwijl er een gebruikers naam beschikbaar is, kunt u zich aan de hand van de volgende stappen aanmelden.

1. Wanneer SMS-aanmelding beschikbaar is, wordt er een banner weer gegeven waarin u wordt gevraagd of u het telefoon nummer voor SMS-aanmelding wilt inschakelen:

    :::image type="content" source="media/sms-sign-in-explainer/sms-sign-in-banner.png" alt-text="Scherm opname van de banner om SMS-aanmelding in te scha kelen voor een telefoon nummer waarbij de actie inschakelen is geselecteerd." lightbox="media/sms-sign-in-explainer/sms-sign-in-banner.png":::

1. Er wordt ook een knop **inschakelen** weer gegeven als u het caret selecteert op de tegel telefoon methode:

    [![Banner om SMS-aanmelding in te scha kelen voor een telefoon nummer.](media/sms-sign-in-explainer/sms-sign-in-phone-method.png)](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. Selecteer **inschakelen** om de methode in te scha kelen. U wordt gevraagd de actie te bevestigen:

    ![Bevestigings venster om SMS-aanmelding voor een telefoon nummer in te scha kelen](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. Selecteer **Inschakelen**.

## <a name="when-you-remove-your-phone-number"></a>Wanneer u uw telefoon nummer verwijdert

1. Als u het telefoon nummer wilt verwijderen, selecteert u de knop verwijderen op de tegel SMS-aanmeldings methode.

    [![Banner voor het verwijderen van SMS-aanmelding voor een telefoon nummer.](media/sms-sign-in-explainer/sms-sign-in-delete-method.png)](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. Wanneer u wordt gevraagd om de actie te bevestigen, selecteert u **OK**.

U kunt geen telefoon nummer verwijderen dat in gebruik is als de standaard methode voor het aanmelden. Als u het nummer wilt verwijderen, moet u de standaard aanmeldings methode wijzigen en vervolgens het telefoon nummer opnieuw verwijderen.
