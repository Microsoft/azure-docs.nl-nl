---
title: Problemen met voorwaardelijke toegang oplossen met behulp van de What If-Azure Active Directory
description: Waar u kunt vinden welk beleid voor voorwaardelijke toegang is toegepast en waarom
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0112ab53c501d639d3f8e0d09d82ef3a12cb93a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94837243"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>Problemen met voorwaardelijke toegang oplossen met het What If-hulp programma

Het [hulp programma What if](what-if-tool.md) in voorwaardelijke toegang is krachtig wanneer u wilt weten waarom een beleid is of niet is toegepast op een gebruiker in een specifieke omstandigheid, of dat een beleid van toepassing zou zijn op een bekende status.

Het hulp programma What if bevindt zich in de **Azure Portal**  >  **Azure Active Directory**  >  **voorwaardelijke toegang**  >  **What if**.

![What If tool voor voorwaardelijke toegang in de standaard status](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> Het What If-hulp programma evalueert momenteel geen beleids regels in de modus alleen-rapport.

## <a name="gathering-information"></a>Gegevens verzamelen

Het What If-hulp programma vereist alleen een **gebruiker** om aan de slag te gaan. 

De volgende aanvullende informatie is optioneel, maar helpt bij het beperken van het bereik voor specifieke gevallen.

* Cloud-apps of acties
* IP-adres 
* Land/regio
* Apparaatplatform
* Client-apps (preview-versie)
* Apparaatstatus (preview-versie) 
* Aanmeldingsrisico

Deze informatie kan worden verzameld van de gebruiker, het apparaat of het logboek van Azure AD-aanmeldingen.

## <a name="generating-results"></a>Resultaten genereren

Geef de criteria op die in de vorige sectie zijn verzameld en selecteer **What if** om een lijst met resultaten te genereren. 

U kunt op elk gewenst moment **opnieuw instellen** selecteren om de invoer van criteria te wissen en terug te keren naar de standaard status.

## <a name="evaluating-results"></a>Resultaten evalueren

### <a name="policies-that-will-apply"></a>Beleids regels die van toepassing zijn

In deze lijst ziet u welke beleids regels voor voorwaardelijke toegang van toepassing zijn op de voor waarden. De lijst bevat de besturings elementen Grant en Session die van toepassing zijn. Voor beelden zijn het vereisen van multi-factor Authentication om toegang te krijgen tot een specifieke toepassing.

### <a name="policies-that-will-not-apply"></a>Beleids regels die niet van toepassing zijn

In deze lijst worden de beleids regels voor voorwaardelijke toegang weer gegeven die niet van toepassing zijn als de voor waarden worden toegepast. De lijst bevat alle beleids regels en de reden waarom ze niet van toepassing zijn. Voor beelden zijn onder meer gebruikers en groepen die kunnen worden uitgesloten van een beleid.

## <a name="use-case"></a>Gebruiksvoorbeeld

Veel organisaties maken beleids regels op basis van netwerk locaties, waardoor vertrouwde locaties en het blok keren van toegang tot de locatie worden geblokkeerd.

Om te valideren of een configuratie op de juiste wijze is gemaakt, kan een beheerder het What If-hulp programma gebruiken om toegang te simuleren, vanaf een locatie die moet worden toegestaan en van een locatie die moet worden geweigerd.

[![What if-hulp programma geeft resultaten weer met toegang voor blok keren](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png#lightbox)

In dit geval heeft de gebruiker geen toegang tot de Cloud-app in de toekomst naar Noord-Korea omdat Contoso de toegang tot die locatie heeft geblokkeerd.

Deze test kan worden uitgebreid om andere gegevens punten op te nemen om het bereik te beperken.

## <a name="next-steps"></a>Volgende stappen

* [Wat is voorwaardelijke toegang?](overview.md)
* [Wat is Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md)
* [Wat is een apparaat-id?](../devices/overview.md)
* [Hoe werkt het? Azure AD-Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)