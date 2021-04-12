---
title: Hoge beschikbaarheid en herstel na noodgevallen
titleSuffix: Azure Digital Twins
description: Hierin worden de Azure-en Azure Digital Apparaatdubbels-functies beschreven waarmee u Maxi maal beschik bare Azure IoT-oplossingen kunt bouwen met mogelijkheden voor herstel na nood gevallen.
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 3336a086fbe8f4291f752836a610cd80b773ec2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98790813"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Azure Digital Apparaatdubbels hoge Beschik baarheid en herstel na nood gevallen

Een belang rijk aandachtspunt voor flexibele IoT-oplossingen is bedrijfs continuïteit en herstel na nood gevallen. Ontwerpen voor **hoge Beschik baarheid (ha)** en herstel **na nood gevallen (Dr)** kan u helpen om de juiste uptime-doelen voor uw oplossing te definiëren en te bereiken.

In dit artikel worden de HA-en DR-functies beschreven die specifiek worden aangeboden door de Azure Digital Apparaatdubbels-service.

Azure Digital Apparaatdubbels ondersteunt deze functie opties:
* *Intra regio ha* : ingebouwde redundantie voor het leveren van de uptime van de service
* *Meerdere regio's van Dr* : een failover naar een geografisch gekoppelde Azure-regio in het geval van een onverwachte Data Center-fout

