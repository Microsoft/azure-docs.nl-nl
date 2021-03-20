---
title: LinkedIn-gegevens delen en toestemming-Azure Active Directory | Microsoft Docs
description: Hierin wordt uitgelegd hoe LinkedIn-Integratie gegevens deelt via micro soft-apps in Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d13ae1e5198666e115507fd91bb0fedb1c5fa64
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96545907"
---
# <a name="linkedin-account-connections-data-sharing-and-consent"></a>LinkedIn-account verbindingen gegevens delen en toestemming

U kunt gebruikers in uw Active Directory-organisatie (Azure AD) in staat stellen om toestemming te geven om hun werk-of school account van micro soft te verbinden met hun LinkedIn-account. Nadat een gebruiker zijn of haar accounts heeft verbonden, zijn de informatie en de hooglichten van LinkedIn beschikbaar in sommige micro soft-apps en-services. Gebruikers kunnen ook verwachten dat hun netwerk ervaring op LinkedIn wordt verbeterd en verrijkt met informatie van micro soft.

Voor het weer geven van LinkedIn-gegevens in micro soft-apps en-services moeten gebruikers toestemming hebben om hun eigen micro soft-en LinkedIn-accounts te verbinden. Gebruikers wordt gevraagd verbinding te maken met hun accounts de eerste keer dat ze op een profiel kaart in Outlook, OneDrive of share point online klikken om de LinkedIn-gegevens te zien. LinkedIn-account verbindingen zijn niet volledig ingeschakeld voor uw gebruikers tot ze toestemming geven voor de ervaring en verbinding maken met hun accounts.

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>Voor delen van het delen van LinkedIn-gegevens

Door toegang te krijgen tot LinkedIn-gegevens in micro soft-apps en-services kunt u uw gebruikers helpen bij het maken, gebruiken en samen stellen van professionele relaties met collega's, klanten en partners binnen en buiten uw organisatie. Nieuwe gebruikers kunnen sneller aan de slag gaan door verbinding te maken met collega's, meer over hen te leren en gemakkelijk meer informatie te krijgen. Hier volgt een voor beeld van hoe LinkedIn-informatie wordt weer gegeven op de profiel kaart in micro soft-apps:

