---
title: Virtuele Azure-machines met Azure Resource Mover tussen regio's verplaatsen
description: Leer hoe u virtuele Azure-machines naar een andere regio kunt verplaatsen met Azure Resource Mover
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5712448c8c5248d3c84ce43f8a41c669355f1d43
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565730"
---
# <a name="tutorial-move-azure-vms-across-regions"></a>Zelfstudie: Virtuele Azure-machines tussen regio's verplaatsen

In dit artikel leert u hoe u virtuele Azure-machines en gerelateerde netwerk-/opslagresources kunt verplaatsen naar een andere Azure-regio met behulp van [Azure Resource Mover](overview.md).
.


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De vereisten controleren.
> * De resources selecteren die u wilt verplaatsen.
> * Resourceafhankelijkheden oplossen.
> * De bronresourcegroep voorbereiden en verplaatsen. 
> * De overige resources voorbereiden en verplaatsen.
> * Beslissen of u de verplaatsing wilt doorvoeren of niet. 
> * Desgewenst resources in de bronregio verwijderen na de verplaatsing.

> [!NOTE]
> Zelfstudies laten de snelste manier zien om een scenario uit te proberen, en gebruiken de standaardopties. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint. Meld u vervolgens aan bij de [Azure-portal](https://portal.azure.com).

## <a name="prerequisites"></a>Vereisten
**Vereiste** | **Beschrijving**
--- | ---
**Ondersteuning voor resource-toedrijfing** | [Bekijk](common-questions.md) ondersteunde regio's en andere veelgestelde vragen.
**Abonnements machtigingen** | Controleer of u toegang hebt tot de *eigenaar* van het abonnement dat de resources bevat die u wilt verplaatsen<br/><br/> **Waarom heb ik de eigenaar toegang nodig?** De eerste keer dat u een resource toevoegt voor een specifiek bron- en doelpaar in een Azure-abonnement maakt Resource Mover een [door het systeem toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (vroeger MSI genoemd (Managed Service Identity)) die door het abonnement wordt vertrouwd. Om de identiteit te maken en deze de juiste rol toe te wijzen (Inzender of Administrator voor gebruikerstoegang in het bronabonnement), moet het account dat u gebruikt om resources toe te voegen *Eigenaars* machtigingen hebben voor het abonnement. [Meer informatie](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) over rollen in Azure.
**VM-ondersteuning** |  Controleer of de virtuele machines die u wilt verplaatsen, worden ondersteund.<br/><br/> - [Controleer](support-matrix-move-region-azure-vm.md#windows-vm-support) de ondersteunde Windows-vm's.<br/><br/> - [Controleer](support-matrix-move-region-azure-vm.md#linux-vm-support) ondersteunde vm's en kernel-versies van Linux.<br/><br/> -Ondersteunde instellingen voor [Compute](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [opslag](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)en [netwerk](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) controle.
**Doel abonnement** | Het abonnement in de doel regio moet voldoende quota hebben om de resources te maken die u verplaatst in de doel regio. Als de quota onvoldoende zijn, moet u [hogere limieten aanvragen](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Kosten van de doel regio** | Verifieer prijzen en kosten voor de doelregio waarnaar u virtuele machines verplaatst. Gebruik de [prijscalculator](https://azure.microsoft.com/pricing/calculator/) om u daarbij te helpen.
    

## <a name="prepare-vms"></a>VM's voorbereiden

1. Nadat u hebt gecontroleerd of de Vm's voldoen aan de vereisten, moet u ervoor zorgen dat de Vm's die u wilt verplaatsen, zijn ingeschakeld. Alle virtuele machines die u in de doel regio wilt beschik bare schijven, moeten zijn gekoppeld en geïnitialiseerd in de virtuele machine.
1. Zorg ervoor dat VM's de meest recente vertrouwde basiscertificaten en een bijgewerkte certificaatintrekkingslijst hebben. Om dit te doen:
    - Installeert u de meest recente Windows-updates op virtuele Windows-machines.
    - Volgt u de richtlijnen van de distributeurs op virtuele Linux-machines zodat computers over de nieuwste certificaten en certificaatintrekkingslijst beschikken. 
1. Sta uitgaande connectiviteit vanaf VM's toe:
    - Als u een URL-firewallproxy gebruikt om de uitgaande connectiviteit te beheren, staat u toegang tot deze [URL's](support-matrix-move-region-azure-vm.md#url-access) toe
    - Als u regels voor netwerk beveiligingsgroepen (NSG) gebruikt om de uitgaande connectiviteit te beheren, maakt u deze [servicetagregels](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources"></a>Resources selecteren 

Selecteer de resources die u wilt verplaatsen.

- Alle ondersteunde resourcetypen in resourcegroepen binnen de geselecteerde bronregio worden weergegeven.
- Resources die al zijn toegevoegd voor het verplaatsen tussen regio's worden niet weergegeven.
- U verplaatst resources naar een doelregio in hetzelfde abonnement als de bronregio. Als u het abonnement wilt wijzigen, kunt u dat doen nadat de resources zijn verplaatst.

1. Zoek in de Azure-portal naar *resource mover*. Selecteer vervolgens **Azure Resource Mover** onder **Services**.

    ![Zoekresultaten voor resource mover in de Azure-portal](./media/tutorial-move-region-virtual-machines/search.png)

2. Klik in **Overzicht** op **Aan de slag**.

    ![Knop voor het toevoegen van naar een andere regio te verplaatsen resources](./media/tutorial-move-region-virtual-machines/get-started.png)

3. Selecteer in **Resources verplaatsen** > **Bron en doel**, het bronabonnement en de regio.
4. Selecteer in **Doel** de regio waarnaar u de virtuele machines wilt verplaatsen. Klik op **Volgende**.

    ![Pagina voor het selecteren van bron- en doelregio](./media/tutorial-move-region-virtual-machines/source-target.png)

6. Klik in **Resources die moeten worden verplaatst** op **Resources selecteren**.
7. Selecteer de virtuele machine in **Resources selecteren**. U kunt alleen resources selecteren [waarvoor verplaatsing wordt ondersteund](#prepare-vms). Klik vervolgens op **Gereed**.

    ![Pagina voor het selecteren van virtuele machines die moeten worden verplaatst](./media/tutorial-move-region-virtual-machines/select-vm.png)

8.  Klik in **Resources die moeten worden verplaatst** op **Volgende**.
9. Controleer bij **controle** de bron-en doel instellingen. 

    ![Pagina voor het controleren van instellingen en doorgaan met de verplaatsing](./media/tutorial-move-region-virtual-machines/review.png)
10. Klik op **Doorgaan** om te beginnen met toevoegen van de resources.
11. Klik nadat het toevoegen geslaagd is op **Resources toevoegen** in het meldingspictogram.
12. Controleer nadat u op de melding hebt geklikt de resources op de pagina **Tussen regio's**.

> [!NOTE]
> - Toegevoegde resources hebben de status *Voorbereiding in behandeling*.
> - De resource groep voor de virtuele machines wordt automatisch toegevoegd.
> - Als u een resource uit een te verplaatsen verzameling wilt verwijderen, hangt de manier waarop u dat doet af van de plaats waar u zich in het verplaatsingsproces bevindt. [Meer informatie](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Afhankelijkheden oplossen

1. Als bij resources het bericht *Afhankelijkheden controleren* staat in de kolom **Problemen**, klikt u op de knop **Afhankelijkheden controleren**. Het validatieproces wordt gestart.
2. Als er afhankelijkheden worden gevonden, klikt u op **Afhankelijkheden toevoegen**. 
3. Laat bij **afhankelijkheden toevoegen** de optie standaard **alle afhankelijkheden weer geven** staan.

    - Alle afhankelijkheden weer geven, worden alle directe en indirecte afhankelijkheden voor een resource herhaald. Voor een VM worden bijvoorbeeld de NIC, het virtuele netwerk, netwerk beveiligings groepen (Nsg's), enzovoort weer gegeven.
    - Afhankelijkheden van het eerste niveau weer geven alleen directe afhankelijkheden worden weer gegeven. Bijvoorbeeld: voor een virtuele machine wordt de NIC weer gegeven, maar niet het virtuele netwerk.


4. Selecteer de afhankelijke resources die u wilt toevoegen > **afhankelijkheden toevoegen**. Controleer de voortgang in de meldingen.

    ![Afhankelijkheden toevoegen](./media/tutorial-move-region-virtual-machines/add-dependencies.png)

4. Valideer afhankelijkheden opnieuw. 
    ![Pagina om aanvullende afhankelijkheden toe te voegen](./media/tutorial-move-region-virtual-machines/add-additional-dependencies.png)



## <a name="move-the-source-resource-group"></a>De bronresourcegroep verplaatsen 

Voordat u virtuele machines kunt voorbereiden en verplaatsen, moet de resourcegroep van de virtuele machine aanwezig zijn in de doelregio. 

### <a name="prepare-to-move-the-source-resource-group"></a>De bronresourcegroep voorbereiden voor verplaatsing

Tijdens het voorbereidingsproces worden met Resource Mover ARM-sjablonen (Azure Resource Manager) gegenereerd met behulp van de instellingen voor de resourcegroep. Resources in de resourcegroep worden niet beïnvloed.

Bereid als volgt voor:

1. Selecteer in **Tussen regio's** de bronresourcegroep > **Voorbereiden**.
2. Klik in **Resources voorbereiden** op **Voorbereiden**.

    ![Resourcegroep voorbereiden](./media/tutorial-move-region-virtual-machines/prepare-resource-group.png)

> [!NOTE]
> Nadat de resourcegroep is voorbereid, heeft deze niet de status *Initiëren verplaatsing in behandeling*. 

 
### <a name="move-the-source-resource-group"></a>De bronresourcegroep verplaatsen

Initieer de verplaatsing als volgt:

1. Selecteer in **Tussen regio's** de resourcegroep > **Verplaatsing initiëren**
2. Klik in **Resources verplaatsen** op **Verplaatsing initiëren**. De resourcegroep wordt verplaatst naar een status *Initiëren verplaatsing wordt uitgevoerd*.
3. Nadat de verplaatsing is geïnitieerd, wordt de doelresourcegroep gemaakt op basis van de gegenereerde ARM-sjabloon. De bronresourcegroep wordt verplaatst naar een status *Verplaatsing doorvoeren in behandeling*.

    ![Klik op de knop Verplaatsing initiëren](./media/tutorial-move-region-virtual-machines/commit-move-pending.png)

Ga als volgt te werk om het verplaatsingsproces door te voeren en te voltooien:

1. Selecteer in **Tussen regio's** de resourcegroep > **Verplaatsing doorvoeren**.
2. Klik in **Resources verplaatsen** op **Doorvoeren**.

> [!NOTE]
> Nadat de verplaatsing is doorgevoerd, bevindt de bronresourcegroep zich in de status *Verwijderen bron in behandeling*.

## <a name="prepare-resources-to-move"></a>Te verplaatsen resources voorbereiden

Nu de bron resource groep is verplaatst, kunt u voor bereidingen treffen om andere resources te verplaatsen die in behandeling zijn voor *bereid* .

1. Controleer in **meerdere regio's** of de resources nu in behandeling zijn en zonder problemen worden *vooropgesteld* . Als dat niet het geval is, moet u opnieuw valideren en eventuele openstaande problemen oplossen.

    ![Pagina met resources met de status Voorbereiding in behandeling](./media/tutorial-move-region-virtual-machines/prepare-pending.png)

2. Als u de doelinstellingen wilt bewerken voordat u begint met verplaatsen, selecteert u de koppeling in de kolom **Doelconfiguratie** voor de resource en bewerkt u de instellingen. Als u de instellingen van de doel-VM bewerkt, mag de grootte van de doel-VM niet kleiner zijn dan de grootte van de bron-VM.  

Nadat de bronresourcegroep is verplaatst, kunt u het verplaatsen van de andere resources voorbereiden.

3. Selecteer de resources die u wilt voorbereiden. 

    ![Pagina voor het selecteren van andere resources om voor te bereiden](./media/tutorial-move-region-virtual-machines/prepare-other.png)

2. Selecteer **Voorbereiden**. 

> [!NOTE]
> - Tijdens het voorbereidingsproces wordt de Azure Site Recovery Mobility-agent op VM's geïnstalleerd om ze te repliceren.
> - VM-gegevens worden periodiek naar de doelregio gerepliceerd. Dit heeft geen invloed op de bron-VM.
> - Met Resource Mover worden ARM-sjablonen voor de andere bronresources gegenereerd.
> - Nadat de resources zijn voorbereid, bevinden ze zich in de status *Initiëren verplaatsing in behandeling*.

![Pagina met resources met de status Initiëren verplaatsing in behandeling](./media/tutorial-move-region-virtual-machines/initiate-move-pending.png)


## <a name="initiate-the-move"></a>De verplaatsing initiëren

Nu de resources zijn voorbereid, kunt u de verplaatsing initiëren. 

1. Selecteer in **Tussen regio's** resources met de status *Initiëren verplaatsing in behandeling*. Klik op **Verplaatsing initiëren**.
2. Klik in **Resources verplaatsen** op **Verplaatsing initiëren**.

    ![Klik op de knop Verplaatsen initiëren](./media/tutorial-move-region-virtual-machines/initiate-move.png)

3. Controleer de voortgang van de verplaatsing in de meldingenbalk.

> [!NOTE]
> - Voor VM's worden replica-VM's gemaakt in de doelregio. De bron-VM wordt uitgeschakeld en er treedt enige downtime op (meestal een aantal minuten).
> - Met Resource Mover worden andere resources opnieuw gemaakt met behulp van de ARM-sjablonen die zijn voorbereid. Meestal treedt er geen downtime op.
> - Nadat de resources zijn verplaatst, bevinden ze zich in de status *Verplaatsing doorvoeren in behandeling*.

![Pagina met resources met de status *Verwijderen bron in behandeling*](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


## <a name="discard-or-commit"></a>Verwijderen of doorvoeren?

Na de eerste verplaatsing kunt u beslissen of u de verplaatsing wilt doorvoeren of verwijderen. 

- **Verwijderen**: Mogelijk wilt u een verplaatsing verwijderen als u een test uitvoert en de bronresource niet echt wilt verplaatsen. Als u de verplaatsing negeert, wordt de resource teruggezet in de status *Initiëren verplaatsing in behandeling*.
- **Doorvoeren**: Met doorvoeren wordt de verplaatsing naar de doelregio voltooid. Na het doorvoeren heeft een bronresource de status *Verwijderen bron in behandeling* en kunt u besluiten of u deze wilt verwijderen.


## <a name="discard-the-move"></a>De verplaatsing verwijderen 

U kunt de verplaatsing als volgt verwijderen:

1. Selecteer in **Tussen regio's** resources met de status *Verplaatsing doorvoeren in behandeling* en klik op **Verplaatsing verwijderen**.
2. Klik in **Verplaatsing verwijderen** op **Verwijderen**.
3. Controleer de voortgang van de verplaatsing in de meldingenbalk.


> [!NOTE]
> Nadat de resources zijn verwijderd, bevinden de VM's zich in de status *Initiëren verplaatsing in behandeling*.

## <a name="commit-the-move"></a>De verplaatsing doorvoeren

Als u het verplaatsingsproces wilt voltooien, moet u de verplaatsing doorvoeren. 

1. Selecteer in **Tussen regio's** resources met de status *Verplaatsing doorvoeren in behandeling* en klik op **Verplaatsing doorvoeren**.
2. Klik in **Resources doorvoeren** op **Doorvoeren**.

    ![Pagina voor het doorvoeren van resources om de verplaatsing te voltooien](./media/tutorial-move-region-virtual-machines/commit-resources.png)

3. Controleer de voortgang van het doorvoeren in de meldingenbalk.

> [!NOTE]
> - Na het doorvoeren van de verplaatsing, worden VM's niet meer gerepliceerd. De bron-VM wordt door de doorvoer niet beïnvloed.
> - Doorvoeren heeft geen invloed op bronnetwerkresources.
> - Nadat de verplaatsing is doorgevoerd, bevinden de resources zich in de status *Verwijderen bron in behandeling*.

![Pagina met resources met de status *Verwijderen bron in behandeling*](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


## <a name="configure-settings-after-the-move"></a>Instellingen configureren na de verplaatsing

- De Mobility-service wordt niet automatisch van VM's verwijderd. Verwijder de service handmatig of laat deze staan als u van plan bent de server opnieuw te verplaatsen.
- Wijzig de regels voor op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) na de verplaatsing.


## <a name="delete-source-resources-after-commit"></a>Bronresources verwijderen na doorvoeren

Na de verplaatsing kunt u desgewenst de resources in de bronregio verwijderen. 

> [!NOTE]
> Enkele resources, bijvoorbeeld sleutel kluizen en SQL Server servers, kunnen niet worden verwijderd uit de portal en moeten worden verwijderd van de eigenschappen pagina van de resource.

1. In **meerdere regio's** klikt u op de naam van de bron resource die u wilt verwijderen.
2. Selecteer **bron verwijderen**.

## <a name="delete-additional-resources-created-for-move"></a>Aanvullende resources verwijderen die zijn gemaakt om te worden verplaatst

Na de verplaatsing kunt u handmatig de verzameling voor verplaatsen en de Site Recovery-resources die zijn gemaakt, verwijderen.

- De verzameling voor verplaatsen wordt standaard verborgen. Als u deze wilt zien, moet u verborgen resources inschakelen.
- De cacheopslag heeft een vergrendeling die moet worden verwijderd voordat de cacheopslag kan worden verwijderd.

Verwijder deze als volgt: 
1. Zoek de resources in resourcegroep ```RegionMoveRG-<sourceregion>-<target-region>```.
2. Controleer of alle virtuele machines en andere bronresources in de bronregio zijn verplaatst of verwijderd. Zo weet u zeker dat er geen resources in behandeling zijn die hiervan gebruikmaken.
2. De resources verwijderen:

    - De naam van de verzameling voor verplaatsen is ```movecollection-<sourceregion>-<target-region>```.
    - De naam van het cacheopslagaccount is ```resmovecache<guid>```
    - De naam van de kluis is ```ResourceMove-<sourceregion>-<target-region>-GUID```.
## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Virtuele Azure-machines naar een andere Azure-regio verplaatst.
> * De resources die zijn gekoppeld aan VM's naar een andere regio verplaatst.

We gaan nu proberen de Azure SQL-databases en elastische pools naar een andere regio te verplaatsen.

> [!div class="nextstepaction"]
> [Azure SQL-resources verplaatsen](./tutorial-move-region-sql.md)
