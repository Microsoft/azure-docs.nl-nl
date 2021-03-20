---
title: Over herstel plannen in Azure Site Recovery
description: Meer informatie over herstel plannen in Azure Site Recovery.
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 8d191781cacc37242dd1be31d6cb87ef196e5e7a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84343911"
---
# <a name="about-recovery-plans"></a>Over herstelplannen

In dit artikel vindt u een overzicht van herstel plannen in [Azure site Recovery](site-recovery-overview.md).

Een herstel plan verzamelt computers in herstel groepen voor het doel van de failover. Met een herstel plan kunt u een systematisch herstel proces definiëren door kleine onafhankelijke eenheden te maken waarvoor u een failover wilt uitvoeren. Een eenheid vertegenwoordigt meestal een app in uw omgeving.

- Een herstel plan definieert hoe machine failover wordt uitgevoerd en de volg orde waarin ze worden gestart na een failover.
- Herstel plannen kunnen worden gebruikt voor zowel failover naar als failback vanuit Azure.
- Maxi maal 100 beveiligde instanties kunnen worden toegevoegd aan één herstel plan.
- U kunt een plan aanpassen door er bestellingen, instructies en taken aan toe te voegen.
- Nadat een plan is gedefinieerd, kunt u er een failover op uitvoeren.
- Er kan in meerdere herstel plannen naar machines worden verwezen, waarbij de volgende plannen de implementatie/het starten van een machine overs Laan als deze eerder is geïmplementeerd met een ander herstel plan.



### <a name="why-use-a-recovery-plan"></a>Waarom een herstel plan gebruiken?

Herstel plannen gebruiken voor:

* Modeleer een app rond de bijbehorende afhankelijkheden.
* Herstel taken automatiseren voor het beperken van de beoogde herstel tijd (RTO).
* Controleer of u bent voor bereid op migratie of herstel na nood gevallen door ervoor te zorgen dat uw apps deel uitmaken van een herstel plan.
* Voer testfailover uit op herstel plannen om ervoor te zorgen dat nood herstel of migratie naar verwachting werkt.


## <a name="model-apps"></a>Apps model leren 
U kunt een herstel groep plannen en maken om app-specifieke eigenschappen vast te leggen. Laten we bijvoorbeeld eens kijken naar een typische toepassing met drie lagen met een back-end van SQL Server, middleware en een Webfront-end. Normaal gesp roken past u het herstel plan aan zodat de computers in elke laag in de juiste volg orde worden gestart na een failover.

- De SQL-back-end moet eerst worden gestart, de middleware volgende en uiteindelijk het Webfront-end.
- Deze start volgorde zorgt ervoor dat de app werkt op het moment dat de laatste machine wordt gestart.
- Deze volg orde zorgt ervoor dat de SQL Server laag al wordt uitgevoerd wanneer de middleware wordt gestart en probeert verbinding te maken met de laag SQL Server. 
- Deze volg orde zorgt er ook voor dat de front-end-server de laatste keer wordt gestart, zodat eind gebruikers geen verbinding met de app-URL kunnen maken voordat alle onderdelen actief zijn, en de app klaar is om aanvragen te accepteren.

Als u deze order wilt maken, voegt u groepen toe aan de herstel groep en voegt u computers toe aan de groepen.
- Waar order is opgegeven, wordt sequentiëren gebruikt. Acties worden parallel uitgevoerd, waar nodig, om de RTO van toepassings herstel te verbeteren.
- Failover van machines in één groep wordt parallel uitgevoerd.
- Voor machines in verschillende groepen wordt een failover uitgevoerd in de groeps volgorde, zodat groep 2-machines de testfailover alleen starten nadat op alle computers in groep 1 een fout is opgetreden en de bewerking is gestart.

    ![Voor beeld van herstel plan](./media/recovery-plan-overview/rp.png)

Wanneer u deze aanpassing uitvoert, gebeurt het volgende wanneer u een failover in het herstel plan voert: 

1. Bij een afsluit stap wordt geprobeerd om de on-premises machines uit te scha kelen. De uitzonde ring hierop is als u een testfailover uitvoert. in dat geval wordt de primaire site actief blijft. 
2. Bij het afsluiten wordt een parallelle failover geactiveerd van alle computers in het herstel plan.
3. De failover bereidt virtuele machine schijven voor met behulp van gerepliceerde gegevens.
4. De opstart groepen worden in volg orde uitgevoerd en de computers in elke groep worden gestart. Eerst groep 1 wordt uitgevoerd, vervolgens groep 2 en tenslotte groep 3. Als een groep meer dan één computer bevat, worden alle machines parallel gestart.


## <a name="automate-tasks-in-recovery-plans"></a>Taken automatiseren in herstel plannen

Het herstellen van grote toepassingen kan een complexe taak zijn. Hand matige stappen maken het proces gevoelig voor fouten en de persoon die de failover uitvoert, is mogelijk niet op de hoogte van alle app-complexiteit. U kunt een herstel plan gebruiken om een order in te stellen en de acties te automatiseren die nodig zijn bij elke stap, met Azure Automation runbooks voor failover naar Azure of scripts. Voor taken die niet kunnen worden geautomatiseerd, kunt u pauzes voor hand matige acties invoegen in herstel plannen. U kunt een aantal typen taken configureren:

* **Taken op de virtuele Azure-machine na een failover**: wanneer u een failover uitvoert naar Azure, moet u doorgaans acties uitvoeren, zodat u na een failover verbinding kunt maken met de virtuele machine. Bijvoorbeeld: 
    * Maak een openbaar IP-adres op de Azure-VM.
    * Wijs een netwerk beveiligings groep toe aan de netwerk adapter van de virtuele Azure-machine.
    * Voeg een load balancer toe aan een beschikbaarheidsset.
* **Taken in de VM na een failover**: met deze taken wordt de app die op de computer wordt uitgevoerd, opnieuw geconfigureerd zodat deze goed blijft werken in de nieuwe omgeving. Bijvoorbeeld:
    * Wijzig de data base-connection string in de computer.
    * Wijzig de webserver configuratie of-regels.


### <a name="run-a-test-failover-on-recovery-plans"></a>Een testfailover uitvoeren op herstel plannen

U kunt een herstel plan gebruiken om een testfailover te activeren. Gebruik de volgende aanbevolen procedures:

- Voltooi altijd een testfailover voor een app voordat u een volledige failover uitvoert. Met testfailover kunt u controleren of de app op de herstel site komt.
- Als u weet dat u iets hebt gemist, kunt u een opschoon bewerking activeren en de testfailover opnieuw uitvoeren. 
- Voer een testfailover meerdere keren uit, totdat u zeker weet dat de app probleemloos wordt hersteld.
- Omdat elke app uniek is, moet u herstel plannen maken die zijn aangepast voor elke toepassing en een testfailover uitvoeren op elk van de apps.
- Apps en hun afhankelijkheden worden regel matig gewijzigd. Voer elk kwar taal een testfailover voor elke app uit om ervoor te zorgen dat herstel plannen up-to-date zijn.

    ![Scherm afbeelding van een voor beeld van een test herstel plan in Site Recovery](./media/recovery-plan-overview/rptest.png)

## <a name="watch-a-recovery-plan-video"></a>Een video van een herstel plan bekijken

Bekijk een snelle voorbeeld video met een on-click failover voor een herstel plan voor een WordPress-app met twee lagen.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Volgende stappen

- Een herstel plan [maken](site-recovery-create-recovery-plans.md) .
- [Voer](site-recovery-failover.md) failovers uit. 
