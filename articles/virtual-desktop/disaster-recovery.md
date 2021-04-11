---
title: Herstel plan voor Windows virtueel bureau blad instellen-Azure
description: Een plan voor bedrijfs continuïteit en herstel na nood gevallen instellen voor uw Windows-implementatie voor virtueel bureau blad.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 18089bc00e9d02087acb149511fbc2c55077c153
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446924"
---
# <a name="set-up-a-business-continuity-and-disaster-recovery-plan"></a>Een plan voor bedrijfs continuïteit en herstel na nood gevallen instellen

Als u de gegevens van uw organisatie wilt beveiligen, moet u mogelijk een strategie voor bedrijfs continuïteit en nood herstel (BCDR) aannemen. Met een strategie voor geluids BCDR blijven uw apps en werk belastingen actief tijdens geplande en ongeplande Services of Azure-uitval.

Windows virtueel bureau blad biedt BCDR voor de virtuele bureau blad-service van Windows om meta gegevens van klanten te behouden tijdens storingen. Wanneer er een storing optreedt in een regio, worden de onderdelen van de service-infra structuur overgezet naar de secundaire locatie en blijven ze normaal functioneren. U kunt nog steeds toegang krijgen tot de service-gerelateerde meta gegevens en gebruikers kunnen nog steeds verbinding maken met beschik bare hosts. Verbindingen voor eind gebruikers blijven online, zolang de Tenant omgeving of-hosts toegankelijk blijven.

