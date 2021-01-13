---
title: Upgrade van DirSync en Azure AD Sync | Microsoft Docs
description: Hierin wordt beschreven hoe u een upgrade uitvoert van DirSync en Azure AD Sync naar Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: bd68fb88-110b-4d76-978a-233e15590803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 713ec3a4020434fa73aad2e04676129cf43853be
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165838"
---
# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>Windows Azure Active Directory-synchronisatie en Azure Active Directory-synchronisatie upgraden
Azure AD Connect is de beste manier om verbinding te maken met uw on-premises Directory met Azure AD en Microsoft 365. Dit is een goede tijd om bij te werken naar Azure AD Connect van Windows Azure Active Directory Sync (DirSync) of Azure AD Sync (AADSync), omdat deze hulpprogram ma's nu zijn afgeschaft en niet langer worden ondersteund vanaf 13 april 2017.

De twee hulpprogram ma's voor het synchroniseren van identiteiten die zijn afgeschaft, worden aangeboden voor klanten met één forest (DirSync) en voor meerdere forests en andere geavanceerde klanten (Azure AD Sync). Deze oudere hulpprogram ma's zijn vervangen door één oplossing die beschikbaar is voor alle scenario's: Azure AD Connect. Het biedt nieuwe functionaliteit, verbeteringen van functies en ondersteuning voor nieuwe scenario's. Om uw on-premises identiteits gegevens te kunnen blijven synchroniseren met Azure AD en Microsoft 365, raden wij u ten zeerste aan om een upgrade uit te voeren naar Azure AD Connect. Micro soft garandeert niet dat deze oudere versies na 31 december 2017 kunnen worden uitgevoerd.

De laatste versie van DirSync is uitgebracht in juli 2014 en de laatste release van Azure AD Sync is uitgebracht in mei 2015.

## <a name="what-is-azure-ad-connect"></a>Wat is Azure AD Connect?
Azure AD Connect is de opvolgende taak van DirSync en Azure AD Sync. Alle scenario's die deze twee ondersteunen worden gecombineerd. Meer informatie hierover vindt u in [uw on-premises identiteiten integreren met Azure Active Directory](whatis-hybrid-identity.md).

## <a name="deprecation-schedule"></a>Schema voor afschaffing
| Datum | Opmerking |
| --- | --- |
| 13 april 2016 |Windows Azure Active Directory Sync (' DirSync ') en Microsoft Azure Active Directory Sync (' Azure AD Sync ') worden aangekondigd als afgeschaft. |
| 13 april 2017 |Ondersteuning wordt beëindigd. Klanten kunnen niet langer een ondersteunings aanvraag openen zonder eerst een upgrade naar Azure AD Connect te hoeven uitvoeren. |
|31 december 2017|Azure AD accepteert mogelijk geen communicaties meer van Windows Azure Active Directory Sync (' DirSync ') en Microsoft Azure Active Directory Sync (' Azure AD Sync ').

## <a name="how-to-transition-to-azure-ad-connect"></a>Overstappen naar Azure AD Connect
Als u DirSync uitvoert, kunt u op twee manieren een upgrade uitvoeren: in-place upgrade en parallelle implementatie. Een in-place upgrade wordt aanbevolen voor de meeste klanten en als u een recent besturings systeem en minder dan 50.000 objecten hebt. In andere gevallen wordt u aangeraden een parallelle implementatie uit te voeren, waarbij uw DirSync-configuratie wordt verplaatst naar een nieuwe server met Azure AD Connect.

| Oplossing | Scenario |
| --- | --- |
| [Upgraden van DirSync](how-to-dirsync-upgrade-get-started.md) |<li>Als er al een DirSync-server wordt uitgevoerd.</li> |
| [Upgrade van Azure AD Sync](how-to-upgrade-previous-version.md) |<li>Als u overstapt van Azure AD Sync.</li> |

Als u wilt zien hoe u een in-place upgrade van DirSync naar Azure AD Connect kunt doen, raadpleegt u deze kanaal 9-video:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>Veelgestelde vragen
**V: Ik heb een e-mail melding ontvangen van het Azure-team en/of een bericht uit het Microsoft 365 berichten centrum, maar ik gebruik Connect.**  
De melding werd ook naar klanten verzonden met behulp van Azure AD Connect met een buildnummer 1,0. \* . 0 (met een bèta versie van 1,1). Micro soft raadt klanten aan om actueel te blijven met Azure AD Connect releases. Met de functie voor [automatische upgrades](how-to-connect-install-automatic-upgrade.md) die in 1,1 is geïntroduceerd, kunt u op eenvoudige wijze altijd een recente versie van Azure AD Connect installeren.

**V: zal DirSync/Azure AD Sync niet meer werken op 13 april 2017?**  
DirSync/Azure AD Sync blijft werken op 13 april 2017.  Azure AD aanvaardt echter mogelijk niet langer communicatie van DirSync/Azure AD Sync na 31 december 2017.

**V: voor welke DirSync-versies kan ik een upgrade uitvoeren?**  
Het wordt ondersteund voor een upgrade vanaf elke versie van DirSync die momenteel wordt gebruikt. 

**V: wat gebeurt er met de Azure AD-connector voor FIM/MIM?**  
De Azure AD-connector voor FIM/MIM is **niet** aangekondigd als afgeschaft. Het is bij **functie bevriezen**; Er wordt geen nieuwe functionaliteit toegevoegd en er worden geen oplossingen voor oplossingen ontvangen. Micro soft raadt klanten aan om te plannen om van IT naar Azure AD Connect te gaan. Het wordt ten zeerste aangeraden geen nieuwe implementaties te starten met het. Deze connector wordt in de toekomst afgeschaft.

## <a name="additional-resources"></a>Aanvullende resources
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
