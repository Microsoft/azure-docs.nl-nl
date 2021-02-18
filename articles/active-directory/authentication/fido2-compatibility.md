---
title: Browser ondersteuning voor FIDO2-verificatie met een wacht woord | Azure Active Directory
description: Browsers en combi Naties van besturings systemen ondersteunen FIDO2-verificatie met een wacht woord voor apps met Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: nichola
author: knicholasa
manager: martinco
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e324ae0fc80bb5990f9cf15901080684086a549
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652238"
---
# <a name="browser-support-of-fido2-passwordless-authentication"></a>Browser ondersteuning voor FIDO2-verificatie met wacht woord

Azure Active Directory kunnen [FIDO2-beveiligings sleutels](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#fido2-security-keys) worden gebruikt als een apparaat met een wacht woord. De beschik baarheid van FIDO2-verificatie voor micro soft-accounts werd [aangekondigd in 2018](https://techcommunity.microsoft.com/t5/identity-standards-blog/all-about-fido2-ctap2-and-webauthn/ba-p/288910). Zoals besproken in de aankondiging, moeten bepaalde optionele functies en uitbrei dingen voor de FIDO2 CTAP-specificatie worden geïmplementeerd ter ondersteuning van beveiligde verificatie met micro soft-en Azure Active Directory-accounts. Het volgende diagram laat zien welke browsers en combi Naties van besturings systemen verificatie zonder wacht woord ondersteunen met behulp van FIDO2-verificatie sleutels met Azure Active Directory.

## <a name="supported-browsers"></a>Ondersteunde browsers

In deze tabel wordt de ondersteuning voor het verifiëren van Azure Active Directory (Azure AD) en micro soft-accounts (MSA) weer gegeven. Micro soft-accounts worden gemaakt door gebruikers voor services zoals Xbox, Skype of Outlook.com. Ondersteunde apparaattypen zijn onder andere **USB**, Near-Field Communication (**NFC**) en Bluetooth Low Energy (**Bel**).

|  | Chrome |  |  | Edge |  |  | Firefox |  |  |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| | USB | NFC | Bel | USB | NFC | Bel | USB | NFC | Bel |
| **Windows**  | ![Chrome ondersteunt USB in Windows voor AAD-accounts.][y] | ![Chrome ondersteunt NFC in Windows voor AAD-accounts.][y] | ![Chrome ondersteunt de ondersteuning van een door Windows voor AAD-accounts.][y] | ![Edge ondersteunt USB in Windows voor AAD-accounts.][y] | ![Edge ondersteunt NFC in Windows voor AAD-accounts.][y] | ![Edge ondersteunt de ondersteuning voor een micro soft-account voor AAD-accounts.][y] | ![Firefox ondersteunt USB in Windows voor AAD-accounts.][y] | ![Firefox ondersteunt NFC in Windows voor AAD-accounts.][y] | ![Firefox ondersteunt de ondersteuning van een bel op Windows voor AAD-accounts.][y] |
| **MacOS**  | ![Chrome ondersteunt USB op macOS voor AAD-accounts.][y] | ![Chrome biedt geen ondersteuning voor NFC in macOS voor AAD-accounts.][n] | ![Chrome biedt geen ondersteuning voor het macOS voor AAD-accounts.][n] | ![Edge ondersteunt USB op macOS voor AAD-accounts.][y] | ![Edge biedt geen ondersteuning voor NFC in macOS voor AAD-accounts.][n] | ![Edge biedt geen ondersteuning voor een service voor AAD-accounts.][n] | ![Firefox biedt geen ondersteuning voor USB op macOS voor AAD-accounts.][n] | ![Firefox biedt geen ondersteuning voor NFC in macOS voor AAD-accounts.][n] | ![Firefox ondersteunt geen ondersteuning voor de AAD-accounts van een macOS.][n] |
| **Linux**  | ![Chrome ondersteunt USB op Linux voor AAD-accounts.][y] | ![Chrome biedt geen ondersteuning voor NFC op Linux voor AAD-accounts.][n] | ![Chrome biedt geen ondersteuning voor het bieden van een service op Linux voor AAD-accounts.][n] | ![Edge biedt geen ondersteuning voor USB op Linux voor AAD-accounts.][n] | ![Edge biedt geen ondersteuning voor NFC op Linux voor AAD-accounts.][n] | ![Edge biedt geen ondersteuning voor een bel op Linux voor AAD-accounts.][n] | ![Firefox biedt geen ondersteuning voor USB op Linux voor AAD-accounts.][n] | ![Firefox biedt geen ondersteuning voor NFC op Linux voor AAD-accounts.][n] | ![Firefox biedt geen ondersteuning voor het bieden van een service op Linux voor AAD-accounts.][n] |

## <a name="operating-system-versions-tested"></a>Geteste besturings systeem versies

De informatie in de bovenstaande tabel is getest op de volgende versies van het besturings systeem.

| Besturingssysteem | Nieuwste test versie |
| --- | --- |
| Windows | Windows 10-20H2 |
| macOS | OS X 11 Big sur |
| Linux | Fedora 32-werk station |

## <a name="next-steps"></a>Volgende stappen
[Aanmelden zonder wacht woord voor beveiligings sleutel inschakelen (preview)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key)

<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png
