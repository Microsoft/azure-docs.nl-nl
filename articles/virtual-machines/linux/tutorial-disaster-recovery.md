---
title: 'Zelfstudie: herstel na noodgevallen voor Linux-VM’s met Azure Site Recovery'
description: Leer hoe u herstel na noodgevallen van Linux-VM’s naar een andere Azure-regio kunt instellen met de Azure Site Recovery-service.
author: rayne-wiselman
ms.service: virtual-machines
ms.collection: linux
ms.subservice: recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b5e83f883b5e1e35842ab128e4732e993fb937a0
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383643"
---
# <a name="tutorial-set-up-disaster-recovery-for-linux-virtual-machines"></a>Zelfstudie: Herstel na noodgevallen voor virtuele Linux-machines instellen

In deze zelfstudie ziet u hoe u herstel na noodgevallen kunt instellen voor Linux-VM’s. In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Herstel na noodgeval inschakelen voor een Linux-VM
> * Een nood herstel analyse uitvoeren om te controleren of deze werkt zoals verwacht
> * De virtuele machine niet meer repliceren na de analyse

Wanneer u replicatie voor een virtuele machine inschakelt, wordt de Site Recovery Mobility-service-extensie geïnstalleerd op de virtuele machine en wordt deze geregistreerd bij [Azure Site Recovery](../../site-recovery/site-recovery-overview.md). Tijdens de replicatie worden VM-schijf schrijf bewerkingen verzonden naar een cache-opslag account in de bron-VM-regio. Er worden gegevens naar de doelregio verzonden en er worden herstelpunten gegenereerd op basis van de gegevens.  Wanneer u een virtuele machine naar een andere regio tijdens het herstel na nood gevallen hebt mislukt, wordt een herstel punt gebruikt voor het maken van een virtuele machine in de doel regio.

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

## <a name="create-a-vm-and-enable-disaster-recovery"></a>Een virtuele machine maken en nood herstel inschakelen

U kunt eventueel herstel na nood gevallen inschakelen wanneer u een VM maakt.

1. [Maak een virtuele Linux-machine](quick-create-portal.md).
2. Selecteer op het tabblad **beheer** onder **site Recovery** **nood herstel inschakelen**.
3. Selecteer in **secundaire regio** de doel regio waarnaar u de virtuele machine voor herstel na nood geval wilt repliceren.
4. Selecteer bij **secundair abonnement** het doel abonnement waarin de doel-VM wordt gemaakt. De doel-VM wordt gemaakt wanneer u een failover van de bron-VM van de bron regio naar de doel regio maakt.
5. Selecteer in **Recovery Services kluis** de kluis die u wilt gebruiken voor de replicatie. Als u geen kluis hebt, selecteert u **nieuwe maken**. Selecteer een resource groep waarin de kluis moet worden geplaatst en geef een kluis naam op.
6. In **site Recovery beleid**, behoud het standaard beleid of selecteer **nieuwe maken** om aangepaste waarden in te stellen.

    - Herstel punten worden gemaakt op basis van moment opnamen van VM-schijven die op een bepaald moment worden uitgevoerd. Wanneer u een failover voor een virtuele machine doorvoert, gebruikt u een herstel punt om de virtuele machine in de doel regio te herstellen. 
    - Er wordt om de vijf minuten een crash consistent herstel punt gemaakt. Deze instelling kan niet worden gewijzigd. Een crash consistente moment opname legt gegevens vast die zich op de schijf bevonden toen de moment opname werd gemaakt. Het bevat niets in het geheugen. 
    - Standaard Site Recovery blijven crash consistente herstel punten gedurende 24 uur. U kunt een aangepaste waarde tussen 0 en 72 uur instellen.
    - Er wordt om de vier uur een app-consistente moment opname gemaakt.
    - Standaard worden herstel punten gedurende 24 uur door Site Recovery opgeslagen.

7. Geef in **beschikbaarheids opties** op of de VM als zelfstandig, in een beschikbaarheids zone of in een beschikbaarheidsset wordt geïmplementeerd.

    :::image type="content" source="./media/tutorial-disaster-recovery/create-vm.png" alt-text="Schakel replicatie in op de pagina eigenschappen van VM-beheer.":::

