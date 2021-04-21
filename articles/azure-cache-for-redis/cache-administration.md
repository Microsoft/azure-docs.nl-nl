---
title: Hoe beheer ik Azure Cache voor Redis
description: Meer informatie over het uitvoeren van beheertaken, zoals opnieuw opstarten en het plannen van updates voor Azure Cache voor Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7f8eb44ab301b211eaeb2dc3679c97714f91f0da
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833036"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>Hoe beheer ik Azure Cache voor Redis
In dit onderwerp wordt beschreven [](#reboot) hoe u beheertaken uitvoert, zoals het opnieuw opstarten en het plannen van [updates](#schedule-updates) voor Azure Cache voor Redis exemplaren.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Opnieuw opstarten
Op **de** blade Opnieuw opstarten kunt u een of meer knooppunten van uw cache opnieuw opstarten. Met deze mogelijkheid voor opnieuw opstarten kunt u uw toepassing testen op tolerantie als er een storing in een cache-knooppunt is.

![Schermopname met de menuoptie Opnieuw opstarten.](./media/cache-administration/redis-cache-administration-reboot.png)

Selecteer de knooppunten om opnieuw op te starten en klik op **Opnieuw opstarten.**

![Schermopname die laat zien welke knooppunten u opnieuw kunt opstarten.](./media/cache-administration/redis-cache-reboot.png)

Als u een Premium-cache hebt waarvoor clustering is ingeschakeld, kunt u selecteren welke shards van de cache opnieuw moeten worden opgestart.

![Opnieuw opstarten](./media/cache-administration/redis-cache-reboot-cluster.png)

Als u een of meer knooppunten van uw cache opnieuw wilt opstarten, selecteert u de gewenste knooppunten en klikt u op **Opnieuw opstarten.** Als u een Premium-cache hebt met clustering ingeschakeld, selecteert u de gewenste shards om opnieuw op te starten en klikt u vervolgens op **Opnieuw opstarten.** Na enkele minuten worden de geselecteerde knooppunten opnieuw opgestart en zijn ze een paar minuten later weer online.

De impact op clienttoepassingen is afhankelijk van de knooppunten die u opnieuw opstart.

* **Master:** wanneer het primaire knooppunt opnieuw wordt opgestart, Azure Cache voor Redis over naar het replica-knooppunt en promoveren naar het primaire knooppunt. Tijdens deze failover kan er een kort interval zijn waarin verbindingen met de cache kunnen mislukken.
* **Replica:** wanneer het replica-knooppunt opnieuw wordt opgestart, heeft dit doorgaans geen invloed op de cache-clients.
* **Zowel primaire als replica:** wanneer beide cacheknooppunten opnieuw worden opgestart, gaan alle gegevens verloren in de cache en mislukken verbindingen met de cache totdat het primaire knooppunt weer online komt. Als u gegevens persistentie hebt [geconfigureerd,](cache-how-to-premium-persistence.md)wordt de meest recente back-up hersteld wanneer de cache weer online komt, maar alle cache-schrijf schrijfgegevens die zijn opgetreden na de meest recente back-up, gaan verloren.
* Knooppunten van een **Premium-cache** met ingeschakelde clustering: wanneer u een of meer knooppunten van een Premium-cache opnieuw opstart terwijl clustering is ingeschakeld, is het gedrag voor de geselecteerde knooppunten hetzelfde als wanneer u het bijbehorende knooppunt of de bijbehorende knooppunten van een niet-geclusterde cache opnieuw opstart.

## <a name="reboot-faq"></a>Veelgestelde vragen over opnieuw opstarten
* [Welk knooppunt moet ik opnieuw opstarten om mijn toepassing te testen?](#which-node-should-i-reboot-to-test-my-application)
* [Kan ik de cache opnieuw opstarten om clientverbindingen te wissen?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Verlies ik gegevens uit mijn cache als ik opnieuw opstart?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Kan ik mijn cache opnieuw opstarten met PowerShell, CLI of andere beheerhulpprogramma's?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Welk knooppunt moet ik opnieuw opstarten om mijn toepassing te testen?
Als u de tolerantie van uw toepassing wilt testen tegen het uitvallen van het primaire knooppunt van uw cache, start u het **hoofd-knooppunt** opnieuw op. Als u de tolerantie van uw toepassing wilt testen tegen fouten in het replica-knooppunt, start u het **knooppunt Replica opnieuw** op. Als u de tolerantie van uw toepassing wilt testen tegen de totale storing in de cache, start u **Beide knooppunten opnieuw** op.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Kan ik de cache opnieuw opstarten om clientverbindingen te wissen?
Ja, als u de cache opnieuw opstart, worden alle clientverbindingen geweerd. Opnieuw opstarten kan handig zijn in het geval dat alle clientverbindingen worden gebruikt vanwege een logische fout of een fout in de clienttoepassing. Elke prijscategorie heeft verschillende [clientverbindingslimieten](cache-configure.md#default-redis-server-configuration) voor de verschillende grootten. Zodra deze limieten zijn bereikt, worden er geen clientverbindingen meer geaccepteerd. Het opnieuw opstarten van de cache biedt een manier om alle clientverbindingen te wissen.

> [!IMPORTANT]
> Als u de cache opnieuw opstart om clientverbindingen te wissen, maakt StackExchange.Redis automatisch opnieuw verbinding zodra het Redis-knooppunt weer online is. Als het onderliggende probleem niet is opgelost, kunnen de clientverbindingen nog steeds worden gebruikt.
> 
> 



### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Verlies ik gegevens uit mijn cache als ik opnieuw opstart?
Als u zowel  de hoofdknooppunten als de **replicaknooppunten** opnieuw opstart, gaan alle gegevens in de cache (of in die shard als u een Premium-cache gebruikt met ingeschakelde clustering) verloren, maar dit is ook niet gegarandeerd. Als u gegevens persistentie hebt [geconfigureerd,](cache-how-to-premium-persistence.md)wordt de meest recente back-up hersteld wanneer de cache weer online komt, maar alle cache-schrijf schrijfgegevens die zijn opgetreden nadat de back-up is gemaakt, gaan verloren.

Als u slechts één van de knooppunten opnieuw opstart, gaan gegevens doorgaans niet verloren, maar dat kan nog wel zo zijn. Als het primaire knooppunt bijvoorbeeld opnieuw wordt opgestart en er een cache wordt geschreven, gaan de gegevens uit de cache schrijven verloren. Een ander scenario voor gegevensverlies is als u één knooppunt opnieuw opstart en het andere knooppunt uitvallen vanwege een storing op hetzelfde moment. Zie Wat is er gebeurd met mijn gegevens in Redis? voor meer informatie over mogelijke oorzaken [voor gegevensverlies.](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Kan ik mijn cache opnieuw opstarten met PowerShell, CLI of andere beheerhulpprogramma's?
Ja, zie Een apparaat opnieuw opstarten voor [PowerShell-Azure Cache voor Redis.](cache-how-to-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis)

## <a name="schedule-updates"></a>Updates plannen
Op de blade **Updates** plannen kunt u een onderhoudsvenster voor uw cache-exemplaar aanwijzen. Met een onderhoudsvenster kunt u de dag(en) en tijd(en) van een week bepalen waarin de VM('s) die als host voor uw cache worden gehost, kunnen worden bijgewerkt. Azure Cache voor Redis wordt er alles aan gedaan om de Redis-serversoftware te starten en bij te werken binnen het opgegeven tijdvenster dat u definieert.

> [!NOTE] 
> Het onderhoudsvenster is van toepassing op Redis-serverupdates en updates voor het besturingssysteem van de VM's die als host voor de cache worden gebruikt. Het onderhoudsvenster is niet van toepassing op updates van host-besturingssystemen op de hosts die als host worden gebruikt voor de cache-VM's of andere Azure-netwerken onderdelen. In zeldzame gevallen, waarbij caches worden gehost op oudere modellen (u kunt zien of uw cache een ouder model heeft als de DNS-naam van de cache wordt overgeslagen op het achtervoegsel 'cloudapp.net', 'chinacloudapp.cn', 'usgovcloudapi.net' of 'cloudapi.de'), is het onderhoudsvenster ook niet van toepassing op updates van gast besturingssystemen.
>


![Updates plannen](./media/cache-administration/redis-schedule-updates.png)

Als u een onderhoudsvenster wilt opgeven, controleert u de gewenste dagen en geeft u het beginuur van het onderhoudsvenster voor elke dag op en klikt u op **OK.** Houd er rekening mee dat de tijd van het onderhoudsvenster in UTC is. 

Het standaardonderhoudsvenster voor updates is vijf uur. Deze waarde kan niet worden geconfigureerd vanuit de Azure Portal, maar u kunt deze configureren in PowerShell met behulp van de parameter van de `MaintenanceWindow` cmdlet [New-AzRedisCacheScheduleEntry.](/powershell/module/az.rediscache/new-azrediscachescheduleentry) Zie Kan ik geplande updates beheren met PowerShell, CLI of andere beheerhulpprogramma's voor meer informatie?

## <a name="schedule-updates-faq"></a>Veelgestelde vragen over het plannen van updates
* [Wanneer vinden er updates plaats als ik de functie voor het plannen van updates niet gebruik?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Welk type updates worden er aangebracht tijdens het geplande onderhoudsvenster?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Kan ik geplande updates beheren met PowerShell, CLI of andere beheerhulpprogramma's?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Wanneer vinden er updates plaats als ik de functie voor het plannen van updates niet gebruik?
Als u geen onderhoudsvenster opgeeft, kunnen er op elk moment updates worden aangebracht.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Welk type updates worden er aangebracht tijdens het geplande onderhoudsvenster?
Alleen Redis-serverupdates worden uitgevoerd tijdens het geplande onderhoudsvenster. Het onderhoudsvenster is niet van toepassing op Azure-updates of -updates voor het VM-besturingssysteem.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Kan ik geplande updates beheren met Behulp van PowerShell, CLI of andere beheerhulpprogramma's?
Ja, u kunt uw geplande updates beheren met behulp van de volgende PowerShell-cmdlets:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [New-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remove-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

## <a name="next-steps"></a>Volgende stappen
Meer informatie over Azure Cache voor Redis functies.

* [Azure Cache voor Redis servicelagen](cache-overview.md#service-tiers)

