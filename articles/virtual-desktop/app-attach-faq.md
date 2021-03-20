---
title: Veelgestelde vragen over de MSIX-app van Windows virtueel bureau blad-Azure
description: Veelgestelde vragen over MSIX app attach voor Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 395c274630131c2ae5f451443913e1e69c7c422a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738698"
---
# <a name="msix-app-attach-faq"></a>Veelgestelde vragen over het koppelen van MSIX-apps

In dit artikel vindt u antwoorden op veelgestelde vragen over MSIX app attach voor Windows virtueel bureau blad.

## <a name="whats-the-difference-between-msix-and-msix-app-attach"></a>Wat is het verschil tussen de MSIX-en MSIX-app-koppeling?

MSIX is een verpakkings indeling voor apps, terwijl MSIX app attach de functie is die MSIX-pakketten levert aan uw implementatie.

## <a name="does-msix-app-attach-use-fslogix"></a>Maakt MSIX-app gebruik van FSLogix?

MSIX app attach maakt geen gebruik van FSLogix. MSIX app attach en FSLogix zijn echter ontworpen om samen te werken om een naadloze gebruikers ervaring te bieden.

## <a name="can-i-use-the-msix-app-attach-outside-of-windows-virtual-desktop"></a>Kan ik de MSIX-app toevoegen buiten het virtuele bureau blad van Windows?

De Api's die aan Power MSIX app attach zijn gekoppeld, zijn beschikbaar voor Windows 10 Enter prise. Deze Api's kunnen worden gebruikt buiten het virtuele bureau blad van Windows. Er is echter geen beheer vlak voor de MSIX-app die zich buiten het virtuele bureau blad van Windows bevindt.

## <a name="how-do-i-get-an-msix-package"></a>Hoe kan ik een MSIX-pakket ophalen?

Uw software leverancier geeft u een MSIX-pakket. U kunt ook niet-MSIX-pakketten converteren naar MSIX. Meer informatie over het [verplaatsen van uw bestaande installatie Programma's naar MSIX](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix).

## <a name="which-operating-systems-support-msix-app-attach"></a>Welke besturings systemen ondersteunen MSIX-app koppelen?

Windows 10 Enter prise en Windows 10 Enter prise multi-session, versie 2004 of hoger.

## <a name="is-msix-app-attach-currently-generally-available"></a>Is MSIX app attach momenteel algemeen beschikbaar?

MSIX app attach maakt deel uit van Windows 10 Enter prise en Windows 10 Enter prise multi-session, versie 2004 of hoger. Beide besturings systemen zijn momenteel algemeen beschikbaar. 

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>Kan ik MSIX app-koppeling buiten het virtuele bureau blad van Windows gebruiken?

MSIX en MSIX app attach-Api's maken deel uit van Windows 10 Enter prise en Windows 10 Enter prise multi-session, versie 2004 en hoger. We bieden momenteel geen beheer software voor een MSIX-app die zich buiten het virtuele bureau blad van Windows bevindt.

## <a name="can-i-run-two-versions-of-the-same-application-at-the-same-time"></a>Kan ik op hetzelfde moment twee versies van dezelfde toepassing uitvoeren?

Voor twee versies van dezelfde MSIX-toepassingen die gelijktijdig moeten worden uitgevoerd, moet de MSIX-pakket familie die in het appxmanifest.xml bestand is gedefinieerd, voor elke app verschillend zijn.

## <a name="should-i-disable-auto-update-when-using-msix-app-attach"></a>Moet ik automatisch bijwerken uitschakelen wanneer ik een MSIX-app toevoeg?

Ja. MSIX app attach biedt geen ondersteuning voor automatische updates voor MSIX-toepassingen.

## <a name="how-do-permissions-work-with-msix-app-attach"></a>Hoe werken machtigingen met MSIX-app koppelen?

Alle virtuele machines (Vm's) in een hostgroep die gebruikmaakt van een MSIX-app-koppeling, moeten lees machtigingen hebben op de bestands share waar de MSIX-installatie kopieën worden opgeslagen. Als ook Azure Files wordt gebruikt, moeten ze zowel op rollen gebaseerd toegangs beheer (RBAC) als nieuwe technologieën voor bestands systeem (NTFS) worden verleend.

## <a name="can-i-use-msix-app-attach-for-http-or-https"></a>Kan ik de MSIX-app attach voor HTTP of HTTPs gebruiken?

Het gebruik van de MSIX-app attach via HTTP of HTTPs wordt momenteel niet ondersteund.

## <a name="can-i-restage-the-same-msix-application"></a>Kan ik dezelfde MSIX-toepassing opnieuw instellen?

Ja. U kunt toepassingen die u al hebt gestage, opnieuw bezetten en dit zou geen fouten moeten veroorzaken.

## <a name="does-msix-app-attach-support-self-signed-certificates"></a>Ondersteunt MSIX-app zelfondertekende certificaten?

Ja. U moet het zelfondertekende certificaat installeren op alle host-Vm's van de sessiehost waarbij MSIX app attach wordt gebruikt om de zelfondertekende toepassing te hosten.


## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over het toevoegen van MSIX-apps, raadpleegt u het [overzicht](what-is-app-attach.md) en de [verklarende woorden lijst](app-attach-glossary.md). U kunt ook aan de slag met het [instellen van een app-koppeling](app-attach.md).
