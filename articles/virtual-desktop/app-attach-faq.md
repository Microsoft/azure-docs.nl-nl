---
title: Windows Virtual Desktop veelgestelde vragen over msix-app koppelen - Azure
description: Veelgestelde vragen over msix-app-attach voor Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 6057f4a76f274e34b036ea352a3691b34d24b3a1
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835358"
---
# <a name="msix-app-attach-faq"></a>Veelgestelde vragen over het koppelen van MSIX-apps

In dit artikel vindt u antwoorden op veelgestelde vragen over msix-app-attach voor Windows Virtual Desktop.

## <a name="whats-the-difference-between-msix-and-msix-app-attach"></a>Wat is het verschil tussen MSIX en MSIX-app-attach?

MSIX is een pakketindeling voor apps, terwijl MSIX-app-attach de functie is waarmee MSIX-pakketten aan uw implementatie worden leveren.

## <a name="does-msix-app-attach-use-fslogix"></a>Maakt msix-app-attach gebruik van FSLogix?

De MSIX-app-attach maakt geen gebruik van FSLogix. MsIX app attach en FSLogix zijn echter ontworpen om samen te werken om een naadloze gebruikerservaring te bieden.

## <a name="can-i-use-the-msix-app-attach-outside-of-windows-virtual-desktop"></a>Kan ik de MSIX-app-attach buiten de Windows Virtual Desktop?

De API's die de MSIX-app koppelen, zijn beschikbaar voor Windows 10 Enterprise. Deze API's kunnen buiten de Windows Virtual Desktop. Er is echter geen beheervlak voor de MSIX-app-attach buiten Windows Virtual Desktop.

## <a name="how-do-i-get-an-msix-package"></a>Hoe kan ik MSIX-pakket ontvangen?

Uw softwareleverancier geeft u een MSIX-pakket. U kunt ook niet-MSIX-pakketten converteren naar MSIX. Meer informatie in [How to move your existing installers to MSIX (Bestaande installatieprogramma's verplaatsen naar MSIX).](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix)

## <a name="which-operating-systems-support-msix-app-attach"></a>Welke besturingssystemen ondersteunen msix-app-attach?

Windows 10 Enterprise en Windows 10 Enterprise multisessie, versie 2004 of hoger.

## <a name="is-msix-app-attach-currently-generally-available"></a>Is de MSIX-app-attach momenteel algemeen beschikbaar?

De MSIX-app-attach maakt deel uit van Windows 10 Enterprise en Windows 10 Enterprise Multi-session, versie 2004 of hoger. Beide besturingssystemen zijn momenteel algemeen beschikbaar. 

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>Kan ik de MSIX-app koppelen buiten Windows Virtual Desktop?

API's voor MSIX- en MSIX-app-attach maken deel uit van Windows 10 Enterprise en Windows 10 Enterprise Multi-session, versie 2004 en hoger. We bieden momenteel geen beheersoftware voor msix-app-attach buiten Windows Virtual Desktop.

## <a name="can-i-run-two-versions-of-the-same-application-at-the-same-time"></a>Kan ik twee versies van dezelfde toepassing tegelijk uitvoeren?

Voor het gelijktijdig uitvoeren van twee versies van dezelfde MSIX-toepassingen moet de MSIX-pakketfamilie die is gedefinieerd in het appxmanifest.xml-bestand voor elke app verschillen.

## <a name="should-i-disable-auto-update-when-using-msix-app-attach"></a>Moet ik automatisch bijwerken uitschakelen wanneer ik de MSIX-app-bijlage gebruik?

Ja. De MSIX-app-attach biedt geen ondersteuning voor automatisch bijwerken voor MSIX-toepassingen.

## <a name="how-do-permissions-work-with-msix-app-attach"></a>Hoe werken machtigingen met msix-app-attach?

Alle virtuele machines (VM's) in een hostgroep die de MSIX-app-attach gebruiken, moeten leesmachtigingen hebben voor de bestands share waarin de MSIX-afbeeldingen worden opgeslagen. Als er ook Azure Files gebruikt, moeten ze zowel op rollen gebaseerd toegangsbeheer (RBAC) als NTFS-machtigingen (New Technology File System) krijgen.

## <a name="can-i-use-msix-app-attach-for-http-or-https"></a>Kan ik de MSIX-app koppelen voor HTTP of HTTPs?

Het gebruik van MSIX-app-attach via HTTP of HTTPs wordt momenteel niet ondersteund.

## <a name="can-i-restage-the-same-msix-application"></a>Kan ik dezelfde MSIX-toepassing een andere toepassing geven?

Ja. U kunt toepassingen die u al hebt ge restageerd, herstellen en dit mag geen fouten veroorzaken.

## <a name="does-msix-app-attach-support-self-signed-certificates"></a>Ondersteunt het koppelen van de MSIX-app zelf-ondertekende certificaten?

Ja. U moet het zelf-ondertekende certificaat installeren op alle sessiehost-VM's waarop de MSIX-app-attach wordt gebruikt om de zelf-ondertekende toepassing te hosten.

## <a name="what-applications-can-i-repackage-to-msix"></a>Welke toepassingen kan ik opnieuw inpakken naar MSIX?

Elke toepassing maakt gebruik van verschillende functies van het besturingssysteem, programmeertalen en frameworks. Als u uw toepassing opnieuw wilt inpakken, volgt u de instructies in Uw bestaande installatieprogramma's [verplaatsen naar MSIX.](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix) U vindt een lijst met de dingen die u nodig hebt om een toepassing opnieuw te verpakken in Voorbereiding voor het [verpakken van een bureaubladtoepassing.](/windows/msix/desktop/desktop-to-uwp-prepare) 

Bepaalde toepassingen kunnen niet gelaagd zijn, wat betekent dat ze niet opnieuw kunnen worden verpakt in een MSIX-bestand. Hier ziet u een lijst met de toepassingen die niet opnieuw kunnen worden verpakt:

- Stuurprogramma's 
- Active-X of Silverlight
- VPN-clients
- Antivirusprogramma

## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over het koppelen van de MSIX-app, raadpleegt u ons [overzicht en](what-is-app-attach.md) de [verklarende woordenlijst](app-attach-glossary.md). Ga anders aan de slag met [App koppelen instellen.](app-attach.md)
