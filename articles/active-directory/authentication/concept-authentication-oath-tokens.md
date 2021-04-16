---
title: Verificatiemethode voor OATH-tokens - Azure Active Directory
description: Meer informatie over het gebruik van OATH-tokens in Azure Active Directory om aanmeldingsgebeurtenissen te verbeteren en te beveiligen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99d0dd081e3e1a681ba55e3457b79a548d6b2bb7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530375"
---
# <a name="authentication-methods-in-azure-active-directory---oath-tokens"></a>Verificatiemethoden in Azure Active Directory - OATH-tokens 

OATH TOTP (tijdgebaseerd een keer wachtwoord) is een open standaard die aangeeft hoe een keer wachtwoord (OTP)-codes worden gegenereerd. OATH TOTP kan worden geïmplementeerd met behulp van software of hardware om de codes te genereren. Azure AD biedt geen ondersteuning voor OATH HOTP, een andere standaard voor het genereren van code.

## <a name="oath-software-tokens"></a>OATH-softwaretokens

Software OATH-tokens zijn doorgaans toepassingen zoals de Microsoft Authenticator app en andere authenticator-apps. Azure AD genereert de geheime sleutel, of seed, die wordt ingevoerd in de app en wordt gebruikt om elke OTP te genereren.

De Authenticator-app genereert automatisch codes wanneer deze zijn ingesteld om pushmeldingen uit te voeren, zodat een gebruiker een back-up heeft, zelfs als het apparaat geen verbinding heeft. Toepassingen van derden die OATH TOTP gebruiken om codes te genereren, kunnen ook worden gebruikt.

Sommige OATH TOTP-hardwaretokens zijn programmeerbaar, wat betekent dat ze niet worden voorzien van een geheime sleutel of vooraf geprogrammeerde seed. Deze programmeerbare hardwaretokens kunnen worden ingesteld met behulp van de geheime sleutel of seed die is verkregen via de installatiestroom van het softwaretoken. Klanten kunnen deze tokens aanschaffen bij de leverancier van hun keuze en de geheime sleutel of seed gebruiken in het installatieproces van hun leverancier.

## <a name="oath-hardware-tokens-preview"></a>OATH-hardwaretokens (preview)

Azure AD ondersteunt het gebruik van OATH-TOTP SHA-1-tokens die codes elke 30 of 60 seconden vernieuwen. Klanten kunnen deze tokens aanschaffen bij de leverancier van hun keuze.

OATH TOTP-hardwaretokens worden meestal voorzien van een geheime sleutel, of seed, die vooraf is geprogrammeerd in het token. Deze sleutels moeten worden ingevoerd in Azure AD, zoals beschreven in de volgende stappen. Geheime sleutels zijn beperkt tot 128 tekens, die mogelijk niet compatibel zijn met alle tokens. De geheime sleutel mag alleen de tekens *a-z* of *A-Z* en cijfers *2-7* bevatten en moet worden gecodeerd in *Base32.*

Programmeerbare OATH TOTP-hardwaretokens die opnieuw kunnen worden geseed, kunnen ook worden ingesteld met Azure AD in de installatiestroom van het softwaretoken.

OATH-hardwaretokens worden ondersteund als onderdeel van een openbare preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

![OATH-tokens uploaden naar de blade MFA OATH-tokens](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Zodra tokens zijn verkregen, moeten ze worden geüpload in een BESTANDsindeling met door komma's gescheiden waarden (CSV), waaronder de UPN, het serienummer, de geheime sleutel, het tijdsinterval, de fabrikant en het model, zoals wordt weergegeven in het volgende voorbeeld:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,2234567abcdef2234567abcdef,60,Contoso,HardwareKey
```  

> [!NOTE]
> Zorg ervoor dat u de koprij in uw CSV-bestand op neemt. Als een UPN één prijsopgave heeft, kunt u deze escapen met nog een enkele prijsopgave. Als de UPN bijvoorbeeld mijn user@domain.com is, wijzigt u deze in mijn' bij user@domain.com het uploaden van het bestand.

Zodra een globale beheerder correct is opgemaakt als een CSV-bestand, kan deze zich aanmelden bij de Azure Portal, navigeren naar **Azure Active Directory > Security > MFA > OATH-tokens** en het resulterende CSV-bestand uploaden.

Afhankelijk van de grootte van het CSV-bestand kan het enkele minuten duren om het te verwerken. Selecteer de **knop Vernieuwen** om de huidige status op te halen. Als er fouten in het bestand zijn, kunt u een CSV-bestand downloaden waarin eventuele fouten worden vermeld die u kunt oplossen. De veldnamen in het gedownloade CSV-bestand verschillen van de geüploade versie.  

Zodra eventuele fouten zijn verholpen, kan de beheerder  elke sleutel activeren door Activeren te selecteren voor het token en de OTP in te geven die op het token wordt weergegeven. U kunt elke vijf minuten maximaal 200 OATH-tokens activeren. 

Gebruikers kunnen een combinatie van maximaal vijf OATH-hardwaretokens of authenticatortoepassingen hebben, zoals de Microsoft Authenticator-app, die op elk moment is geconfigureerd voor gebruik. Hardware OATH-tokens kunnen niet worden toegewezen aan gastgebruikers in de resource-tenant.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het configureren van verificatiemethoden met behulp [van Microsoft Graph REST API](/graph/api/resources/authenticationmethods-overview).
