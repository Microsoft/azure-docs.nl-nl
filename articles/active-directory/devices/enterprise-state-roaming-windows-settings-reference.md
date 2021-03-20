---
title: Referentie voor Windows 10-instellingen voor roaming-Azure Active Directory
description: Instellingen die worden geroamd of waarvan een back-up wordt gemaakt in Windows 10 met behulp van ESR
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6048ee9237640799b7bec37083e607fc74ffb8e4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85252964"
---
# <a name="windows-10-roaming-settings-reference"></a>Naslaginformatie over roaminginstellingen voor Windows 10

Hier volgt een lijst met de instellingen die worden geroamd of waarvan een back-up wordt gemaakt in Windows 10. 

## <a name="devices-and-endpoints"></a>Apparaten en eind punten

Raadpleeg de volgende tabel voor een overzicht van de apparaten en account typen die worden ondersteund door het Framework Sync, backup en Restore in Windows 10.

| Account type en-bewerking | Bureaublad | Mobiel |
| --- | --- | --- |
| Azure Active Directory: synchroniseren |Ja |Nee |
| Azure Active Directory: back-up/herstellen |Nee |Nee |
| Microsoft-account: synchroniseren |Ja |Ja |
| Microsoft-account: back-up/herstellen |Nee |Ja |

## <a name="what-is-backup"></a>Wat is een back-up?

Windows-instellingen worden doorgaans standaard gesynchroniseerd, maar sommige instellingen worden alleen van een back-up gemaakt, zoals de lijst met geïnstalleerde toepassingen op een apparaat. Backup is alleen voor mobiele apparaten en is momenteel niet beschikbaar voor gebruikers van Enterprise State Roaming. Backup maakt gebruik van een Microsoft-account en slaat de instellingen en toepassings gegevens op in OneDrive. Als een gebruiker synchronisatie op het apparaat uitschakelt via de app instellingen, worden toepassings gegevens die normaal gesp roken alleen back-ups. Back-upgegevens kunnen alleen worden geopend via de herstel bewerking tijdens de eerste uitvoering van een nieuw apparaat. U kunt back-ups uitschakelen via de apparaatinstellingen en kunnen worden beheerd en verwijderd via het OneDrive-account van de gebruiker.

## <a name="windows-settings-overview"></a>Overzicht van Windows-instellingen

De volgende instellingen groepen zijn beschikbaar voor eind gebruikers om synchronisatie-instellingen op Windows 10-apparaten in of uit te scha kelen.

* Thema: bureaublad achtergrond, gebruikers tegel, taak balk positie, enzovoort 
* Instellingen voor Internet Explorer: browse geschiedenis, getypte Url's, favorieten enzovoort 
* Wacht woorden: Windows referentie beheer, met inbegrip van Wi-Fi profielen 
* Taal voorkeuren: spelling woordenlijst, systeem taal instellingen 
* Toegankelijkheid: verteller, scherm toetsenbord, vergroot glas 
* Andere Windows-instellingen: Zie Windows-instellingen Details
* Micro soft Edge-browser instelling: micro soft Edge-favorieten, lees lijst en andere instellingen

![Uw instellingen synchroniseren](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-syncyoursettings.png)

> [!NOTE]
> Dit artikel is van toepassing op de op HTML gebaseerde browser van micro soft Edge die is geïntroduceerd in Windows 10 in juli 2015. Het artikel is niet van toepassing op de nieuwe micro soft Edge-browser op basis van chroom, uitgebracht op 15 januari 2020. Zie het artikel [micro soft Edge Sync](/deployedge/microsoft-edge-enterprise-sync)(Engelstalig) voor meer informatie over het synchronisatie gedrag voor de nieuwe micro soft Edge.

De instellings groep van micro soft Edge-browser (favorieten, lees lijst) kan worden ingeschakeld of uitgeschakeld door eind gebruikers via de menu optie micro soft Edge-browser instellingen.

![Account](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-edge.png)

