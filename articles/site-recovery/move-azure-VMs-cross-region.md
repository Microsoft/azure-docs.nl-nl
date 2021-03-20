---
title: Azure-VM’s verplaatsen naar een andere regio met behulp van Azure Site Recovery
description: Azure Site Recovery gebruiken om Azure IaaS-VM’s te verplaatsen van de ene Azure-regio naar een andere.
services: site-recovery
author: sideeksh
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: sideeksh
ms.custom: MVC
ms.openlocfilehash: 5ae930240872c00c8dbb45857e4e77d82766eadf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93398059"
---
# <a name="move-azure-vms-to-another-azure-region"></a>Azure-VM’s verplaatsen naar een andere Azure-regio

U wilt mogelijk virtuele Azure IaaS-machines (Infrastructure as a Service) naar een andere regio verplaatsen om betrouwbaarheid, beschikbaarheid of beheer te verbeteren. Deze zelfstudie laat zien hoe u VM’s naar een andere regio verplaatst met behulp van Azure Site Recovery. U leert het volgende:

> [!div class="checklist"]
> * Vereisten verifiëren
> * De bron-VM’s voorbereiden
> * De doelregio voorbereiden
> * Gegevens kopiëren naar de doelregio
> * De configuratie testen
> * De verplaatsing uitvoeren
> * De resources verwijderen uit de bronregio


> [!IMPORTANT]
> In dit artikel wordt beschreven hoe u Azure-VM’s *ongewijzigd* kunt verplaatsen naar een andere regio. Raadpleeg [Azure-VM’s verplaatsen naar beschikbaarheidszones](move-azure-vms-avset-azone.md) als u de beschikbaarheid van uw infrastructuur wilt verbeteren door VM’s te verplaatsen naar beschikbaarheidszones.

## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de Azure-VM’s zich in de Azure-regio bevinden *waaruit* u wilt verplaatsen.
- Controleer of de [combinatie bronregio-doelregio wordt ondersteund](./azure-to-azure-support-matrix.md#region-support) en kies nauwkeurig de doelregio.
- Zorg ervoor dat u inzicht hebt in de [architectuur en onderdelen voor dit scenario](azure-to-azure-architecture.md).
- Controleer de [beperkingen en vereisten voor ondersteuning](azure-to-azure-support-matrix.md).
- Controleer de accountmachtigingen. Als u net pas een gratis Azure-account hebt gemaakt, bent *u* de beheerder van uw abonnement. Als u niet de beheerder bent, neemt u contact op met de beheerder om de machtigingen te krijgen die u nodig hebt:
  -  Als u replicatie wilt inschakelen voor een VM en gegevens wilt kopiëren naar de doelserver met behulp van Site Recovery, moet u beschikken over machtigingen om een VM te maken in uw Azure-resources. De ingebouwde rol Inzender voor virtuele machines beschikt over deze machtigingen. Met deze machtigingen is het volgende mogelijk:
        - Het maken van een VM in de geselecteerde resourcegroep.
        - Het maken van een VM in het geselecteerde virtuele netwerk.
        - Schrijven naar het geselecteerde opslagaccount.

  - U hebt ook machtigingen nodig om Site Recovery-bewerkingen te beheren. De rol Site Recovery-inzender bevat alle machtigingen die nodig zijn om Azure Site Recovery-bewerkingen in een Recovery Services-kluis te beheren.

## <a name="prepare-the-source-vms"></a>De bron-VM’s voorbereiden

1. Controleer of op de Azure-VM's die u wilt verplaatsen, de meest recente basiscertificaten aanwezig zijn. Als dit niet het geval is, kunt u het kopiëren van gegevens naar de doelregio niet inschakelen vanwege beveiligingsbeperkingen.

    - Voor Windows-VM’s installeert u de meest recente Windows-updates, zodat alle vertrouwde basiscertificaten op de machine aanwezig zijn. In een niet-verbonden omgeving moet u de standaardprocedures van Windows Update en de standaardprocedures voor het bijwerken van de certificaten van uw organisatie volgen.
    - Voor Linux-VM’s volgt u de richtlijnen van de Linux-distributeur om de meest recente basiscertificaten en de certificaatintrekkingslijst op te halen.
2. Zorg ervoor dat u geen verificatieproxy gebruikt om de netwerkverbinding te beheren voor de VM’s die u gaat verplaatsen.
3. Controleer de [vereisten](azure-to-azure-tutorial-enable-replication.md#set-up-vm-connectivity) als een VM die u wilt verplaatsen, geen toegang tot internet heeft en een firewallproxy gebruikt voor het beheren van uitgaande toegang.
4. Leg ter controle de bronnetwerkindeling vast, en ook alle resources die u momenteel gebruikt, inclusief (maar niet beperkt tot) load balancers, netwerkbeveiligingsgroepen, en openbare IP-adressen.

## <a name="prepare-the-target-region"></a>De doelregio voorbereiden

1. Controleer in uw Azure-abonnement of u in staat bent om VM’s te maken in de doelregio die wordt gebruikt voor herstel na noodgevallen. Neem contact op met ondersteuning om, indien nodig, het vereiste quotum in te schakelen.

2. Zorg ervoor dat uw abonnement voldoende resources heeft die ondersteuning bieden voor uw bron-VM’s. Als u Site Recovery gebruikt om gegevens naar het doel te kopiëren, kiest Site Recovery voor de doel-VM’s dezelfde of de dichtstbijzijnde beschikbare grootte.

3. Zorg ervoor dat u een doelresource maakt voor elk onderdeel dat u hebt geïdentificeerd in de bronnetwerkindeling. Dit zorgt ervoor dat de VM’s in de doelregio beschikken over alle functionaliteit en functies, die ze hadden in de bronregio.

   Azure Site Recovery detecteert en maakt automatisch een virtueel netwerk- en opslagaccount wanneer u replicatie voor de bron-VM inschakelt. U kunt deze resources ook vooraf maken en ze toewijzen aan de VM als onderdeel van de stap voor het inschakelen van replicatie. U moet eventuele andere resources in de doelregio echter handmatig maken. Raadpleeg de volgende documenten om de meest gebruikte netwerkresources te maken, op basis van de bron-VM-configuratie:

   - [Netwerkbeveiligingsgroepen](../virtual-network/manage-network-security-group.md)
   - [Load balancers](../load-balancer/index.yml)
   - [Openbare IP](../virtual-network/virtual-network-public-ip-address.md)
    
   Raadpleeg de [Azure-netwerkdocumentatie](../index.yml?pivot=products&panel=network) voor eventuele andere netwerkonderdelen. 

4. [Maak handmatig een niet-productienetwerk](../virtual-network/quick-create-portal.md) in de doelregio als u de configuratie wilt testen voordat u de verplaatsing uitvoert. Bij het testen van de configuratie ontstaat slechts minimale verstoring in de productieomgeving. Dit wordt aanbevolen.
    
## <a name="copy-data-to-the-target-region"></a>Gegevens kopiëren naar de doelregio
In de volgende stappen wordt gebruikgemaakt van Azure Site Recovery om gegevens te kopiëren naar de doelregio.

### <a name="create-the-vault-in-any-region-except-the-source"></a>Maak de kluis in elke gewenste regio, met uitzondering van de bron

1. Meld u aan bij [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Selecteer **Een resource maken** > **Beheerhulpprogramma's** > **Backup en Site Recovery**.
3. Geef voor **Naam** de beschrijvende naam **ContosoVMVault** op. Als u meer dan één abonnement hebt, selecteert u het gewenste abonnement.
4. Maak een resourcegroep met de naam **ContosoRG**.
5. Geef een Azure-regio op. Zie [Prijsinformatie voor Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) om te controleren welke regio's worden ondersteund.
6. Selecteer voor Recovery Services-kluizen **Overzicht** > **ConsotoVMVault** >  **+Repliceren**.
7. Selecteer voor **Bron** de optie **Azure**.
8. Selecteer voor **Bronlocatie** de Azure-bronregio waar de VM’s momenteel worden uitgevoerd.
9. Selecteer het Azure Resource Manager-implementatiemodel. Selecteer vervolgens het **Bronabonnement** en de **Bronresourcegroep**.
10. Selecteer **OK** om de instellingen op te slaan.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Schakel replicatie voor Azure-VM's in en begin met het kopiëren van de gegevens

In Site Recovery wordt een lijst opgehaald met de VM’s die zijn gekoppeld aan het abonnement en de resourcegroep.

1. Selecteer de VM die u wilt verplaatsen. Selecteer vervolgens **OK**.
2. Selecteer voor **Instellingen** de optie **Herstel na noodgeval**.
3. Selecteer in **Noodherstel configureren** > **Doelregio** de doelregio waarnaar u wilt repliceren.
4. Kies ervoor om de standaarddoelresources te gebruiken of de resources die u vooraf hebt gemaakt.
5. Selecteer **Replicatie inschakelen** om de taak te starten.


 

## <a name="test-the-configuration"></a>De configuratie testen


1. Ga naar de kluis. Selecteer in **Instellingen** > **Gerepliceerde items** de virtuele machine die u wilt verplaatsen naar de doelregio. Selecteer vervolgens **Failover testen**.
2. Selecteer in **Failover testen** een herstelpunt om voor de failover te gebruiken:

   - **Laatst verwerkt**: voert een failover van de VM uit naar het laatste herstelpunt dat is verwerkt door de Site Recovery-service. Het tijdstempel wordt weergegeven. Er wordt geen tijd besteed aan het verwerken van gegevens, zodat bij deze optie sprake is van een lage RTO (Recovery Time Objective).
   - **Laatste toepassingsconsistente punt**: Er wordt een failover uitgevoerd van alle VM's naar het laatste app-consistente herstelpunt. Het tijdstempel wordt weergegeven.
   - **Aangepast**: selecteer een herstelpunt.

3. Selecteer het Azure-doelnetwerk waar u de Azure VM's naartoe wilt verplaatsen, om de configuratie te testen.

   > [!IMPORTANT]
   > U wordt aangeraden een afzonderlijk Azure-VM-netwerk te gebruiken voor de testfailover, en niet het productienetwerk in de doelregio.

4. Klik op **OK** om de verplaatsing te testen. Selecteer de VM om de bijbehorende **Eigenschappen** te openen als u de voortgang wilt volgen. Of selecteer de taak **Testfailover** in de kluis. Selecteer vervolgens **Instellingen** > **Taken** > **Site Recovery-taken**.
5. Nadat de failover is voltooid, wordt de replica-Azure-VM weergegeven in Azure Portal > **Virtuele machines**. Controleer of de VM draait, de juiste grootte heeft en aangesloten is op het juiste netwerk.
6. Als u de VM die u hebt gemaakt voor de test, wilt verwijderen, selecteert u **Testfailover opschonen** in het gerepliceerde item. Leg in **Notities** eventuele opmerkingen met betrekking tot de test vast, en sla ze op.

## <a name="perform-the-move-and-confirm"></a>De verplaatsing uitvoeren en bevestigen

1. Ga naar de kluis in **Instellingen** > **Gerepliceerde items**, selecteer de virtuele machine, en selecteer vervolgens **Failover**.
1. Selecteer voor **Failover** de optie **Meest recent**. 
2. Selecteer **Sluit de computer af voordat de failover wordt gestart**. Site Recovery probeert de bron-VM af te sluiten voordat de failover wordt geactiveerd. Maar de failover wordt voortgezet, ook als het afsluiten mislukt. U kunt de voortgang van de failover volgen op de pagina **Taken**.
3. Wanneer de taak is voltooid, controleert u of de VM wordt weergegeven in de Azure-doelregio, zoals verwacht.
4. Klik in **Gerepliceerde items** met de rechtermuisknop op de VM, en selecteer **Doorvoeren**. De verplaatsing is nu voltooid. Wacht tot de doorvoertaak is voltooid.

## <a name="discard-the-resources-from-the-source-region"></a>De resources verwijderen uit de bronregio

- Ga naar de VM en selecteer **Replicatie uitschakelen**. Hiermee wordt het proces van het kopiëren van de gegevens voor de VM beëindigd.

  > [!IMPORTANT]
  > Voltooi deze stap om te voorkomen dat na de verplaatsing kosten in rekening worden gebracht voor Site Recovery-replicatie.

Als u niet van plan bent om een of meer bronresources opnieuw te gebruiken, voert u de volgende stappen uit:

1. Verwijder alle relevante netwerkresources in de bronregio die u hebt vermeld in stap 4 in [De bron-VM’s voorbereiden](#prepare-the-source-vms).
2. Verwijder het bijbehorende opslagaccount in de bronregio.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een Azure-VM verplaatst naar een andere Azure-regio. U kunt nu noodherstel configureren voor deze VM’s.

> [!div class="nextstepaction"]
> [Herstel na noodgeval instellen na een migratie](azure-to-azure-quickstart.md)