Om ervoor te zorgen dat gebruikers nog steeds verbinding kunnen maken tijdens een onderbreking van de regio, moet u de virtuele machines (Vm's) op een andere locatie repliceren. Tijdens de uitval wordt er een failover uitgevoerd voor de primaire site naar de gerepliceerde Vm's op de secundaire locatie. Gebruikers kunnen zonder onderbreking toegang blijven houden tot apps vanaf de secundaire locatie. Op de VM-replicatie moet u de gebruikers identiteiten toegankelijk maken op de secundaire locatie. Als u profiel containers gebruikt, moet u deze ook repliceren. Ten slotte moet u ervoor zorgen dat uw zakelijke apps die afhankelijk zijn van gegevens op de primaire locatie, een failover kunnen uitvoeren met de rest van de gegevens.

Als u wilt samenvatten om uw gebruikers tijdens een storing te laten verbonden blijven, moet u de volgende dingen doen in deze volg orde:

- Repliceer de Vm's op een secundaire locatie.
- Als u profiel containers gebruikt, stelt u de gegevens replicatie in op de secundaire locatie.
- Zorg ervoor dat de gebruikers identiteiten die u op de primaire locatie hebt ingesteld, beschikbaar zijn op de secundaire locatie.
- Zorg ervoor dat voor alle line-of-business-toepassingen die gebruikmaken van gegevens op uw primaire locatie, een failover naar de secundaire locatie wordt uitgevoerd.

## <a name="vm-replication"></a>VM-replicatie

Eerst moet u de virtuele machines naar de secundaire locatie repliceren. De opties hiervoor zijn afhankelijk van de configuratie van de virtuele machines:

- U kunt al uw virtuele machines configureren voor zowel gegroepeerde als persoonlijke hostgroepen met Azure Site Recovery. Met deze methode hoeft u slechts één hostgroep en de bijbehorende toepassings groepen en werk ruimten in te stellen.
- U kunt een nieuwe hostgroep in de failover-regio maken en alle resources in de failover-locatie uitgeschakeld houden. Voor deze methode moet u nieuwe app-groepen en-werk ruimten instellen in de failover-regio. U kunt vervolgens een Azure Site Recovery-abonnement gebruiken om hostgroepen in te scha kelen.
- U kunt een hostgroep maken die wordt ingevuld door Vm's die zijn gebouwd in zowel de primaire als de failover-regio, terwijl de virtuele machines in de failovergebied blijven uitgeschakeld. In dit geval hoeft u slechts één hostgroep en de bijbehorende toepassings groepen en werk ruimten in te stellen. Met deze methode kunt u een Azure Site Recovery-abonnement gebruiken om hostgroepen op te zetten.

We raden u aan [Azure site Recovery](../site-recovery/site-recovery-overview.md) te gebruiken voor het beheren van gerepliceerde vm's in andere Azure-locaties, zoals beschreven in [Azure-to-Azure-nood herstel architectuur](../site-recovery/azure-to-azure-architecture.md). Het is raadzaam om Azure Site Recovery te gebruiken voor persoonlijke hostgroepen, omdat Azure Site Recovery zowel [op server-als op client gebaseerde sku's](../site-recovery/azure-to-azure-support-matrix.md#replicated-machine-operating-systems)ondersteunt.

Als u Azure Site Recovery gebruikt, hoeft u deze Vm's niet hand matig te registreren. De virtuele bureau blad-agent van Windows in de secundaire VM gebruikt automatisch het meest recente beveiligings token om verbinding te maken met het dichtstbijzijnde service-exemplaar. De virtuele machine (sessie-host) op de secundaire locatie wordt automatisch onderdeel van de hostgroep. De eind gebruiker moet tijdens het proces opnieuw verbinding maken, maar er zijn geen andere hand matige bewerkingen.

Als er tijdens de onderbreking bestaande gebruikers verbindingen zijn, moet u de gebruikers verbindingen in de huidige regio beëindigen voordat de beheerder de failover naar de secundaire regio kan starten.

Als u gebruikers wilt verbreken in het virtuele bureau blad van Windows (klassiek), voert u deze cmdlet uit:

```powershell
Invoke-RdsUserSessionLogoff
```

Als u gebruikers wilt verbreken in de met Azure geïntegreerde versie van Windows virtueel bureau blad, voert u de volgende cmdlet uit:

```powershell
Remove-AzWvdUserSession
```

Zodra u alle gebruikers in de primaire regio hebt afgemeld, kunt u een failover uitvoeren voor de virtuele machines in de primaire regio en gebruikers verbinding laten maken met de virtuele machines in de secundaire regio. Zie [virtuele Azure-machines repliceren naar een andere Azure-regio](../site-recovery/azure-to-azure-how-to-enable-replication.md)voor meer informatie over hoe dit proces werkt.

## <a name="virtual-network"></a>Virtueel netwerk

Overweeg vervolgens uw netwerk verbinding tijdens de storing. U moet ervoor zorgen dat u een virtueel netwerk (VNET) in uw secundaire regio hebt ingesteld. Als uw gebruikers toegang moeten hebben tot on-premises bronnen, moet u dit VNET configureren voor toegang tot de resources. U kunt on-premises verbindingen tot stand brengen met een VPN-, ExpressRoute-of virtueel WAN.

U kunt het beste Azure Site Recovery gebruiken om het VNET in te stellen in de failover-regio omdat het de instellingen van uw primaire netwerk behoudt en geen peering nodig heeft.

## <a name="user-identities"></a>Gebruikers identiteiten

Controleer vervolgens of de domein controller beschikbaar is op de secundaire locatie. 

Er zijn drie manieren om de domein controller beschikbaar te maken:

   - Active Directory-domein controller op secundaire locatie hebben
   - Een on-premises Active Directory-domein controller gebruiken
   - Active Directory-domein controller repliceren met behulp van [Azure site Recovery](../site-recovery/site-recovery-active-directory.md)

## <a name="user-and-app-data"></a>Gebruikers-en app-gegevens

Als u profiel containers gebruikt, is de volgende stap het instellen van gegevens replicatie op de secundaire locatie. U hebt vijf opties voor het opslaan van FSLogix-profielen:

   - S2D (Storage Spaces Direct)
   - Netwerk stations (VM met extra stations)
   - Azure Files
   - Azure NetApp Files
   - Cloud cache voor replicatie

Bekijk [opslag opties voor FSLogix-profiel containers in Windows Virtual Desktop](store-fslogix-profile.md)voor meer informatie.

Als u herstel na nood gevallen instelt voor profielen, kunt u kiezen uit de volgende opties:

   - Systeem eigen Azure-replicatie instellen (bijvoorbeeld Azure Files standaard replicatie van het opslag account, Azure NetApp Files replicatie of Azure Files synchronisatie voor bestands servers).
    
     >[!NOTE]
     >NetApp-replicatie wordt automatisch nadat u deze hebt ingesteld. Met Azure Site Recovery-abonnementen kunt u vooraf-scripts en post scripts toevoegen voor het uitvoeren van een failover van niet-VM-resources Azure Storage bronnen repliceren.

   - Stel FSLogix-Cloud cache in voor zowel app-als gebruikers gegevens.
   - Stel alleen herstel na nood gevallen voor app-gegevens in om ervoor te zorgen dat u altijd toegang hebt tot bedrijfs kritieke gegevens. Met deze methode kunt u gebruikers gegevens ophalen nadat de storing is overschreden.

Laten we eens kijken hoe u FSLogix kunt configureren om herstel na nood gevallen voor elke optie in te stellen.

### <a name="fslogix-configuration"></a>FSLogix-configuratie

De FSLogix-agent kan meerdere profiel locaties ondersteunen als u de Register vermeldingen voor FSLogix configureert.

De Register vermeldingen configureren:

1. Open de **register-editor**.
2. Ga naar **computer**  >  **HKEY_LOCAL_MACHINE**-profielen voor  >  **Software**  >  **FSLogix**  >  .
   
     > [!div class="mx-imgBorder"]
     > ![Een scherm opname van het venster profielen in de REGI ster-editor. VHDLocation is geselecteerd.](media/regedit-profiles.png)

3. Klik met de rechter muisknop op **VHDLocations** en selecteer **meerdere teken reeksen bewerken**.

     > [!div class="mx-imgBorder"]
     > ![Een scherm opname van het venster met meerdere teken reeksen bewerken. De waardegegevens bevat een lijst met de locaties voor Centrual VS en VS-Oost.](media/multi-string-edit.png)

4. Geef in het veld **Waardegegevens** de locaties op die u wilt gebruiken.
5. Wanneer u gereed bent, selecteert u **OK**.

Als de eerste locatie niet beschikbaar is, zal de FSLogix-agent automatisch een failover uitvoeren naar de tweede, enzovoort.

We raden u aan om de FSLogix-agent te configureren met een pad naar de secundaire locatie in de hoofd regio. Zodra de primaire locatie is afgesloten, wordt de FLogix-agent gerepliceerd als onderdeel van de virtuele machine Azure Site Recovery replicatie. Zodra de gerepliceerde Vm's gereed zijn, probeert de agent automatisch naar de secundaire regio te gaan.

Stel bijvoorbeeld dat uw virtuele machines voor de host van de primaire sessiehost zich in de regionale regio VS bevinden, maar uw profiel container bevindt zich in de regio VS-midden om prestatie redenen.

In dit geval configureert u de FSLogix-agent met een pad naar de opslag in de centrale vs. U configureert de host-Vm's voor de sessie om te repliceren in VS-West. Zodra het pad naar de Verenigde Staten mislukt, probeert de agent in plaats daarvan een nieuw pad te maken voor opslag in VS-West.

### <a name="s2d"></a>S2D

Aangezien S2D de replicatie voor de interne regio's intern afhandelt, hoeft u het secundaire pad niet hand matig in te stellen.

### <a name="network-drives-vm-with-extra-drives"></a>Netwerk stations (VM met extra stations)

Als u de virtuele machines met netwerk opslag repliceert met Azure Site Recovery zoals de host-Vm's voor sessies, behoudt het herstel hetzelfde pad, wat betekent dat u FSlogix niet opnieuw hoeft te configureren.

### <a name="azure-files"></a>Azure Files

Azure Files ondersteunt asynchrone replicatie tussen regio's die u kunt opgeven wanneer u het opslag account maakt. Als de asynchrone aard van Azure Files al uw doel stellingen voor herstel na nood gevallen omvat, hoeft u geen aanvullende configuratie te maken.

Als u synchrone replicatie nodig hebt om verlies van gegevens te voor komen, raden we u aan om in plaats daarvan FSLogix-Cloud cache te gebruiken.

>[!NOTE]
>Deze sectie heeft geen betrekking op het mechanisme voor failover-verificatie voor Azure Files.

### <a name="azure-netapp-files"></a>Azure NetApp Files

Meer informatie over Azure NetApp Files bij het [maken van replicatie-peering voor Azure NetApp files](../azure-netapp-files/cross-region-replication-create-peering.md).

## <a name="app-dependencies"></a>App-afhankelijkheden

Ten slotte moet u ervoor zorgen dat alle zakelijke apps die afhankelijk zijn van de gegevens in de primaire regio, een failover naar de secundaire locatie kunnen uitvoeren. Zorg er ook voor dat u de instellingen configureert die de apps moeten gebruiken op de nieuwe locatie. Als een van de apps bijvoorbeeld afhankelijk is van de SQL-back-end, zorgt u ervoor dat SQL op de secundaire locatie repliceert. U moet de app configureren voor het gebruik van de secundaire locatie als onderdeel van het failoverproces of als de standaard configuratie. U kunt app-afhankelijkheden op Azure Site Recovery plannen model leren. Zie [over herstel plannen](../site-recovery/recovery-plan-overview.md)voor meer informatie.

## <a name="disaster-recovery-testing"></a>Tests voor herstel na nood geval

Nadat u klaar bent met het instellen van herstel na nood gevallen, moet u uw plan testen om er zeker van te zijn dat het werkt.

Hier volgen enkele suggesties voor het testen van uw plan:

- Als de test-Vm's toegang hebben tot internet, nemen ze over op een bestaande sessiehost voor nieuwe verbindingen, maar alle bestaande verbindingen met de oorspronkelijke sessiehost blijven actief. Zorg ervoor dat de beheerder die de test uitvoert, alle actieve gebruikers afmeldt voordat het plan wordt getest. 
- U moet alleen volledige nood herstel tests uitvoeren tijdens een onderhouds venster om uw gebruikers te verstoren. U kunt ook een hostgroep in de validatie omgeving gebruiken voor de test. 
- Zorg ervoor dat uw test betrekking heeft op alle bedrijfskritische apps.
- U wordt aangeraden om Maxi maal 100 Vm's tegelijk een failover uit te voeren. Als u meer virtuele machines hebt dan die, kunt u het beste in batches tien minuten van elkaar een failover uitvoeren.

## <a name="next-steps"></a>Volgende stappen

Als u vragen hebt over het beveiligen van uw gegevens naast het plannen van uitval, raadpleegt u onze [beveiligings handleiding](security-guide.md).