---
title: Windows Defender firewall-gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Schakel de Windows Firewall-connector in azure Sentinel in om eenvoudig Firewall gebeurtenissen van Windows-machines te streamen waarop Log Analytics agents zijn geïnstalleerd.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2020
ms.author: yelevin
ms.openlocfilehash: cf7e389fc4a8a8dfa88691dc034611cae3471731
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94655337"
---
# <a name="connect-windows-defender-firewall-with-advanced-security-to-azure-sentinel"></a>Windows Defender Firewall met geavanceerde beveiliging verbinden met Azure Sentinel

De [Windows Defender Firewall met Advanced Security](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) connector staat Azure Sentinel toe om Windows Defender firewall eenvoudig op te nemen in geavanceerde beveiligings logboeken van alle Windows-computers in uw werk ruimte. Met deze verbinding kunt u Windows Firewall gebeurtenissen in uw werkmappen weer geven en analyseren, om ze te gebruiken bij het maken van aangepaste waarschuwingen en om deze op te nemen in uw beveiligings onderzoeken, waardoor u meer inzicht hebt in het netwerk van uw organisatie en de mogelijkheden voor beveiligings bewerkingen kunt verbeteren. 

De oplossing verzamelt Windows Firewall-gebeurtenissen van de Windows-computers waarop een Log Analytics-agent is geïnstalleerd. 

> [!NOTE]
> - Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.
>
> - Als Azure Defender-waarschuwingen van Azure Security Center al zijn verzameld in de Azure Sentinel-werk ruimte, is het niet nodig om de Windows Firewall oplossing via deze connector in te scha kelen. Als u de functie echter hebt ingeschakeld, worden er geen dubbele gegevens weer gegeven. 

## <a name="prerequisites"></a>Vereisten

- U moet de machtigingen lezen en schrijven hebben voor de werk ruimte waarmee de computers die u wilt bewaken, zijn verbonden.

- U moet de rol **log Analytics Inzender** toewijzen aan de SecurityInsights-oplossing in die werk ruimte, naast eventuele **Azure-Sentinel** -rollen. [Meer informatie](../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="enable-the-connector"></a>De connector inschakelen 

1. Selecteer in de Azure-Sentinel-Portal de optie **Data connectors** in het navigatie menu.

1. Selecteer **Windows Firewall** in de galerie Connect oren en klik op de **pagina connector openen**.

### <a name="instructions-tab"></a>Tabblad instructies

- **Als uw Windows-computers zich in azure bevinden:**

    1. Selecteer **agent installeren op virtuele machine van Azure Windows**.

    1. Klik op de koppeling **& installatie agent voor Azure Windows-machines installeren >** die wordt weer gegeven.

    1. Selecteer in de lijst **virtuele machines** de Windows-machine die u wilt streamen naar Azure Sentinel. (U kunt **Windows** selecteren in het kolom filter van het besturings systeem om ervoor te zorgen dat alleen Windows-vm's worden weer gegeven).

    1. Klik in het venster dat wordt geopend voor die VM op **verbinden**.

    1. Ga terug naar het **virtual machines** venster en herhaal de vorige twee stappen voor andere vm's die u wilt verbinden. Wanneer u klaar bent, keert u terug naar het deel venster **Windows Firewall** .

- **Als uw Windows-computer geen Azure-VM is:**

    1. Selecteer **agent installeren op niet-Azure Windows-machine**.

    1. Klik op de **& installatie agent voor niet-Azure Windows-machines downloaden >** die wordt weer gegeven.

    1. Selecteer in het deel venster **agents beheren** de optie **Windows-agent (64 bits) downloaden** of **Windows-agent (32 bits) downloaden**, indien nodig.

    1. Kopieer de **werk ruimte-id**, **primaire sleutel** en **secundaire sleutel** teken reeksen naar een tekst bestand. Kopieer het bestand en het gedownloade installatie bestand naar uw Windows-computer. Voer het installatie bestand uit en voer, wanneer u hierom wordt gevraagd, de ID en de sleutel teken reeksen in het tekst bestand tijdens de installatie in.

    1. Ga terug naar het deel venster **Windows Firewall** .

1. Klik op **oplossing installeren**.

### <a name="next-steps-tab"></a>Tabblad volgende stappen

- Bekijk de beschik bare aanbevolen werkmappen en query voorbeelden die zijn gebundeld met de **Windows Firewall** Data Connector om inzicht te krijgen in uw Windows Firewall-logboek gegevens.

- Als u gegevens van Windows Firewall in **Logboeken** wilt opvragen, typt u **WindowsFirewall** in het query venster.

## <a name="validate-connectivity"></a>Connectiviteit valideren
 
Omdat Windows Firewall-Logboeken alleen worden verzonden naar Azure Sentinel wanneer het lokale logboek bestand de capaciteit bereikt, zal het logboek in de standaard grootte van 4096 KB waarschijnlijk resulteren in een hoge latentie van de verzameling. U kunt de latentie verlagen door de grootte van het logboek bestand te verlagen. Zie de instructies voor [het configureren van het Windows Firewall logboek](/windows/security/threat-protection/windows-firewall/configure-the-windows-firewall-log). Houd er rekening mee dat bij het definiëren van de minimale mogelijke logboek grootte (1 KB) de latentie van de verzameling vrijwel wordt geëlimineerd. Dit kan ook een negatieve invloed hebben op de prestaties van de lokale computer. 

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Windows Firewall verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).