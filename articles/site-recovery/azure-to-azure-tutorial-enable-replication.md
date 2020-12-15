---
title: Zelfstudie om herstel na noodgevallen van Azure VM's in te stellen met Azure Site Recovery
description: In deze zelfstudie stelt u herstel na noodgevallen van Azure-VM's naar een andere Azure-regio in met de Site Recovery-service.
ms.topic: tutorial
ms.date: 11/03/2020
ms.custom: mvc
ms.openlocfilehash: 6d07082b4a9c18461d5cc74de8844be803da7168
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922490"
---
# <a name="tutorial-set-up-disaster-recovery-for-azure-vms"></a>Zelfstudie: Herstel na noodgevallen instellen voor Azure-VM's

Deze zelfstudie laat zien hoe u herstel na noodgevallen voor Azure-VM's kunt instellen met behulp van [Azure Site Recovery](site-recovery-overview.md). In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Azure-instellingen en -machtigingen verifiëren
> * VM's voorbereiden die u wilt repliceren
> * Een Recovery Services-kluis maken
> * VM-replicatie inschakelen

Wanneer u replicatie voor een VM inschakelt om herstel na noodgevallen in te stellen, wordt de Site Recovery Mobility-service-extensie geïnstalleerd op de VM en wordt deze geregistreerd bij Azure Site Recovery. Tijdens de replicatie worden schrijfbewerkingen van de VM-schijf verzonden naar een cache-opslagaccount in de bronregio. Er worden gegevens naar de doelregio verzonden en er worden herstelpunten gegenereerd op basis van de gegevens. Wanneer u een VM overbrengt tijdens noodherstel, wordt een herstelpunt gebruikt om de VM in de doelregio te herstellen.

> [!NOTE]
> Zelfstudies bieden instructies met de eenvoudigste standaardinstellingen. Bekijk [dit artikel](azure-to-azure-how-to-enable-replication.md) als u herstel na noodgevallen van Azure-VM's wilt instellen met aangepaste instellingen.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelfstudie begint:

- [Bekijk ondersteunde regio's](azure-to-azure-support-matrix.md#region-support). U kunt herstel na noodgevallen voor Azure-VM's instellen tussen twee regio's in dezelfde geografie.
- U hebt één of meer Azure-VM's nodig. Verifieer dat [Windows](azure-to-azure-support-matrix.md#windows)- of [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage)-VM's worden ondersteund.
- Bekijk de vereisten voor [compute](azure-to-azure-support-matrix.md#replicated-machines---compute-settings), [opslag](azure-to-azure-support-matrix.md#replicated-machines---storage) en [netwerken](azure-to-azure-support-matrix.md#replicated-machines---networking) voor de VM.
- In deze zelfstudie wordt ervan uitgegaan dat VM's niet zijn versleuteld. [Bekijk dit artikel](azure-to-azure-how-to-enable-replication-ade-vms.md) als u herstel na noodgevallen wilt instellen voor versleutelde VM's.

## <a name="check-azure-settings"></a>Azure-instellingen controleren

Controleer machtigingen, en instellingen in de doelregio.

### <a name="check-permissions"></a>Machtigingen controleren

Uw Azure-account heeft machtigingen nodig om een Recovery Services-kluis te maken en om VM's in de doelregio te maken.

- Als u een gratis Azure-abonnement hebt genomen, bent u de accountbeheerder en hoeft u verder niets te doen.
- Als u niet de beheerder bent, neemt u contact op met de beheerder om de machtigingen te krijgen die u nodig hebt.
    - **Een kluis maken**: Beheerders- of eigenaarsmachtigingen voor het abonnement. 
    - **Site Recovery-bewerkingen beheren in de kluis**: De ingebouwde Azure-rol *Site Recovery-inzender*.
    - **Azure-VM's in de doelregio maken**: De ingebouwde rol *Inzender voor virtuele machines* of specifieke machtigingen voor:
        - Het maken van een VM in het geselecteerde virtuele netwerk.
        - Schrijf naar een Azure Storage-account.
        - Schrijf naar een door Azure beheerde schijf.

### <a name="verify-target-settings"></a>Doelinstellingen verifiëren

Wanneer u tijdens herstel na noodgevallen een failover uitvoert vanuit de bronregio, worden VM's in de doelregio gemaakt. 

Controleer of uw abonnement voldoende resources in de doelregio heeft. U moet VM's kunnen maken met een grootte die overeenkomt met VM's in de bronregio. Wanneer u herstel na noodgevallen instelt, kiest Site Recovery voor de doel-VM dezelfde (of de dichtstbijzijnde) grootte.


## <a name="prepare-vms"></a>VM's voorbereiden

Zorg ervoor dat VM's uitgaande connectiviteit hebben en over de meest recente basiscertificaten beschikken. 


### <a name="set-up-vm-connectivity"></a>VM-connectiviteit instellen

VM's die u wilt repliceren, hebben uitgaande netwerkconnectiviteit nodig.

> [!NOTE]
> Site Recovery biedt geen ondersteuning voor het gebruiken van een verificatieproxy om netwerkconnectiviteit te beheren.

#### <a name="outbound-connectivity-for-urls"></a>Uitgaande connectiviteit voor URL's

Als u een URL-firewallproxy gebruikt om de uitgaande connectiviteit te beheren, staat u toegang tot deze URL’s toe:

| **Naam**                  | **Commercieel**                               | **Overheid**                                 | **Beschrijving** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Storage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | Hiermee kunnen gegevens van de VM naar het cache-opslagaccount in de bronregio worden geschreven. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Verzorgt autorisatie en authenticatie voor de URL’s van Site Recovery. |
| Replicatie               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Maakt het de VM mogelijk te communiceren met de Site Recovery-service. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Maakt het de VM mogelijk bewakings- en diagnosegegevens van Site Recovery te schrijven. |

#### <a name="outbound-connectivity-for-ip-address-ranges"></a>Uitgaande connectiviteit voor IP-adresbereiken

Als u netwerkbeveiligingsgroepen (NSG's) gebruikt om connectiviteit te beheren, maakt u op servicetags gebaseerde NSG-regels die uitgaande HTTPS-connectiviteit naar poort 443 toestaan voor deze [servicetags](../virtual-network/service-tags-overview.md#available-service-tags) (groepen IP-adressen):

**Tag** | **Toestaan** 
--- | ---
Opslagtag  |Hiermee kunnen gegevens van de VM naar het cache-opslagaccount worden geschreven.   
Azure AD-tag | Hiermee hebt u toegang tot alle IP-adressen die overeenkomen met Azure AD.   
EventsHub-tag | Hiermee wordt toegang tot Site Recovery-bewaking toegestaan.  
AzureSiteRecovery-tag | Hiermee hebt u toegang tot de Site Recovery-service in een willekeurige regio.   
GuestAndHybridManagement-tag | Gebruik deze optie als u de Site Recovery Mobility-agent automatisch wilt upgraden die wordt uitgevoerd op VM's die zijn ingeschakeld voor replicatie.

[Meer informatie](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) over vereiste tags en tagvoorbeelden.

### <a name="verify-vm-certificates"></a>VM-certificaten controleren

Controleer of de VM's over de meest recente basiscertificaten beschikken. Als dit niet het geval is, kunnen de VM's vanwege beveiligingsbeperkingen niet worden geregistreerd bij Site Recovery.

- **Virtuele Windows-machines**: Installeer de meest recente Windows-updates op de VM, zodat alle vertrouwde basiscertificaten op de machine aanwezig zijn. In een niet-verbonden omgeving volgt u uw standaardprocedures voor Windows Update en certificaatupdates.
- **Virtuele Linux-machines**: Volg de richtlijnen van de Linux-distributeur voor het verkrijgen van de meest recente basiscertificaten en de certificaatintrekkingslijst.

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Maak een Recovery Services-kluis in een willekeurige regio, behalve in de bronregio waaruit u VM's wilt repliceren.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Typ *herstel* in het zoekvak. Onder **Services** selecteert u **Recovery Services-kluizen**.

    ![Zoeken naar Recovery Services-kluizen](./media/azure-to-azure-tutorial-enable-replication/search.png)

3. Bij **Recovery Services-kluizen** selecteert u **Toevoegen**.
4. Bij **Een Recovery Services-kluis maken** > **Basisbeginselen** selecteert u het abonnement waarin u de kluis wilt maken.
5. Selecteer bij **Resourcegroep** een bestaande resourcegroep voor de kluis of maak een nieuwe.
6. Geef bij **Kluisnaam** een beschrijvende naam op om de kluis mee aan te duiden.
7. Selecteer bij **Regio** de Azure-regio waarin de kluis moet worden geplaatst. [Controleer ondersteunde regio's](https://azure.microsoft.com/pricing/details/site-recovery/).
8. Selecteer **Controleren + maken**.

   ![Kluisinstellingen op de pagina voor het maken van een nieuwe kluis](./media/azure-to-azure-tutorial-enable-replication/vault-basics.png)

9. Bij **Controleren en maken** selecteert u **Maken**.

10. De kluisimplementatie begint. Houd de voortgang bij in de meldingen.
11. Nadat de kluis is geïmplementeerd, selecteert u **Vastmaken aan dashboard** om deze op te slaan voor snelle naslag. Selecteer **Ga naar resource** om de nieuwe kluis te openen. 
    
    ![Knoppen voor het openen van de kluis na implementatie en het vastmaken aan het dashboard](./media/azure-to-azure-tutorial-enable-replication/vault-deploy.png)

### <a name="enable-site-recovery"></a>Site Recovery inschakelen

Selecteer **Site Recovery inschakelen** in de kluisinstellingen.

![Selectie om Site Recovery in te schakelen in de kluis](./media/azure-to-azure-tutorial-enable-replication/enable-site-recovery.png)

## <a name="enable-replication"></a>Replicatie inschakelen

Selecteer de broninstellingen en schakel VM-replicatie in. 

### <a name="select-source-settings"></a>Broninstellingen selecteren

1. Op de pagina **Site Recovery** in de kluis selecteert u onder **Virtuele Azure-machines** de optie **Replicatie inschakelen**.

    ![Selectie om replicatie in te schakelen voor Azure-VM's](./media/azure-to-azure-tutorial-enable-replication/enable-replication.png)

2. Bij **Bron**> **Bronlocatie** selecteert u de Azure-bronregio waarin VM's momenteel worden uitgevoerd.
3. Bij **Implementatiemodel voor virtuele Azure-machines** laat u de standaardinstelling **Resource Manager** staan.
4. Selecteer bij **Bronabonnement** het abonnement waarin VM's worden uitgevoerd. U kunt elk abonnement selecteren dat zich in dezelfde Azure Active Directory-tenant (AD) als de kluis bevindt.
5. Selecteer bij **Bronresourcegroep** de resourcegroep die de VM's bevat.
6. Bij **Herstel na noodgeval tussen beschikbaarheidszones** laat u de standaardinstelling **Nee** staan.

     ![Bron instellen](./media/azure-to-azure-tutorial-enable-replication/source.png)

7. Selecteer **Volgende**.

### <a name="select-the-vms"></a>De VM’s selecteren

In Site Recovery worden de VM's opgehaald die zijn gekoppeld aan het geselecteerde abonnement/de geselecteerde resourcegroep.

1. Selecteer bij **Virtuele machines** de VM's die u wilt inschakelen voor herstel na noodgevallen.

     ![Pagina om VM's voor replicatie te selecteren](./media/azure-to-azure-tutorial-enable-replication/select-vm.png)

2. Selecteer **Volgende**.

### <a name="review-replication-settings"></a>Replicatie-instellingen controleren

1. Controleer bij **Replicatie-instellingen** de instellingen. Site Recovery maakt standaardinstellingen/-beleidsregels voor de doelregio. In deze zelfstudie gebruiken we de standaardinstellingen.
2. Selecteer **Replicatie inschakelen**.

    ![Pagina om instellingen aan te passen en replicatie in te schakelen](./media/azure-to-azure-tutorial-enable-replication/enable-vm-replication.png)   

3. Houd de replicatievoortgang bij in de meldingen.

     ![Voorgang bijhouden in meldingen](./media/azure-to-azure-tutorial-enable-replication/notification.png) ![Melding voor Geslaagde replicatie bijhouden](./media/azure-to-azure-tutorial-enable-replication/notification-success.png)

4. De VM's die u inschakelt, worden weergegeven op de pagina **Gerepliceerde items** in de kluis.

    ![VM op de pagina Gerepliceerde items](./media/azure-to-azure-tutorial-enable-replication/replicated-items.png)


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u herstel na noodgevallen ingeschakeld voor een Azure-VM. Voer nu een analyse uit om te controleren of de failover werkt zoals verwacht.

> [!div class="nextstepaction"]
> [Noodherstelanalyse uitvoeren](azure-to-azure-tutorial-dr-drill.md)
