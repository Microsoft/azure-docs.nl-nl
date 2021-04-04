---
title: Onderbroken domeinen in Azure AD Domain Services | Microsoft Docs
description: Meer informatie over de verschillende statussen voor een beheerd domein van Azure AD DS en hoe u een opgeschort domein herstelt.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: b0b48e7ad494386052e6d94c32d7215b3f4f0202
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618821"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>Meer informatie over de statussen en het oplossen van onderbroken domeinen in Azure Active Directory Domain Services

Als Azure Active Directory Domain Services (Azure AD DS) een beheerd domein gedurende een lange periode niet kan onderhouden, wordt het beheerde domein in een onderbroken status geplaatst. Als een beheerd domein een onderbroken status behoudt, wordt het automatisch verwijderd. Om uw Azure AD DS beheerd domein in orde te laten en onderbrekingen te voor komen, moet u eventuele waarschuwingen zo snel mogelijk oplossen.

In dit artikel wordt uitgelegd waarom beheerde domeinen worden onderbroken en hoe u een opgeschort domein herstelt.

## <a name="overview-of-managed-domain-states"></a>Overzicht van beheerde domein statussen

Door de levens cyclus van een beheerd domein zijn er verschillende statussen die de status aangeven. Als het beheerde domein een probleem rapporteert, lost u de onderliggende oorzaak snel op om te voor komen dat de status wordt gedegradeerd.

