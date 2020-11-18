---
title: 'Zelfstudie: herstel na noodgevallen voor Linux-VM’s met Azure Site Recovery'
description: Leer hoe u herstel na noodgevallen van Linux-VM’s naar een andere Azure-regio kunt instellen met de Azure Site Recovery-service.
author: rayne-wiselman
ms.service: virtual-machines-linux
ms.subservice: recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: d14d276c798e40d417a8038aee5b7550e84f4114
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379785"
---
# <a name="tutorial-set-up-disaster-recovery-for-linux-virtual-machines"></a>Zelfstudie: Herstel na noodgevallen voor virtuele Linux-machines instellen


In deze zelfstudie ziet u hoe u herstel na noodgevallen kunt instellen voor Linux-VM’s. In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Herstel na noodgeval inschakelen voor een Linux-VM
> * Noodherstelanalyse uitvoeren
> * De virtuele machine niet meer repliceren na de analyse

Wanneer u replicatie voor een virtuele machine inschakelt, wordt de Site Recovery Mobility-service-extensie geïnstalleerd op de virtuele machine en wordt deze geregistreerd bij [Azure Site Recovery](../../site-recovery/site-recovery-overview.md). Tijdens de replicatie worden schrijfbewerkingen van de VM-schijf verzonden naar een cache-opslagaccount in de bronregio. Er worden gegevens naar de doelregio verzonden en er worden herstelpunten gegenereerd op basis van de gegevens.  Wanneer u een virtuele machine overbrengt naar een andere regio tijdens herstel na noodgeval, wordt een herstelpunt gebruikt om de virtuele machine in de doelregio te herstellen.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

1. Controleer of u een Azure-abonnement hebt waarmee u een VM kunt maken in de doelregio. Als u pas net uw gratis Azure-account hebt gemaakt, bent u de beheerder van het abonnement en hebt u de benodigde machtigingen.
2. Als u niet de abonnementsbeheerder bent, neemt u contact op met de beheerder en vraagt u deze het volgende aan u toe te wijzen:
    - De ingebouwde rol Inzender voor virtuele machines of specifieke machtigingen voor:
        - Het maken van een VM in het geselecteerde virtuele netwerk.
        - Schrijf naar een Azure Storage-account.
        - Schrijf naar een door Azure beheerde schijf.
     - De ingebouwde rol van Site Recovery-inzender om Site Recovery-bewerkingen in de kluis te beheren. 
