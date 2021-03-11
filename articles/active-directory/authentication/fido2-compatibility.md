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
ms.openlocfilehash: 3f90edd5729ff5229be09bc3798082c33bdeead2
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632098"
---
# <a name="browser-support-of-fido2-passwordless-authentication"></a>Browser ondersteuning voor FIDO2-verificatie met wacht woord

Azure Active Directory kunnen [FIDO2-beveiligings sleutels](./concept-authentication-passwordless.md#fido2-security-keys) worden gebruikt als een apparaat met een wacht woord. De beschik baarheid van FIDO2-verificatie voor micro soft-accounts werd [aangekondigd in 2018](https://techcommunity.microsoft.com/t5/identity-standards-blog/all-about-fido2-ctap2-and-webauthn/ba-p/288910). Zoals besproken in de aankondiging, moeten bepaalde optionele functies en uitbrei dingen voor de FIDO2 CTAP-specificatie worden geïmplementeerd ter ondersteuning van beveiligde verificatie met micro soft-en Azure Active Directory-accounts. Het volgende diagram laat zien welke browsers en combi Naties van besturings systemen verificatie zonder wacht woord ondersteunen met behulp van FIDO2-verificatie sleutels met Azure Active Directory.

## <a name="supported-browsers"></a>Ondersteunde browsers

In deze tabel wordt de ondersteuning voor het verifiëren van Azure Active Directory (Azure AD) en micro soft-accounts (MSA) weer gegeven. Micro soft-accounts worden gemaakt door gebruikers voor services zoals Xbox, Skype of Outlook.com. Ondersteunde apparaattypen zijn onder andere **USB**, Near-Field Communication (**NFC**) en Bluetooth Low Energy (**Bel**).

| Besturingssysteem | Chrome | Chrome  | Chrome | Edge | Edge | Edge | Firefox | Firefox | Firefox |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| | USB | NFC | Bel | USB | NFC | Bel | USB | NFC | Bel |
| **Windows**  | ![Chrome ondersteunt USB in Windows voor AAD-accounts.][y] | ![Chrome ondersteunt NFC in Windows voor AAD-accounts.][y] | ![Chrome ondersteunt de ondersteuning van een door Windows voor AAD-accounts.][y] | ![Edge ondersteunt USB in Windows voor AAD-accounts.][y] | ![Edge ondersteunt NFC in Windows voor AAD-accounts.][y] | ![Edge ondersteunt de ondersteuning voor een micro soft-account voor AAD-accounts.][y] | ![Firefox ondersteunt USB in Windows voor AAD-accounts.][y] | ![Firefox ondersteunt NFC in Windows voor AAD-accounts.][y] | ![Firefox ondersteunt de ondersteuning van een bel op Windows voor AAD-accounts.][y] |
| **MacOS**  | ![Chrome ondersteunt USB op macOS voor AAD-accounts.][y] | ![Chrome biedt geen ondersteuning voor NFC in macOS voor AAD-accounts.][n] | ![Chrome biedt geen ondersteuning voor het macOS voor AAD-accounts.][n] | ![Edge ondersteunt USB op macOS voor AAD-accounts.][y] | ![Edge biedt geen ondersteuning voor NFC in macOS voor AAD-accounts.][n] | ![Edge biedt geen ondersteuning voor een service voor AAD-accounts.][n] | ![Firefox biedt geen ondersteuning voor USB op macOS voor AAD-accounts.][n] | ![Firefox biedt geen ondersteuning voor NFC in macOS voor AAD-accounts.][n] | ![Firefox ondersteunt geen ondersteuning voor de AAD-accounts van een macOS.][n] |
| **Linux**  | ![Chrome ondersteunt USB op Linux voor AAD-accounts.][y] | ![Chrome biedt geen ondersteuning voor NFC op Linux voor AAD-accounts.][n] | ![Chrome biedt geen ondersteuning voor het bieden van een service op Linux voor AAD-accounts.][n] | ![Edge biedt geen ondersteuning voor USB op Linux voor AAD-accounts.][n] | ![Edge biedt geen ondersteuning voor NFC op Linux voor AAD-accounts.][n] | ![Edge biedt geen ondersteuning voor een bel op Linux voor AAD-accounts.][n] | ![Firefox biedt geen ondersteuning voor USB op Linux voor AAD-accounts.][n] | ![Firefox biedt geen ondersteuning voor NFC op Linux voor AAD-accounts.][n] | ![Firefox biedt geen ondersteuning voor het bieden van een service op Linux voor AAD-accounts.][n] |



## <a name="unsupported-browsers"></a>Niet-ondersteunde browsers

De volgende combi Naties van besturings systemen en browsers worden niet ondersteund, maar toekomstige ondersteuning en tests worden onderzocht. Als u andere besturings systemen en browser ondersteuning wilt zien, kunt u feedback geven via het hulp programma voor product feedback onder aan de pagina.

| Besturingssysteem | Browser |
| ---- | ---- |
| iOS | Safari, Brave |
| macOS | Safari |
| Android | Chrome |
| ChromeOS | Chrome |

## <a name="minimum-browser-version"></a>Minimale browserversie

Hier volgen de minimale vereisten voor de browser versie. 

| Browser | Minimale versie |
| ---- | ---- |
| Chrome | 76 |
| Edge | Windows 10 versie 1903<sup>1</sup> |
| Firefox | 66 |

<sup>1</sup> Alle versies van de nieuwe op chroom gebaseerde micro soft Edge ondersteunen Fido2. Ondersteuning voor micro soft Edge verouderd is toegevoegd in 1903.

## <a name="next-steps"></a>Volgende stappen
[Aanmelden zonder wacht woord voor beveiligings sleutel inschakelen (preview)](./howto-authentication-passwordless-security-key.md)

<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png