8. De virtuele machine is gemaakt.

## <a name="enable-disaster-recovery-for-an-existing-vm"></a>Herstel na nood geval voor een bestaande virtuele machine inschakelen

Als u herstel na nood gevallen wilt inschakelen op een bestaande virtuele machine, gebruikt u deze procedure.

1. Open de pagina VM-eigenschappen in Azure Portal.
2. Selecteer in **Bewerkingen** de optie **Herstel na noodgeval**.

    :::image type="content" source="./media/tutorial-disaster-recovery/existing-vm.png" alt-text="Open opties voor nood herstel voor een bestaande virtuele machine.":::

3. Als de virtuele machine in een beschikbaarheids zone is geïmplementeerd, kunt u in **basis beginselen** het herstel na nood gevallen tussen beschikbaarheids zones selecteren.
4. Selecteer in **doel regio** de regio waarnaar u de virtuele machine wilt repliceren. De bron- en doelregio's moeten zich in dezelfde Azure Active Directory-tenant bevinden.

    :::image type="content" source="./media/tutorial-disaster-recovery/basics.png" alt-text="Stel de basis opties voor herstel na nood gevallen in voor een virtuele machine.":::

5. Selecteer **Volgende: Geavanceerde instellingen**.
6. In **Geavanceerde instellingen** kunt u instellingen bekijken en waarden wijzigen in aangepaste instellingen. Site Recovery komt standaard overeen met de broninstellingen om doelresources te maken.

    - **Doel abonnement**. Het abonnement waarin de doel-VM is gemaakt na een failover.
    - **Doel-VM-resource groep**. De resource groep waarin de doel-VM is gemaakt na een failover.
    - **Virtueel netwerk van doel**. Het virtuele Azure-netwerk waarin de doel-VM zich bevindt als deze wordt gemaakt na een failover.
    - **Beschik baarheid doel**. Wanneer de doel-VM wordt gemaakt als één instantie, in een beschikbaarheidsset of een beschikbaarheids zone.
    - **Proximity-plaatsing**. Selecteer, indien van toepassing, de plaatsings groep waarin de doel-VM zich bevindt na een failover.
    - **Opslaginstellingen - cache-opslagaccount**. Herstel maakt gebruik van een opslag account in de bron regio als tijdelijke gegevens opslag. Wijzigingen in de bron-VM worden in de cache van dit account opgeslagen voordat ze worden gerepliceerd naar de doellocatie.
        - Er wordt standaard één cache opslag account gemaakt per kluis en opnieuw gebruikt.
        - U kunt een ander opslag account selecteren als u het cache-account voor de virtuele machine wilt aanpassen.
    - **Opslag instellingen-door de replica beheerde schijf**. Standaard maakt Site Recovery replica beheerde schijven in de doel regio.
        -  De doel-beheerde schijf spiegelt standaard de door de virtuele machine beheerde schijven met hetzelfde opslag type (standaard HDD/SSD of Premium SSD).
        - U kunt het opslag type naar wens aanpassen.
    - **Replicatie-instellingen**. Toont de kluis waarin de virtuele machine zich bevindt en het replicatie beleid dat wordt gebruikt voor de virtuele machine. Standaard worden de herstel punten die zijn gemaakt door Site Recovery voor de virtuele machine 24 uur bewaard.
    - **Extensie-instellingen**. Geeft aan dat Site Recovery updates beheert voor de Site Recovery Mobility Service-extensie die is geïnstalleerd op VM's die u repliceert.
        - Het updateproces wordt beheerd door aangegeven Azure Automation-account.
        - U kunt het Automation-account aanpassen.

    :::image type="content" source="./media/tutorial-disaster-recovery/settings-summary.png" alt-text="Pagina met een samenvatting van de doel- en replicatie-instellingen.":::

6. Selecteer **evalueren en replicatie starten**.

7. Selecteer **Replicatie starten**. De implementatie wordt gestart en Site Recovery begint met het maken van doelresources. U kunt de replicatievoortgang bewaken via Meldingen.

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
