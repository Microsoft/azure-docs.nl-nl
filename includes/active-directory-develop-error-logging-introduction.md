---
author: mmacy
ms.author: marsma
ms.date: 12/16/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 53198c663d318a2eb47bcb3207939bbcb1fdd59c
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763395"
---
De micro soft Authentication Library (MSAL)-apps genereren logboek berichten die u kunnen helpen bij het vaststellen van problemen. Een app kan logboek registratie met een paar regels code configureren en aangepaste controle hebben over het detail niveau en bepalen of persoonlijke en organisatie gegevens worden vastgelegd. We raden u aan om een MSAL-call back te maken en een manier te bieden waarop gebruikers logboeken kunnen indienen wanneer ze verificatie problemen hebben.

## <a name="logging-levels"></a>Registratie niveaus

MSAL biedt verschillende detail niveaus voor logboek registratie:

- Fout: geeft aan dat er iets is overgegaan en dat er een fout is gegenereerd. Wordt gebruikt voor het opsporen van fouten en het identificeren van problemen.
- Waarschuwing: er is niet noodzakelijkerwijs een fout of een fout opgetreden, maar is bedoeld voor diagnostische gegevens en het lokaliseren van problemen.
- Info: MSAL registreert gebeurtenissen die bedoeld zijn voor informatie die niet nood zakelijk is bedoeld voor fout opsporing.
- Uitgebreid: standaard. MSAL registreert de volledige details van het gedrag van de tape wisselaar.

## <a name="personal-and-organizational-data"></a>Persoonlijke en organisatie gegevens

Standaard worden in de MSAL-logger geen zeer gevoelige persoonlijke of bedrijfs gegevens vastgelegd. De bibliotheek biedt de mogelijkheid om logboek registratie van persoonlijke en organisatie gegevens in te scha kelen als u dit besluit.

In de volgende secties vindt u meer informatie over de MSAL-fout registratie voor uw toepassing.