Voor Windows 10 versie 1803 of hoger kan de instellings groep voor Internet Explorer (favorieten, getypte Url's) worden ingeschakeld of uitgeschakeld door eind gebruikers via de menu optie instellingen van Internet Explorer. 

![Instellingen](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-ie.png)

## <a name="windows-settings-details"></a>Details van Windows-instellingen

In de volgende tabel verwijzen andere vermeldingen in de kolom instellingen groep naar instellingen die kunnen worden uitgeschakeld door te gaan naar instellingen > accounts > uw instellingen te synchroniseren > andere Windows-instellingen. 

Interne vermeldingen in de kolom instellingen groep verwijzen naar instellingen en apps die alleen kunnen worden uitgeschakeld voor synchronisatie binnen de app zelf of door synchronisatie uit te scha kelen voor het hele apparaat met behulp van Mobile Device Management (MDM) of groepsbeleid instellingen.
Instellingen die niet worden geroamd of gesynchroniseerd, behoren niet tot een groep.

| Instellingen | Bureaublad | Mobiel | Groep |
| --- | --- | --- | --- |
| **Accounts**: account afbeelding |synchroniseren |X |Thema |
| **Accounts**: andere account instellingen |X |X | |
| **Geavanceerde mobiele breedband**: netwerk naam voor delen van Internet verbinding (automatische detectie van mobiele Wi-Fi HOTS pots via Bluetooth inschakelen) |X |X |Wachtwoorden |
| **App-gegevens**: individuele apps kunnen gegevens synchroniseren |back-up synchroniseren |back-up synchroniseren |binnen |
| **App-lijst**: lijst met geïnstalleerde apps |X |Back-up |Anders |
| **Bluetooth**: alle Bluetooth-instellingen |X |X | |
| **Opdracht prompt**: instellingen voor opdracht prompt "defaults" |synchroniseren |X |binnen |
| **Referenties**: referentie kluis |synchroniseren |synchroniseren |wachtwoord |
| **Datum, tijd en regio**: automatische tijd (synchronisatie op internet tijd) |synchroniseren |synchroniseren |language |
| **Datum, tijd en regio**: 24-uurs klok |synchroniseren |X |language |
| **Datum, tijd en regio**: datum en tijd |synchroniseren |X |language |
| **Datum, tijd en regio**: tijd zone | |X |language |
| **Datum, tijd en regio**: zomer tijd |synchroniseren |X |language |
| **Datum, tijd en regio**: land/regio |synchroniseren |X |language |
| **Datum, tijd en regio**: eerste dag van de week |synchroniseren |X |language |
| **Datum, tijd en regio**: regio-indeling (land instelling) |synchroniseren |X |language |
| **Datum, tijd en regio**: korte datum |synchroniseren |X |language |
| **Datum, tijd en regio**: lange datum notatie |synchroniseren |X |language |
| **Datum, tijd en regio**: korte tijd |synchroniseren |X |language |
| **Datum, tijd en regio**: lange tijd |synchroniseren |X |language |
| **Bureaublad personalisatie**: bureaublad thema (achtergrond, systeem kleur, standaard systeem geluiden, scherm beveiliging) |synchroniseren |X |Thema |
| **Bureaublad personalisatie**: achtergrond van diavoorstelling |synchroniseren |X |Thema |
| **Bureaublad personalisatie**: taak balk instellingen (positie, automatisch verbergen, enzovoort) |synchroniseren |X |Thema |
| **Persoonlijke instellingen voor het bureau blad**: de indeling van het Start scherm |X |Back-up | |
| **Apparaten**: gedeelde printers waarmee u verbinding hebt |X |X |other |
| **Micro soft Edge-browser**: Lees lijst |synchroniseren |synchroniseren |binnen |
| **Micro soft Edge-browser**: Favorieten |synchroniseren |synchroniseren |binnen |
| **Micro soft Edge-browser**: sites op het hoogste niveau <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |binnen |
| **Micro soft Edge-browser**: getypte url's <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |binnen |
| **Micro soft Edge-browser**: instellingen voor de werk balk Favorieten <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |binnen |
| **Micro soft Edge-browser**: de knop Start weer geven <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |binnen |
| **Micro soft Edge-browser**: pop-ups blok keren <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |binnen |
| **Micro soft Edge-browser**: vraag mij wat te doen met elke down load <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |binnen |
| **Micro soft Edge-browser**: aanbieding voor het opslaan van wacht woorden <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |binnen |
| **Micro soft Edge-browser**: aanvragen voor verzenden niet bijhouden <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |binnen |
| **Micro soft Edge-browser**: formulier vermeldingen opslaan <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |binnen |
| **Micro soft Edge-browser**: Zoek-en site suggesties weer geven als ik typ <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |binnen |
| **Micro soft Edge-browser**: cookies-voor keur <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |binnen |
| **Micro soft Edge-browser**: laat websites beveiligde media licenties opslaan op mijn apparaat <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |binnen |
| **Micro soft Edge-browser**: scherm lezer-instelling <sup> [[1]](#footnote-1)</sup> |synchroniseren |synchroniseren |binnen |
| **Hoog contrast**: in-of uitschakelen |synchroniseren |X |toegankelijkheid |
| **Hoog contrast**: thema-instellingen |synchroniseren |X |toegankelijkheid |
| **Internet Explorer**: open TABBLADEN (URL en titel) |synchroniseren |synchroniseren |Internet Explorer |
| **Internet Explorer**: Lees lijst |synchroniseren |synchroniseren |Internet Explorer |
| **Internet Explorer**: getypte url's |synchroniseren |synchroniseren |Internet Explorer |
| **Internet Explorer**: browse geschiedenis |synchroniseren |synchroniseren |Internet Explorer |
| **Internet Explorer**: Favorieten |synchroniseren |synchroniseren |Internet Explorer |
| **Internet Explorer**: uitgesloten url's |synchroniseren |synchroniseren |Internet Explorer |
| **Internet Explorer**: start pagina's |synchroniseren |synchroniseren |Internet Explorer |
| **Internet Explorer**: domein suggesties |synchroniseren |synchroniseren |Internet Explorer |
| **Toetsen bord**: gebruikers kunnen het scherm toetsenbord in-of uitschakelen |synchroniseren |X |toegankelijkheid |
| **Toetsen bord**: Plak Ja inschakelen (standaard instelling) |synchroniseren |X |toegankelijkheid |
| **Toetsen bord**: filter toetsen inschakelen (standaard uitgeschakeld) |synchroniseren |X |toegankelijkheid |
| **Toetsen bord**: wissel knop toetsen inschakelen (standaard uitgeschakeld) |synchroniseren |X |toegankelijkheid |
| **Internet Explorer**: domein taal: Chinees (CHS) QWERTY-zelf studie inschakelen |synchroniseren |X |Taal |
| **Taal**: CHS QWERTY-classificatie van dynamische kandidaten inschakelen |synchroniseren |X |Taal |
| **Taal**: CHS QWERTY-char-set vereenvoudigd Chinees |synchroniseren |X |Taal |
| **Taal**: CHS QWERTY-char-set traditioneel Chinees |synchroniseren |X |Taal |
| **Taal**: CHS QWERTY-fuzzy pinyin |synchroniseren |Back-up |Taal |
| **Taal**: CHS-QWERTY-fuzzy-paren |synchroniseren |Back-up |Taal |
| **Taal**: CHS QWERTY-volledige pinyin |synchroniseren |X |Taal |
| **Taal**: CHS QWERTY-dubbele pinyin |synchroniseren |X |Taal |
| **Taal**: CHS QWERTY-Lees Automatische correctie |synchroniseren |X |Taal |
| **Taal**: CHS QWERTY-C/E switch Key, Shift |synchroniseren |X |Taal |
| **Taal**: CHS QWERTY-C/E-switch sleutel, CTRL |synchroniseren |X |Taal |
| **Taal**: CHS WUBI-invoer modus met één teken |synchroniseren |X |Taal |
| **Taal**: CHS WUBI: de resterende code ring van de kandidaat weer geven |synchroniseren |X |Taal |
| **Taal**: CHS WUBI-beep wanneer 4-code ring is ongeldig |synchroniseren |X |Taal |
| **Taal**: CHT Bopomofo-incl. cjk ext-A |synchroniseren |X |Taal |
| **Taal**: Japanse IME-voorspellende typen en aangepaste woorden |synchroniseren |synchroniseren |Taal |
| **Language**: KOREAANS (KOR) IME |X |X |Taal |
| **Taal**: Handschrift herkenning |X |X |Taal |
| **Taal**: taal profiel |synchroniseren |Back-up |Taal |
| **Taal**: spelling controle-auto correctie en spel fouten markeren |synchroniseren |Back-up |Taal |
| **Taal**: lijst met toetsen borden |synchroniseren |Back-up |Taal |
| **Vergrendelings scherm**: alle instellingen voor het vergrendelings scherm |X |X | |
| **Vergroot glas**: in-of uitschakelen (in-of uitgeschakeld) |X |X |Betere toegankelijkheid |
| **Vergroot glas**: inversie kleur in-of uitschakelen (standaard uitgeschakeld) |synchroniseren |X |Betere toegankelijkheid |
| **Vergroot glas**: bijhouden-de focus van het toetsen bord volgen |synchroniseren |X |Betere toegankelijkheid |
| **Vergroot glas**: bijhouden-de muis aanwijzer volgen |synchroniseren |X |Betere toegankelijkheid |
| **Vergroot glas**: starten wanneer gebruikers zich aanmelden (standaard uitgeschakeld) |synchroniseren |X |Betere toegankelijkheid |
| **Muis**: de grootte van de muis aanwijzer wijzigen |synchroniseren |X |other |
| **Muis**: de kleur van de muis aanwijzer wijzigen |synchroniseren |X |other |
| **Muis**: alle andere instellingen |X |X | |
| **Verteller**: snel starten |synchroniseren |X |Betere toegankelijkheid |
| **Verteller**: gebruikers kunnen de spreek hoogte van Verteller wijzigen |synchroniseren |X |Betere toegankelijkheid |
| **Verteller**: gebruikers kunnen Verteller-Lees hints voor algemene items in-of uitschakelen (standaard) |synchroniseren |X |Betere toegankelijkheid |
| **Verteller**: gebruikers kunnen in-of uitschakelen of ze getypte tekens kunnen horen (standaard ingeschakeld) |synchroniseren |X |Betere toegankelijkheid |
| **Verteller**: gebruikers kunnen in-of uitschakelen of ze getypte woorden (standaard) kunnen horen. |synchroniseren |X |Betere toegankelijkheid |
| **Verteller**: invoeg cursor invoegen na Verteller (standaard ingeschakeld) |synchroniseren |X |Betere toegankelijkheid |
| **Verteller**: visuele markering van Verteller-cursor inschakelen (standaard ingeschakeld) |synchroniseren |X |Betere toegankelijkheid |
| **Verteller**: Audio hints afspelen (standaard ingeschakeld) |synchroniseren |X |Betere toegankelijkheid |
| **Verteller**: Activeer toetsen op het scherm toetsenbord wanneer u uw vinger optilt (standaard uitgeschakeld) |synchroniseren |X |Betere toegankelijkheid |
| **Toegankelijkheid**: de dikte van de knipperende cursor instellen |synchroniseren |X |Betere toegankelijkheid |
| **Toegankelijkheid**: achtergrond afbeeldingen verwijderen (standaard uitgeschakeld) |synchroniseren |X |Betere toegankelijkheid |
| **Kracht en slaap stand**: alle instellingen |X |X | |
| **Persoonlijke instellingen van het Start scherm**: accent kleur (alleen telefoon) |X |synchroniseren |Thema |
| **Typen**: spelling woordenlijst |synchroniseren |Back-up |Taal |
| **Typen**: onjuist gespelde woorden in auto correctie |synchroniseren |Back-up |Taal |
| **Typen**: onjuist gespelde woorden markeren |synchroniseren |Back-up |Taal |
| **Typen**: tekst suggesties weer geven tijdens type |synchroniseren |Back-up |Taal |
| **Typen**: een spatie toevoegen nadat ik een tekst suggestie heb gekozen |synchroniseren |Back-up |Taal |
| **Typen**: een punt toevoegen nadat ik op de spatie balk dubbel Tik |synchroniseren |Back-up |Taal |
| **Typen**: de eerste letter van elke zin in hoofd letters zetten |synchroniseren |Back-up |Taal |
| **Typen**: alle hoofd letters gebruiken wanneer ik op SHIFT-toets Dubbeltik |synchroniseren |Back-up |Taal |
| **Typen**: geluiden afspelen tijdens het typen |synchroniseren |Back-up |Taal |
| **Typen**: personalisatie gegevens voor het scherm toetsenbord |synchroniseren |Back-up |Taal |
| **Wi-Fi**: Wi-Fi profielen (alleen WPA) |synchroniseren |synchroniseren |Wachtwoorden |

###### <a name="footnote-1"></a>Voetnoot 1

Mini maal ondersteunde OS-versie van de Windows Creators-update (build 15063). 

## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van ENTER prise State roaming](enterprise-state-roaming-overview.md)voor een overzicht.
