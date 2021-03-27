---
title: Verificatie methode van OATH-tokens-Azure Active Directory
description: Meer informatie over het gebruik van OATH-tokens in Azure Active Directory om aanmeldings gebeurtenissen te verbeteren en te beveiligen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/26/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45b3d5155befe005a6af68355426883b63a8c410
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105611783"
---
# <a name="authentication-methods-in-azure-active-directory---oath-tokens"></a>Verificatie methoden in Azure Active Directory OATH-tokens 

OATH mobiele TOTP (tijd op basis van een tijd wacht woord) is een open standaard die aangeeft hoe OTP-codes (one-time password) worden gegenereerd. OATH mobiele TOTP kan worden geïmplementeerd met behulp van software of hardware voor het genereren van de codes. Azure AD biedt geen ondersteuning voor OATH HOTP, een andere standaard code generatie.

## <a name="oath-software-tokens"></a>OATH-software tokens

Software OATH-tokens zijn doorgaans toepassingen zoals de Microsoft Authenticator-app en andere verificator-apps. Azure AD genereert de geheime sleutel, of Seed, die wordt ingevoerd in de app en die wordt gebruikt voor het genereren van elke OTP.

De verificator-app genereert automatisch codes bij het instellen van push meldingen, zodat een gebruiker een back-up heeft, zelfs als het apparaat geen verbinding heeft. Toepassingen van derden die gebruikmaken van OATH mobiele TOTP om codes te genereren, kunnen ook worden gebruikt.

Sommige software-tokens van OATH mobiele TOTP zijn programmeerbaar, wat betekent dat ze niet worden geleverd met een geheime sleutel of een vooraf geprogrammeerd Zaai zaad. Deze Programmeer bare hardware-tokens kunnen worden ingesteld met behulp van de geheime sleutel of Seed die is verkregen van de installatie stroom van het software token. Klanten kunnen deze tokens aanschaffen bij de leverancier van hun keuze en de geheime sleutel of het Seed gebruiken in het installatie proces van de leverancier.

## <a name="oath-hardware-tokens-preview"></a>OATH-hardware-tokens (preview-versie)

Azure AD ondersteunt het gebruik van OATH-mobiele TOTP SHA-1-tokens waarmee codes elke 30 of 60 seconden worden vernieuwd. Klanten kunnen deze tokens aanschaffen bij de leverancier van hun keuze.

OATH mobiele TOTP-hardware-tokens worden doorgaans geleverd met een geheime sleutel, of Seed, vooraf geprogrammeerd in het token. Deze sleutels moeten worden ingevoerd in azure AD, zoals beschreven in de volgende stappen. Geheime sleutels zijn beperkt tot 128 tekens, die mogelijk niet compatibel zijn met alle tokens. De geheime sleutel mag alleen de tekens *a-z* of *a-* z en cijfers *2-7* bevatten en moet worden gecodeerd in *Base32*.

Programmeer bare OATH-mobiele TOTP die kunnen worden geseedd, kunnen ook worden ingesteld met Azure AD in de instellings stroom van het software token.

OATH-hardware-tokens worden ondersteund als onderdeel van een open bare preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

![OATH-tokens uploaden naar de Blade MFA OATH-tokens](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Wanneer de tokens zijn verkregen, moeten ze worden geüpload in een CSV-bestand (Comma-Separated Values), met inbegrip van de UPN, het serie nummer, de geheime sleutel, het tijds interval, de fabrikant en het model, zoals wordt weer gegeven in het volgende voor beeld:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,2234567abcdef2234567abcdef,60,Contoso,HardwareKey
```  

> [!NOTE]
> Zorg ervoor dat u de rij met koppen in het CSV-bestand opneemt. Als een UPN een enkele aanhalings tekens heeft, kunt u deze met een andere aanhalings teken met één citaat escapen. Als de UPN bijvoorbeeld mijn is user@domain.com , wijzigt u deze in mijn ' ' user@domain.com bij het uploaden van het bestand.

Als een globale beheerder zich heeft geformatteerd als een CSV-bestand, kan deze zich vervolgens aanmelden bij de Azure Portal, naar **Azure Active Directory > Security > MFA > OATH-tokens** gaan en het resulterende CSV-bestand uploaden.

Afhankelijk van de grootte van het CSV-bestand kan het enkele minuten duren voordat het proces is uitgevoerd. Selecteer de knop **vernieuwen** om de huidige status op te halen. Als het bestand fouten bevat, kunt u een CSV-bestand downloaden met een lijst met fouten die u moet oplossen. De veld namen in het gedownloade CSV-bestand wijken af van de geüploade versie.  

Zodra er fouten zijn opgelost, kan de beheerder elke sleutel activeren door **Activate** voor het token te selecteren en de otp op te geven die op het token wordt weer gegeven. U kunt Maxi maal 200 OATH-tokens activeren om de vijf minuten. 

Gebruikers kunnen een combi natie hebben van Maxi maal vijf OATH-hardware-tokens of verificator-toepassingen, zoals de app Microsoft Authenticator, die op elk gewenst moment worden geconfigureerd voor gebruik.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het configureren van verificatie methoden met behulp van de [Microsoft Graph rest API bèta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).
