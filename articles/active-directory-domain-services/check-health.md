---
title: De status van de Azure Active Directory Domain Services controleren | Microsoft Docs
description: Meer informatie over het controleren van de status van een beheerd domein van Azure Active Directory Domain Services (Azure AD DS) en het begrijpen van status berichten met behulp van de Azure Portal.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 92cc6157c590d3e05b4002e0f071c08e4ec23a16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620321"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>De status controleren van een door Azure Active Directory Domain Services beheerd domein

Azure Active Directory Domain Services (Azure AD DS) voert enkele achtergrond taken uit om het beheerde domein in orde en up-to-date te houden. Deze taken omvatten het maken van back-ups, het Toep assen van beveiligings updates en het synchroniseren van gegevens vanuit Azure AD. Als er problemen zijn met het door Azure AD DS beheerde domein, worden deze taken mogelijk niet voltooid. Als u problemen wilt controleren en oplossen, kunt u de status van een beheerd domein controleren met behulp van de Azure Portal.

In dit artikel leest u hoe u de status van Azure AD DS kunt bekijken en hoe u de informatie of waarschuwingen kunt zien.

## <a name="view-the-health-status"></a>De status weer geven

De integriteits status voor een beheerd domein wordt weer gegeven met behulp van de Azure Portal. Informatie over de tijd van de laatste back-up en synchronisatie met Azure AD kan worden weer gegeven, samen met eventuele waarschuwingen die duiden op een probleem met de status van het beheerde domein. Voer de volgende stappen uit om de status van een beheerd domein weer te geven:

