---
title: Toegang tot Azure AD-app proxy-apps in teams | Microsoft Docs
description: Gebruik Azure AD-toepassingsproxy om toegang te krijgen tot uw on-premises toepassing via micro soft teams.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/05/2017
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c44716f045340022c871501609cf582015ba20f
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256606"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Toegang tot uw on-premises toepassingen via micro soft teams

Azure Active Directory-toepassingsproxy biedt u eenmalige aanmelding voor on-premises toepassingen, waar u ook bent. Micro soft teams stroomlijnt uw samenwerkings inspanningen op één plek. Als u de twee elkaar integreert, kunnen uw gebruikers in elke situatie productief zijn met hun team leden.

Uw gebruikers kunnen Cloud-apps toevoegen aan hun teams kanalen [met behulp van tabs](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US), maar wat zijn de share point-sites of het plannings programma die on-premises worden gehost? Toepassings proxy is de oplossing. Ze kunnen apps die zijn gepubliceerd via een toepassings proxy toevoegen aan hun kanalen met behulp van dezelfde externe Url's die ze altijd gebruiken voor toegang tot hun apps op afstand. En omdat toepassings proxy wordt geverifieerd via Azure Active Directory, krijgen uw gebruikers een eenmalige aanmelding.

## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>De connector voor de toepassings proxy installeren en uw app publiceren

Als u dat nog niet hebt gedaan, [configureert u de toepassings proxy voor uw Tenant en installeert u de connector](application-proxy-add-on-premises-application.md). Publiceer vervolgens [uw on-premises toepassing](application-proxy-add-on-premises-application.md) voor externe toegang. Wanneer u de app publiceert, noteert u de externe URL, omdat deze wordt gebruikt om de app toe te voegen aan teams.

Als u uw apps al hebt gepubliceerd, maar geen externe Url's meer weet, kunt u deze bekijken in de [Azure Portal](https://portal.azure.com). Meld u aan en ga naar **Azure Active Directory**  >  **Enter prise-toepassingen**  >  **alle toepassingen** > Selecteer uw app > **toepassings proxy**.

## <a name="add-your-app-to-teams"></a>Uw app toevoegen aan teams

Zodra u de app via toepassings proxy hebt gepubliceerd, laat u uw gebruikers weten dat ze deze kunnen toevoegen als een tabblad rechtstreeks in hun teams-kanalen en dat de app beschikbaar is voor iedereen in het team. Voer de volgende drie stappen uit:

1. Navigeer naar het teams-kanaal waar u deze app wilt toevoegen en selecteer **+** om een tabblad toe te voegen.

   ![Selecteer + om een tabblad aan teams toe te voegen](./media/application-proxy-integrate-with-teams/add-tab.png)

1. Selecteer **website** op de tabblad Opties.

   ![Website selecteren in het scherm een tabblad toevoegen](./media/application-proxy-integrate-with-teams/website.png)

1. Geef het tabblad een naam en stel de URL in op de externe URL van de toepassings proxy.

   ![Geef het tabblad een naam en voeg de externe URL toe](./media/application-proxy-integrate-with-teams/tab-name-url.png)

Zodra een lid van een team het tabblad toevoegt, wordt het weer gegeven voor iedereen in het kanaal. Gebruikers die toegang hebben tot de app, krijgen toegang tot eenmalige aanmelding met de referenties die ze gebruiken voor micro soft teams. Gebruikers die geen toegang hebben tot de app, kunnen het tabblad zien in teams, maar worden geblokkeerd totdat u ze machtigingen geeft voor de on-premises app en de Azure Portal gepubliceerde versie van de app.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [publiceren van on-premises share point-sites](application-proxy-integrate-with-sharepoint-server.md) met toepassings proxy.
- Configureer uw apps voor het gebruik van [aangepaste domeinen](application-proxy-configure-custom-domain.md) voor hun externe URL.