U kunt ook het gedeelte [*Aanbevolen procedures*](#best-practices) voor algemene Azure-richt lijnen over het ontwerpen van ha/Dr bekijken.

## <a name="intra-region-ha"></a>Binnenste regio HA
 
Azure Digital Apparaatdubbels voorziet in een binnenste regio HA door redundantie in de service te implementeren. Dit wordt weer gegeven in de [service-Sla](https://azure.microsoft.com/support/legal/sla/digital-twins) voor de actieve tijds duur. **Voor de ontwikkel aars van een Azure Digital Apparaatdubbels-oplossing is geen extra werk vereist om deze HA-functies te benutten.** Hoewel Azure Digital Apparaatdubbels een redelijk hoge uptime biedt, kunnen tijdelijke fouten nog steeds worden verwacht, net zoals bij een gedistribueerd computing platform. Het juiste beleid voor opnieuw proberen moet zijn ingebouwd in de onderdelen die communiceren met een Cloud toepassing om te kunnen omgaan met tijdelijke fouten.

## <a name="cross-region-dr"></a>DR-regio tussen regio's

Er kunnen zeldzame situaties optreden wanneer een Data Center uitvallende storingen ondervindt als gevolg van stroom storingen of andere gebeurtenissen in de regio. Dergelijke gebeurtenissen zijn zeldzaam, en tijdens dergelijke storingen is het mogelijk dat de mogelijkheden voor de intra regio HA die hierboven worden beschreven, niet kunnen helpen. Azure Digital Apparaatdubbels lost dit op met door micro soft geïnitieerde failover.

Door micro soft **geïnitieerde failover** wordt door micro soft in zeldzame gevallen uitgevoerd om alle Azure Digital apparaatdubbels-exemplaren van een betrokken regio over te gaan naar de overeenkomstige geografische paar regio. Dit proces is een standaard optie (zonder een manier om gebruikers te kiezen) en vereist geen tussen komst van de gebruiker. Micro soft behoudt zich het recht voor om te bepalen wanneer deze optie wordt uitgeoefend. Dit mechanisme heeft geen toestemming van een gebruiker voor het uitvoeren van een failover van het gebruikers exemplaar.

>[!NOTE]
> Sommige Azure-Services bieden ook een extra optie met de door de **klant geïnitieerde failover**, waarmee klanten een failover voor hun exemplaar kunnen initiëren, bijvoorbeeld om een Dr-analyse uit te voeren. Dit mechanisme wordt momenteel **niet ondersteund** door Azure Digital apparaatdubbels. 

## <a name="monitor-service-health"></a>Servicestatus bewaken

Als er een failover wordt uitgevoerd voor Azure Digital Apparaatdubbels-exemplaren, kunt u het proces bewaken met het hulp programma [Azure service Health](../service-health/service-health-overview.md) . Service Health houdt de status van uw Azure-Services in verschillende regio's en abonnementen bij en deelt de communicatie met de service die invloed heeft op storingen en uitval tijd.

Tijdens een failover-gebeurtenis kan Service Health een indicatie geven wanneer uw service niet actief is en wanneer er een back-up wordt gemaakt.

Service Health gebeurtenissen weer geven...
1. Navigeer naar [service Health](https://portal.azure.com/?feature.customportal=false#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues) in het Azure Portal (u kunt deze koppeling gebruiken of zoeken met de zoek balk van de portal).
1. Gebruik het menu aan de linkerkant om over te scha kelen naar de pagina *status geschiedenis* .
1. Zoek naar een *probleem naam* die begint met **Azure Digital apparaatdubbels** en selecteer deze.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/navigate.png" alt-text="Scherm afbeelding van de Azure Portal de pagina met de status geschiedenis wordt weer gegeven. Er is een lijst met verschillende problemen van de afgelopen paar dagen en een probleem met de naam ' Azure Digital Apparaatdubbels-Europa-west-Reduced ' is gemarkeerd." lightbox="media/concepts-high-availability-disaster-recovery/navigate.png":::

1. Voor algemene informatie over de onderbreking bekijkt u het tabblad *samen vatting* .

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/summary.png" alt-text="Op de pagina status geschiedenis is het tabblad samen vatting gemarkeerd. Op het tabblad wordt algemene informatie weer gegeven, zoals de resource die is beïnvloed, de regio en het abonnement." lightbox="media/concepts-high-availability-disaster-recovery/summary.png":::
1. Voor meer informatie over en updates van het probleem in de loop van de tijd bekijkt u het tabblad *updates van problemen* .

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/issue-updates.png" alt-text="Op de pagina status geschiedenis is het tabblad updates van problemen gemarkeerd. Op het tabblad worden verschillende vermeldingen weer gegeven met de huidige status van een dag geleden." lightbox="media/concepts-high-availability-disaster-recovery/issue-updates.png":::


Houd er rekening mee dat de informatie die in dit hulp programma wordt weer gegeven, niet specifiek is voor één Azure Digital-instantie. Nadat u Service Health hebt gebruikt om te begrijpen wat er gebeurt met de Azure Digital Apparaatdubbels-service in een bepaalde regio of abonnement, kunt u een stap verder volgen met behulp van het [resource Health-hulp programma](troubleshoot-resource-health.md) om in te zoomen op specifieke instanties en te controleren of ze worden beïnvloed.

## <a name="best-practices"></a>Aanbevolen procedures

Raadpleeg de volgende Azure-richt lijnen in dit onderwerp voor aanbevolen procedures op HA/DR: 
* In het artikel [*technische richt lijnen voor Azure-bedrijfs continuïteit*](/azure/architecture/framework/resiliency/overview) wordt een algemeen framework beschreven waarmee u rekening moet houden met bedrijfs continuïteit en herstel na nood gevallen. 
* Het document [*nood herstel en hoge Beschik baarheid voor Azure-toepassingen*](/azure/architecture/framework/resiliency/backup-and-recovery) biedt architectuur richtlijnen voor strategieën voor Azure-toepassingen om hoge beschik BAARHEID (ha) en herstel na nood gevallen (Dr) te kunnen uitvoeren.

## <a name="next-steps"></a>Volgende stappen 

Lees meer over aan de slag met Azure Digital Apparaatdubbels-oplossingen:
 
* [*Wat is Azure Digital Twins?*](overview.md)
* [*Quick Start: een voorbeeld scenario verkennen*](quickstart-adt-explorer.md)