![LinkedIn-integratie inschakelen in uw organisatie](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>LinkedIn-integratie inschakelen en aankondigen

U moet een Azure Active Directory beheerder zijn om de instelling voor uw organisatie te beheren. U kunt deze inschakelen voor alle gebruikers of voor een specifieke set gebruikers.

1. Als u de integratie wilt in-of uitschakelen, volgt u de stappen in [toestemming geven aan LinkedIn-integratie voor uw Azure AD-organisatie](linkedin-integration.md).
2. Wanneer u de LinkedIn-integratie in uw organisatie aankondigt, wijst u uw gebruikers aan de veelgestelde vragen over [LinkedIn-gegevens in micro soft-apps en-services](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381). Het artikel bevat informatie over waar LinkedIn-informatie wordt weer gegeven, [delen van gegevens en privacy](https://support.microsoft.com/office/your-data-ae9c08a7-4d06-45b5-a065-320a97bc1400), [het verbinden van accounts](https://support.microsoft.com/office/connect-your-linkedin-and-work-or-school-accounts-c7c245f2-fa56-4c9b-ba20-3fceb23c5772) en meer.

U moet LinkedIn-Integratie aankondigen aan uw gebruikers die hen alle informatie geven over het [delen van gegevens en privacy met LinkedIn-Integratie](https://support.microsoft.com/office/your-data-ae9c08a7-4d06-45b5-a065-320a97bc1400). 

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>Toestemming van de gebruiker voor toegang tot gegevens in micro soft en LinkedIn

Gegevens die worden geopend vanuit LinkedIn, worden niet permanent opgeslagen in micro soft-Services. Gegevens die worden geopend vanuit micro soft, worden niet permanent opgeslagen met LinkedIn, met uitzonde ring van contact personen. Micro soft-contact personen worden op LinkedIn opgeslagen totdat gebruikers ze verwijderen, zoals wordt beschreven in [geïmporteerde contact personen verwijderen van LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Wanneer gebruikers verbinding maken met hun accounts, zijn informatie en inzichten van LinkedIn beschikbaar in sommige micro soft-apps, zoals de profiel kaart. Gebruikers kunnen ook verwachten dat hun netwerk ervaring op LinkedIn wordt verbeterd en verrijkt met informatie van micro soft.
Wanneer gebruikers in uw organisatie hun LinkedIn-en micro soft work-of school-account verbinden, hebben ze twee opties:

* Toestemming geven om gegevens te openen vanuit beide accounts. Dit betekent dat ze toestemming geven voor hun micro soft-of werk account om toegang te krijgen tot gegevens van hun LinkedIn-account, en voor [hun LinkedIn-account om toegang te krijgen tot gegevens van hun werk-of school account van micro soft](https://www.linkedin.com/help/linkedin/answer/84077).
* Geef alleen toegang tot de LinkedIn-gegevens door hun micro soft-werk-en school account.

Gebruikers kunnen accounts verbinding verbreken en de toegangs machtigingen voor gegevens op elk gewenst moment verwijderen, en [gebruikers kunnen bepalen hoe hun eigen LinkedIn-profiel wordt weer gegeven](https://www.linkedin.com/help/linkedin/answer/83), met inbegrip van of het profiel kan worden weer gegeven in micro soft-apps.

### <a name="linkedin-account-data"></a>LinkedIn-account gegevens

Wanneer u uw micro soft-en LinkedIn-accounts verbindt, staat u toe dat LinkedIn de volgende gegevens aan micro soft verstrekt:

* Profiel gegevens: bevat de LinkedIn-identiteit, de contact gegevens en de informatie die u met anderen deelt in uw [LinkedIn-profiel](https://www.linkedin.com/help/linkedin/answer/15493).
* Belangen gegevens: bevat interesses op LinkedIn, zoals mensen en onderwerpen die u volgt, cursussen groepen en inhoud die u wilt en delen.
* Abonnementen gegevens: bevat abonnementen op LinkedIn-toepassingen en-services, samen met de bijbehorende gegevens. 
* Verbindingen gegevens: bevat uw [LinkedIn-netwerk](https://www.linkedin.com/help/linkedin/answer/110) , inclusief profielen en contact gegevens van uw verbindingen met een 1e graad.

Gegevens die worden geopend vanuit LinkedIn, worden niet permanent opgeslagen in micro soft-Services. Zie de [privacyverklaring van micro soft](https://privacy.microsoft.com/privacystatement/)voor meer informatie over het gebruik van persoonlijke gegevens van micro soft.

### <a name="microsoft-work-or-school-account-data"></a>Gegevens van micro soft-werk-of school account

Wanneer u uw micro soft-en LinkedIn-accounts verbindt, stelt u micro soft in staat om de volgende gegevens te verstrekken aan LinkedIn:

* Profiel gegevens: bevat informatie zoals uw voor naam, achternaam, profiel foto, e-mail adres, Manager en personen die u beheert.
* Agenda gegevens: bevat vergaderingen in uw agenda's, hun tijden, locaties en contact gegevens van deel nemers. Informatie over de vergadering, zoals agenda, inhoud of titel van de vergadering, is niet opgenomen in de agenda gegevens.
* Belangen gegevens: bevat de interesses die zijn gekoppeld aan uw account, op basis van uw gebruik van micro soft-Services, zoals Cortana en Bing voor bedrijven.
* Abonnementen gegevens: bevat abonnementen die door uw organisatie worden geleverd aan micro soft-apps en-services, zoals Microsoft 365.
* Gegevens van contact personen: bevat contact lijsten in Outlook, Skype en andere Microsoft-account services, met inbegrip van de contact gegevens voor personen met wie u regel matig communiceert of waarmee u samenwerkt. Contact personen worden regel matig geïmporteerd, opgeslagen en gebruikt door LinkedIn, bijvoorbeeld om verbindingen voor te stellen, contact personen te organiseren en updates over contact personen weer te geven.

Gegevens die worden geopend vanuit micro soft, worden niet permanent opgeslagen met LinkedIn, met uitzonde ring van contact personen. Micro soft-contact personen worden op LinkedIn opgeslagen totdat gebruikers ze verwijderen. Meer informatie over het [verwijderen van geïmporteerde contact personen van LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Zie het [LinkedIn-privacybeleid](https://www.linkedin.com/legal/privacy-policy)voor meer informatie over het gebruik van persoonlijke gegevens van LinkedIn. Voor LinkedIn-Services, gegevens overdracht en opslag kunnen gegevens van de Europese Unie naar het Verenigde Staten en terug worden gestroomd en wordt uw privacy beschermd zoals beschreven in [gegevens overdrachten van de Europese Unie](https://www.linkedin.com/help/linkedin/answer/62533).

## <a name="next-steps"></a>Volgende stappen

* [LinkedIn in micro soft-toepassingen met uw werk-of school account](https://www.linkedin.com/help/linkedin/answer/84077)