![De voortgang van de status die een beheerd domein tot onderbreking neemt](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Een beheerd domein kan een van de volgende statussen hebben:

* [Wordt uitgevoerd](#running-state)
* [Aandacht vereist](#needs-attention-state)
* [Gebroken](#suspended-state)
* [Verwijderd](#deleted-state)

## <a name="running-state"></a>Uitvoerings status

Een beheerd domein dat juist is geconfigureerd en zonder problemen heeft de status *bezig met uitvoeren* . Dit is de gewenste status voor een beheerd domein.

### <a name="what-to-expect"></a>Wat u kunt verwachten

* Het Azure-platform kan regel matig de status van het beheerde domein bewaken.
* Domein controllers voor het beheerde domein worden regel matig patches en bijgewerkt.
* Wijzigingen van Azure Active Directory worden regel matig gesynchroniseerd met het beheerde domein.
* Reguliere back-ups worden gemaakt voor het beheerde domein.

## <a name="needs-attention-state"></a>Attentie status vereist

Een beheerd domein met een of meer problemen die moeten worden opgelost, is in de status *attentie vereist* . Op de status pagina voor het beheerde domein worden de waarschuwingen vermeld en wordt aangegeven waar zich een probleem voordoet.

Sommige waarschuwingen zijn tijdelijk en worden automatisch opgelost door het Azure-platform. Voor andere waarschuwingen kunt u het probleem oplossen door de meegeleverde stappen te volgen. Er is een kritieke waarschuwing, [Open een ondersteunings aanvraag voor Azure][azure-support] voor aanvullende hulp bij het oplossen van problemen.

Een voor beeld van een waarschuwing is wanneer er sprake is van een beperkende netwerk beveiligings groep. In deze configuratie is het mogelijk dat het Azure-platform het beheerde domein niet kan bijwerken en bewaken. Er wordt een waarschuwing gegenereerd en de status wordt gewijzigd in *vereist aandacht*.

Zie [problemen met waarschuwingen voor een beheerd domein oplossen][resolve-alerts]voor meer informatie.

### <a name="what-to-expect"></a>Wat u kunt verwachten

Wanneer een beheerd domein in de *attentie status vereist* is, is het mogelijk dat het Azure-platform niet regel matig gegevens kan bewaken, patcheren, bijwerken of er back-ups van maakt. In sommige gevallen, zoals een ongeldige netwerk configuratie, zijn de domein controllers voor het beheerde domein mogelijk onbereikbaar.

* Het beheerde domein bevindt zich in een slechte staat en de status controle wordt mogelijk gestopt totdat de waarschuwing is opgelost.
* Domein controllers voor het beheerde domein kunnen niet worden gerepareerd of bijgewerkt.
* Wijzigingen van Azure Active Directory kunnen mogelijk niet worden gesynchroniseerd met het beheerde domein.
* Er kunnen geen back-ups worden gemaakt voor het beheerde domein.
* Als u niet-kritieke waarschuwingen oplost die van invloed zijn op het beheerde domein, moet de status worden hersteld naar de status *actief* .
* Kritieke waarschuwingen worden geactiveerd voor configuratie problemen waarbij het Azure-platform de domein controllers niet kan bereiken. Als deze kritieke waarschuwingen niet binnen 15 dagen worden opgelost, wordt de *onderbroken* status in het beheerde domein ingevoerd.

## <a name="suspended-state"></a>Onderbroken status

Een beheerd domein voert de **onderbroken** status in om een van de volgende redenen:

* Een of meer kritieke waarschuwingen zijn in 15 dagen niet opgelost.
    * Kritieke waarschuwingen kunnen worden veroorzaakt door een onjuiste configuratie waarmee de toegang wordt geblokkeerd tot resources die nodig zijn voor Azure AD DS. De waarschuwing [AADDS104: de netwerk fout][alert-nsg] is langer dan 15 dagen in het beheerde domein.
* Er is een facturerings probleem met het Azure-abonnement of het Azure-abonnement is verlopen.

Beheerde domeinen worden onderbroken wanneer het Azure-platform niet in staat is om het domein te beheren, te controleren, te bewaken of te maken. Een beheerd domein blijft 15 dagen in een *onderbroken* status. Als u de toegang tot het beheerde domein wilt behouden, moet u de kritieke waarschuwingen direct oplossen.

### <a name="what-to-expect"></a>Wat u kunt verwachten

Het volgende gedrag treedt op wanneer een beheerd domein zich in de *onderbroken* status bevindt:

* Domein controllers voor het beheerde domein worden onbeschikbaar gemaakt en zijn niet bereikbaar binnen het virtuele netwerk.
* Secure LDAP toegang tot het beheerde domein via internet, als deze functie is ingeschakeld, werkt niet meer.
* Er zijn fouten opgetreden bij het verifiëren van het beheerde domein, aanmelden bij Vm's die lid zijn van een domein of verbinding maken via LDAP/LDAPS.
* Back-ups voor het beheerde domein worden niet langer ondernomen.
* Synchronisatie met Azure AD stopt.

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Hoe weet u of uw beheerde domein is onderbroken?

U ziet een [waarschuwing][resolve-alerts] op de pagina Azure AD DS Health in de Azure portal die het domein heeft gesuspendeerd. De status van het domein wordt ook weer gegeven als *onderbroken*.

### <a name="restore-a-suspended-domain"></a>Een opgeschort domein herstellen

Voer de volgende stappen uit om de status van een beheerd domein met de status *onderbroken* te herstellen:

1. Zoek en selecteer in het Azure Portal **Domain Services**.
1. Kies uw beheerde domein in de lijst, bijvoorbeeld *aaddscontoso.com*, en selecteer vervolgens **status**.
1. Selecteer de waarschuwing, zoals *AADDS503* of *AADDS504*, afhankelijk van de oorzaak van de onderbreking.
1. Kies de oplossings koppeling die in de waarschuwing wordt weer gegeven en volg de stappen om deze op te lossen.

Een beheerd domein kan alleen worden teruggezet naar de datum van de laatste back-up. De datum van de laatste back-up wordt weer gegeven op de pagina **status** van het beheerde domein. Wijzigingen die zijn opgetreden na de laatste back-up, worden niet hersteld. Back-ups voor een beheerd domein worden Maxi maal 30 dagen opgeslagen. Back-ups die ouder zijn dan 30 dagen, worden verwijderd.

Nadat u de waarschuwingen hebt opgelost wanneer het beheerde domein zich in de *onderbroken* status bevindt, [opent u een ondersteunings aanvraag voor Azure][azure-support] om terug te keren naar de status in orde. Als er een back-up van minder dan 30 dagen oud is, kan Azure-ondersteuning het beheerde domein herstellen.

## <a name="deleted-state"></a>Verwijderde status

Als een beheerd domein vijf tien dagen in de *onderbroken* status blijft, wordt het verwijderd. Dit proces kan niet worden hersteld.

### <a name="what-to-expect"></a>Wat u kunt verwachten

Wanneer een beheerd domein de status *verwijderd* krijgt, wordt het volgende gedrag weer gegeven:

* Alle bronnen en back-ups voor het beheerde domein worden verwijderd.
* U kunt het beheerde domein niet herstellen. U moet een vervangend beheerd domein maken om Azure AD DS opnieuw te gebruiken.
* Nadat de app is verwijderd, worden er geen kosten in rekening gebracht voor het beheerde domein.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [oplossen van waarschuwingen voor uw beheerde domein][resolve-alerts]om uw beheerde domein in orde te houden en het risico te beperken dat het wordt onderbroken.

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
