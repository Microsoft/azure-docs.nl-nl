---
title: Fouten in azure Serial console | Microsoft Docs
description: Veelvoorkomende fouten in de Azure seriële console
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: cad12a55332a6c7898f9709776c58d7dba8dd81a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022833"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Veelvoorkomende fouten in de Azure seriële console
Er zijn een aantal bekende fouten in de Azure Serial console. Dit is een lijst met deze fouten en de stappen voor het oplossen ervan.

## <a name="common-errors"></a>Algemene fouten

Fout                             |   Oplossing
:---------------------------------|:--------------------------------------------|
"Voor Azure Serial console moeten diagnostische gegevens over opstarten zijn ingeschakeld. Klik hier om diagnostische gegevens over opstarten voor uw virtuele machine te configureren. | Zorg ervoor dat [Diagnostische gegevens over opstarten](boot-diagnostics.md) zijn ingeschakeld voor de virtuele machine of de VM-schaalset. Als u seriële console gebruikt op een exemplaar van een virtuele-machine schaalset, moet u ervoor zorgen dat uw exemplaar het meest recente model heeft.
"Voor Azure Serial console moet een virtuele machine worden uitgevoerd. Gebruik de knop Start hierboven om de virtuele machine te starten.  | De VM of het exemplaar van de virtuele-machine schaalset moet de status gestart hebben om toegang te krijgen tot de seriële console (uw VM mag niet worden gestopt of gespreid). Zorg ervoor dat de virtuele machine of het exemplaar van de VM-schaalset wordt uitgevoerd en probeer het opnieuw.
' Azure Serial console is niet ingeschakeld voor dit abonnement, neemt u contact op met de abonnements beheerder om in te scha kelen. ' | De seriële Azure-console kan worden uitgeschakeld op abonnements niveau. Als u een abonnements beheerder bent, kunt u [de Azure Serial console inschakelen en uitschakelen](./serial-console-enable-disable.md). Als u geen abonnements beheerder bent, kunt u contact met de beheerder van uw abonnement vinden voor de volgende stappen.
Er is een ' verboden ' antwoord aangetroffen bij het openen van het opslag account voor diagnostische gegevens over opstarten van deze virtuele machine. | Zorg ervoor dat diagnostische gegevens over opstarten geen account firewall hebben. Er is een toegankelijke opslag account voor diagnostische gegevens over opstarten nodig om de seriële console te laten functioneren. Seriële console per ontwerp kan niet werken met firewalls voor opslag accounts die zijn ingeschakeld in het opslag account voor diagnostische gegevens over opstarten.
U beschikt niet over de vereiste machtigingen om deze virtuele machine met de seriële console te gebruiken. Zorg ervoor dat u ten minste machtigingen voor de rol Inzender voor virtuele machines hebt.| Voor toegang tot de seriële console hebt u toegang op Inzender niveau of hoger nodig op uw VM of virtuele-machine schaalset. Zie de [pagina overzicht](serial-console-overview.md)voor meer informatie.
Het opslag account dat wordt gebruikt voor diagnostische gegevens over opstarten op deze VM is niet gevonden. Controleer of de diagnostische gegevens over opstarten zijn ingeschakeld voor deze VM. dit opslag account is niet verwijderd en u hebt toegang tot dit opslag account. | Controleer of u het opslag account voor diagnostische gegevens over opstarten niet hebt verwijderd voor uw VM of virtuele-machine schaalset
Er is een fout opgetreden tijdens de verbinding met de seriële console met de virtuele machine: ' ongeldige aanvraag ' (400) | Dit kan gebeuren als de URI van de opstart diagnose onjuist is. Zo is bijvoorbeeld ' http://' gebruikt in plaats van ' https://'. De diagnostische URI voor opstarten kan worden opgelost met deze opdracht: `az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
U beschikt niet over de vereiste machtigingen om te schrijven naar het opslag account voor diagnostische gegevens over opstarten voor deze VM. Zorg ervoor dat u ten minste beschikt over de machtigingen voor de VM-bijdrage | Voor Seriële console toegang is toegang op Inzender niveau vereist voor het opslag account voor diagnostische gegevens over opstarten. Zie de [pagina overzicht](serial-console-overview.md)voor meer informatie.
Kan de resource groep voor het opslag account voor diagnostische gegevens over opstarten niet bepalen *&lt; STORAGEACCOUNTNAME &gt;*. Controleer of de diagnostische gegevens over opstarten zijn ingeschakeld voor deze VM en of u toegang tot dit opslag account hebt. | Voor Seriële console toegang is toegang op Inzender niveau vereist voor het opslag account voor diagnostische gegevens over opstarten. Zie de [pagina overzicht](serial-console-overview.md)voor meer informatie.
Het inrichten voor deze VM is nog niet voltooid. Controleer of de virtuele machine volledig is geïmplementeerd en voer de verbinding met de seriële console opnieuw uit. | Uw VM of schaalset voor virtuele machines kan nog steeds worden ingericht. Wacht enige tijd en probeer het opnieuw.
De WebSocket is gesloten of kan niet worden geopend. | Mogelijk moet u toegang tot de firewall toevoegen aan `*.console.azure.com` . Een meer gedetailleerde maar meer benadering is het toestaan van firewall toegang tot de [IP-bereiken van het Microsoft Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653), die regel matig worden gewijzigd.
Seriële console werkt niet met een opslag account met behulp van Azure Data Lake Storage Gen2 met hiërarchische naam ruimten. | Dit is een bekend probleem met hiërarchische naam ruimten. Als u wilt beperken, moet u ervoor zorgen dat het opslag account voor diagnostische gegevens over opstarten van de virtuele machine niet is gemaakt met behulp van Azure Data Lake Storage Gen2. Deze optie kan alleen worden ingesteld bij het maken van een opslag account. Mogelijk moet u een afzonderlijk opslag account voor diagnostische gegevens over opstarten maken zonder dat Azure Data Lake Storage Gen2 ingeschakeld om dit probleem te verhelpen.
Er is een fout opgetreden in de verbinding met de seriële console naar de virtuele machine: ' verboden ' (SubscriptionNotEnabled)-abonnements naam niet gedefinieerd, id \<subscription id> heeft niet-ingeschakelde status niet gedefinieerd | Dit probleem kan optreden als het abonnement dat een gebruiker heeft gemaakt voor de Cloud Shell-opslag account is uitgeschakeld. Start Cloud Shell en [Voer de stappen uit die nodig zijn](../../cloud-shell/persisting-shell-storage.md#unmount-clouddrive-1) om een back-upopslag account voor Cloud shell in het huidige abonnement opnieuw in te richten.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [Azure Serial console voor Linux vm's](./serial-console-linux.md)
* Meer informatie over de [Azure Serial console voor Windows-vm's](./serial-console-windows.md)
