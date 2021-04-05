---
title: Azure-VM's verplaatsen naar een andere Azure-regio met behulp van Azure Site Recovery
description: Azure Site Recovery gebruiken om Azure-VM's te verplaatsen van de ene Azure-regio naar de andere.
services: site-recovery
author: Sharmistha-Rai
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: sharrai
ms.custom: MVC
ms.openlocfilehash: 076adbfd4cecf7dae9ffc490e911fcb7ffce48e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93394829"
---
# <a name="move-vms-to-another-azure-region"></a>VM's verplaatsen naar een andere Azure-regio

Er zijn scenario's waarin u uw bestaande Azure IaaS-VM's (virtuele machines) wilt verplaatsen van de ene regio naar de andere. Bijvoorbeeld wanneer u de betrouwbaarheid en beschikbaarheid van uw bestaande VM’s wilt verbeteren, de beheersbaarheid wilt verbeteren, of de VM’s om beheerredenen wilt verplaatsen. Zie [Overzicht van het verplaatsen van Azure-VM’s](azure-to-azure-move-overview.md) voor meer informatie. 

U kunt de service [Azure Site Recovery](site-recovery-overview.md) gebruiken om Azure-VM's naar een secundaire regio te verplaatsen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> 
> * De vereisten voor de verplaatsing verifiëren
> * De bron-VM's en de doelregio voorbereiden
> * De gegevens kopiëren en replicatie inschakelen
> * De configuratie testen en de verplaatsing uitvoeren
> * De resources verwijderen in de doelregio


> [!IMPORTANT]
> Momenteel is het aan te raden [Azure Resource Mover](../resource-mover/tutorial-move-region-virtual-machines.md) te gebruiken om Azure-VM's te verplaatsen naar een andere regio. Azure Resource Mover is momenteel beschikbaar in openbare preview en biedt het volgende:
> - Eén hub voor het verplaatsen van resources binnen regio's.
> - Beperkte verplaatsingstijd en complexiteit. Alles wat u nodig hebt, bevindt zich op één locatie.
> - Een eenvoudige en consistente ervaring voor het verplaatsen van verschillende typen Azure-resources.
> - Een eenvoudige manier om afhankelijkheden te identificeren binnen resources die u wilt verplaatsen. Dit helpt om gerelateerde resources samen te verplaatsen zodat alles na de verplaatsing naar verwachting werkt in de doelregio.
> - Automatische opschoning van resources in de bronregio, als u deze na de verplaatsing wilt verwijderen.
> - Testen. U kunt een verplaatsing uitproberen en deze vervolgens verwijderen als u geen volledige verplaatsing wilt uitvoeren.



> [!NOTE]
> Deze zelfstudie laat zien hoe u Azure-VM’s ongewijzigd kunt verplaatsen naar een andere regio. Raadpleeg de [zelfstudie Azure-VM’s verplaatsen naar beschikbaarheidszones](move-azure-vms-avset-azone.md) als u de beschikbaarheid wilt verbeteren door VM’s die zich in een beschikbaarheidsset bevinden, te verplaatsen naar VM’s die zijn vastgemaakt aan een zone in een andere regio.

## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de Azure-VM’s zich in de Azure-regio bevinden waaruit u wilt verplaatsen.
- Controleer of de [combinatie bronregio-doelregio wordt ondersteund](./azure-to-azure-support-matrix.md#region-support) en neem een weloverwogen beslissing over de doelregio.
- Zorg ervoor dat u inzicht hebt in de [architectuur en onderdelen voor dit scenario](azure-to-azure-architecture.md).
- Controleer de [beperkingen en vereisten voor ondersteuning](azure-to-azure-support-matrix.md).
- Controleer de accountmachtigingen. Als u een gratis Azure-account hebt gemaakt, bent u de beheerder van uw abonnement. Als u niet de abonnementsbeheerder bent, vraagt u de beheerder om u de machtigingen toe te wijzen die u nodig hebt. Om replicatie in te schakelen voor een VM en uiteindelijk gegevens te kopiëren met behulp van Azure Site Recovery, moet u beschikken over het volgende:

    - Machtigingen voor het maken van een virtuele machine in Azure-resources. De ingebouwde rol Inzender voor virtuele machines beschikt over deze machtigingen, waaronder:
    - Machtiging voor het maken van een virtuele machine in de geselecteerde resourcegroep
    - Machtiging voor het maken van een virtuele machine in het geselecteerde virtuele netwerk
    - Machtigingen om naar het geselecteerde opslagaccount te schrijven
    
    - Machtigingen om Azure Site Recovery-bewerkingen te beheren. De rol Site Recovery-inzender bevat alle machtigingen die nodig zijn om Site Recovery-bewerkingen in een Recovery Services-kluis te beheren.

- Zorg ervoor dat alle meest recente basiscertificaten aanwezig zijn op de Azure-VM’s die u wilt verplaatsen. Als de meest recente basiscertificaten niet aanwezig zijn op de VM, kan het kopiëren van gegevens naar de doelregio niet worden ingeschakeld vanwege veiligheidsbeperkingen.

- Voor Windows-VM’s moet u de meest recente Windows-updates op de VM installeren, zodat alle vertrouwde basiscertificaten op de machine aanwezig zijn. In een niet-verbonden omgeving moet u de standaardprocedures van Windows Update en de standaardprocedures voor het bijwerken van de certificaten van uw organisatie volgen.
    
- Voor Linux-VM’s volgt u de richtlijnen van de Linux-distributeur voor het verkrijgen van de meest recente basiscertificaten en de certificaatintrekkingslijst op de VM.
- Zorg ervoor dat u geen verificatieproxy gebruikt om de netwerkverbinding te beheren voor de VM’s die u wilt verplaatsen.

- [Controleer de vereisten](azure-to-azure-tutorial-enable-replication.md#set-up-vm-connectivity) als de VM die u wilt verplaatsen, geen toegang tot internet heeft, of een firewallproxy gebruikt voor het beheren van uitgaande toegang.

- Identificeer de bronnetwerkindeling en alle resources die u momenteel gebruikt. Dit omvat, maar is niet beperkt tot load balancers, NSG’s (netwerkbeveiligingsgroepen), en openbare IP-adressen.

- Controleer of u een Azure-abonnement hebt waarmee u in staat bent om VM’s te maken in de doelregio die wordt gebruikt voor herstel na noodgevallen. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.

- Zorg ervoor dat uw abonnement voldoende resources heeft die ondersteuning kunnen bieden voor VM’s met grootten die overeenkomen met uw bron-VM’s. Als u Site Recovery gebruikt om gegevens naar het doel te kopiëren, kiest Site Recovery voor de doel-VM dezelfde of de dichtstbijzijnde grootte.

- Zorg ervoor dat u een doelresource maakt voor elk onderdeel dat is geïdentificeerd in de bronnetwerkindeling. Deze stap is belangrijk om ervoor te zorgen dat de VM’s in de doelregio beschikken over alle functionaliteit en functies, die u had in de bronregio.

     > [!NOTE] 
     > Azure Site Recovery detecteert en maakt automatisch een virtueel netwerk wanneer u replicatie inschakelt voor de bron-VM. U kunt ook vooraf een netwerk maken en dit toewijzen aan de VM in de gebruikersstroom, om replicatie in te schakelen. Zoals verderop wordt beschreven moet u eventuele andere resources in de doelregio handmatig maken.

    Raadpleeg de volgende documentatie om de meest gebruikte netwerkresources te maken die relevant zijn voor u, op basis van de bron-VM-configuratie:
    - [Netwerkbeveiligingsgroepen](../virtual-network/manage-network-security-group.md)
    - [Load balancers](../load-balancer/index.yml)
    -  [Openbare IP](../virtual-network/virtual-network-public-ip-address.md)
    - Raadpleeg de [netwerkdocumentatie](../index.yml?pivot=products&panel=network) voor eventuele andere netwerkonderdelen.



## <a name="prepare"></a>Voorbereiden
De volgende stappen laten zien hoe u de virtuele machine voorbereidt voor het verplaatsen, met behulp van Azure Site Recovery als oplossing. 

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Maak de kluis in elke gewenste regio, met uitzondering van de bronregio

1. Meld u aan bij [Azure Portal](https://portal.azure.com)
1. Typ Recovery Services in het zoekvak > klik op Recovery Services-kluizen
1. Klik in het menu Recovery Services-kluizen op +Toevoegen.
1. Bij **Naam** geeft u de beschrijvende naam **ContosoVMVault** op. Als u meer dan één abonnement hebt, selecteert u het gewenste abonnement.
1. Maak de resourcegroep met de naam **ContosoRG**.
1. Geef een Azure-regio op. Bekijk de geografische beschikbaarheid in [Prijsinformatie voor Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) om te controleren welke regio's worden ondersteund.
1. Selecteer in **Recovery Services-kluizen** **ContosoVMVault** > **Gerepliceerde items** >  **+Repliceren**.
1. Selecteer **Azure Virtual Machines** in de vervolgkeuzelijst.
1. Bij **Bronlocatie** selecteert u de Azure-bronregio waar uw VM’s momenteel worden uitgevoerd.
1. Selecteer het Resource Manager-implementatiemodel. Selecteer vervolgens het **Bronabonnement** en de **Bronresourcegroep**.
1. Selecteer **OK** om de instellingen op te slaan.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Schakel replicatie voor Azure-VM's in en begin met het kopiëren van de gegevens

In Site Recovery wordt een lijst opgehaald met de VM’s die zijn gekoppeld aan het abonnement en de resourcegroep.

1. Selecteer in de volgende stap de VM die u wilt verplaatsen. Selecteer vervolgens **OK**.
1. Selecteer bij **Instellingen** de optie **Herstel na noodgeval**.
1. Selecteer in **Noodherstel configureren** > **Doelregio** de doelregio waarnaar u wilt repliceren.
1. Accepteer voor deze zelfstudie de overige standaardinstellingen.
1. Selecteer **Replicatie inschakelen**. Met deze stap wordt een taak gestart voor het inschakelen van replicatie voor de VM.



## <a name="move"></a>Verplaatsen

De volgende stappen laten zien hoe u de verplaatsing naar de doelregio uitvoert.

1. Ga naar de kluis. Selecteer in **Instellingen** > **Gerepliceerde items** de VM, en selecteer vervolgens **Failover**.
2. Bij **Failover** selecteert u **Meest recente**.
3. Selecteer **Sluit de computer af voordat de failover wordt gestart**. Site Recovery sluit de virtuele bronmachine af voordat de failover wordt geactiveerd. De failover wordt voortgezet zelfs als het afsluiten is mislukt. U kunt de voortgang van de failover volgen op de pagina **Taken**.
4. Nadat de taak is voltooid, controleert u of de VM zoals verwacht wordt weergegeven in de Azure-doelregio.


## <a name="discard"></a>Verwijderen 

Als u de verplaatste VM hebt gecontroleerd en wijzigingen wilt aanbrengen in het failover-punt, of wilt teruggaan naar een eerder punt, selecteert u met de rechtermuisknop in **Gerepliceerde items** de VM > **Herstelpunt wijzigen**. Deze stap biedt u de mogelijkheid om een ander herstelpunt te kiezen en de failover naar dit punt uit te voeren. 


## <a name="commit"></a>Doorvoeren 

Nadat u de verplaatste VM hebt gecontroleerd en klaar bent om de wijziging door te voeren, klikt u met de rechtermuisknop in **Gerepliceerde items** op de VM > **Doorvoeren**. Met deze stap wordt de verplaatsing naar de doelregio voltooid. Wacht tot de doorvoertaak is voltooid.

## <a name="clean-up"></a>Opruimen

De volgende stappen begeleiden u bij het opschonen van de bronregio en gerelateerde resources die zijn gebruikt voor de verplaatsing.

Voor alle resources die zijn gebruikt voor de verplaatsing:

- Ga naar de VM. Selecteer **Replicatie uitschakelen**. Met deze stap wordt het kopieerproces van de gegevens voor de VM beëindigd.

   > [!IMPORTANT]
   > Het is belangrijk dat u deze stap uitvoert om te voorkomen dat kosten in rekening worden gebracht voor Azure Site Recovery-replicatie.

Als u niet van plan bent om een of meer bronresources opnieuw te gebruiken, voert u deze aanvullende stappen uit:

1. Verwijder alle relevante netwerkresources in de bronregio die u hebt geïdentificeerd in [vereisten](#prerequisites).
1. Verwijder het bijbehorende opslagaccount in de bronregio.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Azure-VM verplaatst naar een andere Azure-regio. U kunt nu noodherstel configureren voor de VM die u hebt verplaatst.

> [!div class="nextstepaction"]
> [Herstel na noodgeval instellen na een migratie](azure-to-azure-quickstart.md)