1. Zoek en selecteer **Azure AD Domain Services** in de Azure-portal.
1. Selecteer uw beheerde domein, zoals *aaddscontoso.com*.
1. Selecteer aan de linkerkant van het venster Azure AD DS resource de optie **status**. In de volgende voorbeeld scherm afbeelding ziet u een goed beheerd domein en de status van de laatste back-up en Azure AD-synchronisatie:

    ![Overzicht van status pagina's in de Azure Portal de Azure Active Directory Domain Services status weer geven](./media/check-health/health-page.png)

De *laatste geëvalueerde* tijds tempel van de status pagina wordt weer gegeven wanneer het beheerde domein voor het laatst is gecontroleerd. De status van een beheerd domein wordt elk uur geëvalueerd. Als u wijzigingen aanbrengt in een beheerd domein, wacht u tot de volgende evaluatie cyclus om de bijgewerkte status weer te geven.

De status in de rechter bovenhoek geeft de algemene status van het beheerde domein aan. Met de status worden alle bestaande waarschuwingen in uw domein gemeten. De volgende tabel bevat een overzicht van de beschik bare status indicatoren:

| Status | Pictogram | Uitleg |
| --- | :----: | --- |
| Wordt uitgevoerd | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Het beheerde domein wordt correct uitgevoerd en heeft geen kritieke of waarschuwings meldingen. Het domein heeft mogelijk informatieve waarschuwingen. |
| Aandacht vereist (waarschuwing) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Er zijn geen kritieke waarschuwingen op het beheerde domein, maar er zijn een of meer waarschuwingen die moeten worden opgelost. |
| Aandacht vereist (kritiek) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Er zijn een of meer kritieke waarschuwingen op het beheerde domein die moeten worden geadresseerd. Mogelijk hebt u ook waarschuwings-en/of informatieve waarschuwingen. |
| Installeert | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | Het beheerde domein wordt geïmplementeerd. |

## <a name="understand-monitors-and-alerts"></a>Monitors en waarschuwingen begrijpen

De integriteits status voor een beheerd domein toont twee typen informatie: *monitors* en *waarschuwingen*. Monitors tonen het tijdstip waarop de basis achtergrond taken zijn voltooid. Waarschuwingen bieden informatie of suggesties voor het verbeteren van de stabiliteit van het beheerde domein.

### <a name="monitors"></a>Monitors

Monitors zijn gebieden van een beheerd domein die regel matig worden gecontroleerd. Als er actieve waarschuwingen voor het beheerde domein zijn, kan dit ertoe leiden dat een van de monitors een probleem meldt. Azure AD DS heeft momenteel monitors voor de volgende gebieden:

* Backup
* Synchronisatie met Azure AD

#### <a name="backup-monitor"></a>Back-upmonitor

De back-upmonitor controleert of automatische reguliere back-ups van het beheerde domein zijn uitgevoerd. De volgende tabel bevat een overzicht van de beschik bare status van back-upmonitor:

| Detail waarde | Uitleg |
| --- | --- |
| Geen back-up maken | Deze status is normaal voor nieuwe beheerde domeinen. De eerste back-up moet 24 uur na het implementeren van het beheerde domein worden gemaakt. Als deze status zich blijft voordoen, [opent u een ondersteunings aanvraag voor Azure][azure-support]. |
| De laatste back-up is 1 tot 14 dagen geleden genomen | Dit tijds bereik is de verwachte status voor de back-upmonitor. Automatische reguliere back-ups moeten in deze periode optreden. |
| De laatste back-up is meer dan 14 dagen geleden gemaakt. | Een time span die langer is dan twee weken geeft aan dat er een probleem is met de automatische reguliere back-ups. Actieve kritieke waarschuwingen kunnen verhinderen dat een back-up van het beheerde domein wordt gemaakt. Alle actieve waarschuwingen voor het beheerde domein oplossen. Als de back-upcontrole de status niet bijwerkt om een recente back-up te melden, [opent u een ondersteunings aanvraag voor Azure][azure-support]. |

#### <a name="synchronization-with-azure-ad-monitor"></a>Synchronisatie met Azure AD monitor

Een beheerd domein wordt regel matig gesynchroniseerd met Azure Active Directory. Het aantal gebruikers en groeps objecten, en het aantal wijzigingen dat sinds de laatste synchronisatie in de Azure AD-adres lijst is aangebracht, is van invloed op hoe lang het duurt om te synchroniseren. Als het beheerde domein voor het laatst drie dagen geleden is gesynchroniseerd, kunt u actieve waarschuwingen controleren en oplossen. Als de synchronisatie monitor de status niet bijwerkt om een recente synchronisatie weer te geven nadat u actieve waarschuwingen hebt geadresseerd, [opent u een ondersteunings aanvraag voor Azure][azure-support].

### <a name="alerts"></a>Waarschuwingen

Er worden waarschuwingen gegenereerd voor problemen in een beheerd domein die moeten worden geadresseerd om de service goed uit te voeren. Elke waarschuwing legt het probleem uit en geeft een URL die specifieke stappen bevat om het probleem op te lossen. Zie [probleemoplossings waarschuwingen](troubleshoot-alerts.md)voor meer informatie over de mogelijke waarschuwingen en hun oplossingen.

Waarschuwingen voor de status worden gecategoriseerd in de volgende Ernst niveaus:

 * **Kritieke waarschuwingen** zijn problemen die het beheerde domein aanzienlijk beïnvloeden. Deze waarschuwingen moeten onmiddellijk worden opgelost. Het Azure-platform kan het beheerde domein pas controleren, beheren, bijwerken en synchroniseren als de problemen zijn opgelost.
 * **Waarschuwingen** worden weer gegeven over problemen die van invloed kunnen zijn op de bewerkingen van het beheerde domein als het probleem zich blijft voordoen. Deze waarschuwingen bieden ook aanbevelingen voor het beveiligen van het beheerde domein.
 * **Informatieve waarschuwingen** zijn meldingen die geen negatieve invloed hebben op het beheerde domein. Informatieve waarschuwingen bieden inzicht in wat er gebeurt in het beheerde domein.

## <a name="next-steps"></a>Volgende stappen

Zie [waarschuwingen op uw beheerde domein oplossen][troubleshoot-alerts] voor meer informatie over waarschuwingen die worden weer gegeven op de pagina status status.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
