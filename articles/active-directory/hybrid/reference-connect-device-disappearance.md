---
title: Meer informatie over Azure AD Connect 1.4. xx. x en apparaat weer geven | Microsoft Docs
description: In dit document wordt een probleem beschreven dat zich voordoet bij versie 1.4. xx. x van Azure AD Connect
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: bc159452c81a673ca4a7ed46aa7eff19fd9209eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "73176024"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Meer informatie over Azure AD Connect 1.4. xx. x en het weer geven van apparaten
Met versie 1.4. xx. x van Azure AD Connect kunnen sommige klanten enkele of alle Windows-apparaten zien, verdwijnen van Azure AD. Dit is geen oorzaak van bezorgdheid, omdat deze apparaat-id's niet worden gebruikt door Azure AD tijdens de autorisatie van voorwaardelijke toegang. Met deze wijziging worden geen Windows-apparaten verwijderd die correct zijn geregistreerd bij Azure AD voor hybride Azure AD-deelname.

Als u ziet dat de verwijdering van object-objecten in azure AD de drempel waarde voor het verwijderen van het exporteren overschrijdt, wordt u geadviseerd dat de klant de verwijderingen kan door lopen. [Procedure: verwijderingen naar flow toestaan wanneer deze de drempel waarde voor verwijderen overschrijden](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>Achtergrond
Windows-apparaten die zijn geregistreerd als Hybrid Azure AD join, worden weer gegeven in azure AD als objecten voor apparaten. Deze apparaatobject kunnen worden gebruikt voor voorwaardelijke toegang. Windows 10-apparaten worden gesynchroniseerd met de Cloud via Azure AD Connect, Windows-apparaten met een lagere versie worden rechtstreeks geregistreerd met behulp van AD FS of naadloze eenmalige aanmelding.

## <a name="windows-10-devices"></a>Windows 10-apparaten
Alleen Windows 10-apparaten met een specifieke userCertificate-kenmerk waarde die is geconfigureerd met hybride Azure AD-deelname, moeten worden gesynchroniseerd met de Cloud door Azure AD Connect. In eerdere versies van Azure AD Connect was deze vereiste niet strikt afgedwongen, wat leidt tot overbodige apparaatfuncties in azure AD. Dergelijke apparaten in azure AD hebben altijd de status ' in behandeling ', omdat deze apparaten niet zijn bedoeld om te worden geregistreerd bij Azure AD. 

Met deze versie van Azure AD Connect worden alleen Windows 10-apparaten gesynchroniseerd die correct zijn geconfigureerd voor hybride Azure AD. Windows 10-apparaatgroepen zonder de specifieke userCertificate van Azure AD worden verwijderd uit Azure AD.

## <a name="down-level-windows-devices"></a>Windows-apparaten Down-Level
Azure AD Connect moet nooit [Windows-apparaten op lagere niveaus](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)synchroniseren. Alle apparaten in azure AD die voorheen onjuist zijn gesynchroniseerd, worden nu verwijderd uit Azure AD. Als Azure AD Connect probeert [Windows-apparaten op lagere niveaus](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)te verwijderen, is het apparaat niet de versie die is gemaakt door het [micro soft-Workplace join voor niet-Windows 10-computers MSI](https://www.microsoft.com/download/details.aspx?id=53554) en kan het niet worden gebruikt door een andere Azure AD-functie.

Sommige klanten moeten mogelijk opnieuw door gaan met het [plannen van uw hybrid Azure Active Directory-implementatie](../devices/hybrid-azuread-join-plan.md) om de Windows-apparaten op de juiste wijze te registreren en ervoor te zorgen dat dergelijke apparaten volledig kunnen deel nemen aan voorwaardelijke toegang op basis van een apparaat. 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>Hoe kan ik controleren welke apparaten worden verwijderd met deze update?

U kunt dit Power shell-script gebruiken om te controleren welke apparaten worden verwijderd: https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

Met dit script wordt een rapport gegenereerd over certificaten die zijn opgeslagen in Active Directory computer objecten, met name certificaten die zijn uitgegeven door de hybride Azure AD-functie voor deelname.
Hiermee wordt gecontroleerd of de certificaten aanwezig zijn in de eigenschap UserCertificate van een computer object in AD en, voor elk niet-verlopen certificaat, wordt gevalideerd of het certificaat is uitgegeven voor de hybride functie voor Azure AD-deelname (de onderwerpnaam komt overeen met CN = {ObjectGUID}).
Voordat Azure AD Connect synchroniseert met Azure AD op elke computer die ten minste één geldig certificaat bevat, maar vanaf Azure AD Connect versie 1,4, kan de synchronisatie-engine hybride Azure AD-deelname certificaten identificeren en ' cloudfilter ' het computer object van synchroniseren naar Azure AD tenzij er een geldig hybride Azure AD-deelname certificaat is.
Azure AD-apparaten die al zijn gesynchroniseerd met AD, maar geen geldig hybride Azure AD-deelname certificaat hebben, worden verwijderd (CloudFiltered = TRUE) door de synchronisatie-engine.

## <a name="next-steps"></a>Volgende stappen
- [Geschiedenis van Azure AD Connect-versie](reference-connect-version-history.md)