3. Controleer of de Linux-VM op een ondersteund [besturingssysteem](../../site-recovery/azure-to-azure-support-matrix.md#linux) wordt uitgevoerd.
4. Als voor uitgaande VM-verbindingen een op een URL gebaseerde proxy wordt gebruikt, zorg er dan voor dat deze toegang heeft tot deze URL's. Het gebruik van een geverifieerde proxy wordt niet ondersteund.

    **Naam** | **Openbare cloud** | **Cloud van de overheid** | **Details**
    --- | --- | --- | ---
    Storage | `*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`| Gegevens van de VM naar het cache-opslagaccount in de bronregio schrijven. 
    Azure AD  | `login.microsoftonline.com` | `login.microsoftonline.us`| Autoriseren en verifiëren naar URL's van de Site Recovery-service. 
    Replicatie | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`  |Communicatie van VM met de Site Recovery-service. 
    Service Bus | `*.servicebus.windows.net` | `*.servicebus.usgovcloudapi.net` | VM schrijft naar Site Recovery voor bewaking en diagnostische gegevens. 

4. Als u netwerkbeveiligingsgroepen (NSG's) gebruikt om netwerkverkeer voor virtuele machines te beperken, maakt u NSG-regels die uitgaande connectiviteit (HTTPS 443) voor de virtuele machine toestaan ​​met behulp van deze servicetags (groepen IP-adressen). Probeer de regels eerst op een test-NSG.

    **Tag** | **Toestaan** 
    --- | --- 
    Opslagtag | Hiermee kunnen gegevens van de VM naar het cache-opslagaccount worden geschreven.
    Azure AD-tag | Hiermee hebt u toegang tot alle IP-adressen die overeenkomen met Azure AD.
    EventsHub-tag | Hiermee wordt toegang tot Site Recovery-bewaking toegestaan.
    AzureSiteRecovery-tag | Hiermee hebt u toegang tot de Site Recovery-service in een willekeurige regio.
    GuestAndHybridManagement | Gebruik deze optie als u de Site Recovery Mobility-agent automatisch wilt upgraden die wordt uitgevoerd op virtuele machines die zijn ingeschakeld voor replicatie.
5. Zorg ervoor dat VM's over de meest recente basiscertificaten beschikken. Op Linux-VM’s volgt u de richtlijnen van de Linux-distributeur voor het verkrijgen van de meest recente basiscertificaten en de certificaatintrekkingslijst op de VM.

## <a name="enable-disaster-recovery"></a>Herstel na noodgevallen inschakelen

1. Open de pagina VM-eigenschappen in Azure Portal.
2. Selecteer in **Bewerkingen** de optie **Herstel na noodgeval**.
3. Selecteer in **Basis** > **Doelregio** de regio waarnaar u de VM wilt repliceren. De bron- en doelregio's moeten zich in dezelfde Azure Active Directory-tenant bevinden.
4. Klik op **Replicatie beoordelen en starten**.

    :::image type="content" source="./media/tutorial-disaster-recovery/disaster-recovery.png" alt-text="Schakel replicatie in op de pagina voor herstel na noodgevallen van VM-eigenschappen.":::

5. Controleer de instellingen in **Replicatie beoordelen en starten**:

    - **Doelinstellingen**. Site Recovery komt standaard overeen met de broninstellingen om doelresources te maken.
    - **Opslaginstellingen - cache-opslagaccount**. Recovery maakt gebruik van een opslagaccount in de bronregio. Wijzigingen in de bron-VM worden in de cache van dit account opgeslagen voordat ze worden gerepliceerd naar de doellocatie.
    - **Opslaginstellingen - replica-schijf**. Met Site Recovery worden standaard replicaschijven in de doelregio gemaakt om de beheerde schijven van de bron-VM's met hetzelfde opslagtype (Standard of Premium) te spiegelen.
    - **Replicatie-instellingen**. Toont de kluisdetails en geeft aan dat herstelpunten die door Site Recovery zijn gemaakt 24 uur worden bewaard.
    - **Extensie-instellingen**. Geeft aan dat Site Recovery updates beheert voor de Site Recovery Mobility Service-extensie die is geïnstalleerd op VM's die u repliceert. Het updateproces wordt beheerd door aangegeven Azure Automation-account.

    :::image type="content" source="./media/tutorial-disaster-recovery/settings-summary.png" alt-text="Pagina met een samenvatting van de doel- en replicatie-instellingen.":::

2. Selecteer **Replicatie starten**. De implementatie wordt gestart en Site Recovery begint met het maken van doelresources. U kunt de replicatievoortgang bewaken via Meldingen.

    :::image type="content" source="./media/tutorial-disaster-recovery/notifications.png" alt-text="Melding voor voortgang van replicatie.":::

## <a name="check-vm-status"></a>VM-status controleren

Nadat de replicatietaak is voltooid, kunt u de replicatiestatus van de virtuele machine controleren.

1. Open de pagina VM-eigenschappen.
2. Selecteer in **Bewerkingen** de optie **Herstel na noodgeval**.
3. Vouw de sectie **Essentials** uit om de standaard waarden te bekijken voor de kluis, het replicatiebeleid en de doelinstellingen.
4. In **Integriteit en status** kunt u informatie ophalen over de replicatiestatus voor de virtuele machine, de agentversie, de gereedheid van de failover en de meest recente herstelpunten. 

    :::image type="content" source="./media/tutorial-disaster-recovery/essentials.png" alt-text="Essentials-weergave voor herstel na noodgeval voor VM.":::

5. Bekijk in **Infrastructuurweergave** een visueel overzicht van de bron- en doel-VM's, beheerde schijven en het cache-opslagaccount.

    :::image type="content" source="./media/tutorial-disaster-recovery/infrastructure.png" alt-text="Visuele infrastructuurkaart voor herstel na noodgevallen voor VM's.":::


## <a name="run-a-drill"></a>Een analyse uitvoeren

Voer een analyse uit om te controleren of herstel na noodgevallen naar verwachting werkt. Wanneer u een testfailover uitvoert, maakt deze een kopie van de virtuele machine, zonder dat dit van invloed is op de continue replicatie of uw productieomgeving.

1. Selecteer op de pagina Herstel na noodgevallen voor de VM de optie **testfailover**.
2. Laat in **testfailover** de standaardinstelling **Laatst verwerkt (lage RPO)** voor het herstelpunt staan.

   Deze optie biedt de laagste Recovery Point Objective (RPO) en draait doorgaans in de doelregio snel naar de virtuele machine. Eerst worden alle gegevens verwerkt die naar de Site Recovery-service zijn verzonden, om een herstelpunt voor elke VM te maken voordat er een failover naar wordt uitgevoerd. Voor dit herstelpunt worden alle gegevens gerepliceerd naar Site Recovery wanneer de failover werd geactiveerd.

3. Selecteer het virtuele netwerk waarin de VM zich na een failover bevindt. 

     :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-settings.png" alt-text="Pagina om opties voor testfailover in te stellen.":::

4. Het proces voor testfailover wordt gestart. U kunt de voortgang bewaken via Meldingen.

    :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-notification.png" alt-text="Meldingen voor testfailover."::: 
    
   Nadat de testfailover is voltooid, bevindt de virtuele machine zich in de status *Opschonen van testfailover in behandeling* op de pagina **Essentials**. 
  
## <a name="clean-up-resources"></a>Resources opschonen

De VM wordt na de analyse automatisch opgeschoond door Site Recovery. 
 
1. Selecteer **Opschonen van testfailover** om automatisch opschonen te starten.

    :::image type="content" source="./media/tutorial-disaster-recovery/start-cleanup.png" alt-text="Start opschoning op de pagina Essentials."::: 

6. Typ in **Opschonen van de testfailover** een notitie die u wilt opnemen voor de failover en selecteer vervolgens **Testen is voltooid. Verwijder de testfailover virtuele machine**. Selecteer vervolgens **OK**.

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test.png" alt-text="Pagina voor het vastleggen van notities en het verwijderen van de test-VM."::: 

7. Het verwijderingsproces wordt gestart. U kunt de voortgang bewaken via Meldingen.

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test-notification.png" alt-text="Meldingen voor het bewaken van het verwijderen van test-VM."::: 


### <a name="stop-replicating-the-vm"></a>Replicatie van de VM stoppen

Wanneer u een analyse voor herstel na noodgevallen hebt voltooid, kunt u het beste een volledige failover uitvoeren. Als u geen volledige failover wilt uitvoeren, kunt u de replicatie uitschakelen. Er gebeurt nu het volgende:

- Hiermee verwijdert u de VM uit de Site Recovery-lijst met gerepliceerde machines.
- Site Recovery-facturering voor de VM wordt stopgezet.
- De bronreplicatie-instellingen worden automatisch opgeschoond.

Stop de replicatie als volgt:

1. Selecteer op de pagina Herstel na noodgevallen voor de VM de optie **Replicatie uitschakelen**.
2. Selecteer in **Replicatie uitschakelen** de redenen waarom u de replicatie wilt uitschakelen. Selecteer vervolgens **OK**.

    :::image type="content" source="./media/tutorial-disaster-recovery/disable-replication.png" alt-text="Pagina om replicatie uit te schakelen en een reden op te geven."::: 


De Site Recovery-extensie die tijdens de replicatie op de virtuele machine is geïnstalleerd, wordt niet automatisch verwijderd. Als u replicatie voor de virtuele machine uitschakelt en u deze later niet opnieuw wilt repliceren, kunt u de Site Recovery-extensie als volgt handmatig verwijderen: 

1. Ga naar de VM > **Instellingen** > **Extensies**.
2. Selecteer op de pagina **Extensies** elke *Microsoft.Azure.RecoveryServices*-vermelding voor Linux.
3. Selecteer **Verwijderen** op de eigenschappenpagina voor de extensie.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u herstel na noodgevallen geconfigureerd voor een Azure-VM en een herstelanalyse uitgevoerd. U kunt nu een volledige failover uitvoeren voor de VM.

> [!div class="nextstepaction"]
> [Failover van een virtuele machine naar een andere regio uitvoeren](../../site-recovery/azure-to-azure-tutorial-dr-drill.md)
