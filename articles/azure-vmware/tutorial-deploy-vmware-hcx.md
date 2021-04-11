---
title: 'Zelfstudie: VMware HCX implementeren en configureren'
description: Meer informatie over het implementeren en configureren van een VMware HCX-oplossing voor de privécloud van uw Azure VMware Solution.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: ab59b4a29f8ba372c1892613d5e0461bf3fdc512
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869086"
---
# <a name="deploy-and-configure-vmware-hcx"></a>VMware HCX implementeren en configureren

In dit artikel wordt uitgelegd hoe de implementatie en configuratie van de on-premises VMware HCX-connector voor de privécloud van uw Azure VMware Solution in zijn werk gaat. Met VMware HCX kunt u uw VMware-workloads migreren naar Azure VMware Solution en andere verbonden sites mogelijk via verschillende migratietypen. Omdat Azure VMware Solution de HCX Cloud Manager implementeert en configureert, moet u de HCX-connector in uw on-premises VMware-datacenter downloaden, activeren en configureren.

VMware HCX Advanced Connector is vooraf geïmplementeerd in Azure VMware Solution. De connector ondersteunt maximaal drie siteverbindingen (on-premises naar de cloud of van cloud naar cloud). Als u meer dan drie site verbindingen nodig hebt, dient u een [ondersteuningsaanvraag](https://portal.azure.com/#create/Microsoft.Support) in om de invoegtoepassing [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) in te schakelen.  

>[!TIP]
>Hoewel het maximum aantal site paren van het hulp programma VMware-configuratie een beschrijving heeft van 25 tussen de on-premises connector en Cloud Manager, wordt dit door de licentie limiet beperkt tot drie voor HCX Advanced en 10 voor HCX Enter prise Edition.

>[!NOTE]
>VMware HCX Enterprise is beschikbaar bij Azure VMware Solution als een preview-service. Het is gratis en de voorwaarden voor een preview-service zijn van toepassing. Nadat de VMware HCX Enterprise-service algemeen beschikbaar is, krijgt u een melding dat de facturering over 30 dagen wordt omgeschakeld. U hebt ook de mogelijkheid om de service uit te schakelen of op te zeggen. Er is geen eenvoudig pad om van VMware HCX Enterprise naar VMware HCX Advanced te downgraden. Als u besluit om te downgraden, moet u de implementatie opnieuw uitvoeren. Dit brengt downtime met zich mee.

Neem eerst [Voordat u begint](#before-you-begin), [Vereisten voor de softwareversie](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html) en [Vereisten](#prerequisites) door. 

Vervolgens worden alle benodigde procedures doorlopen voor het volgende:

> [!div class="checklist"]
> * De VMware HCX-connector OVA downloaden.
> * De on-premises VMware HCX OVA (VMware HCX-connector) implementeren.
> * Activeer de VMware HCX-connector.
> * Koppel uw on-premises VMware HCX-connector aan uw Azure VMware Solution HCX-cloudbeheerder.
> * Configureer de interconnect (netwerkprofiel, rekenprofiel en service-mesh).
> * Voltooi de installatie door te controleren wat de apparaatstatus is en na te gaan of migratie mogelijk is.

Volg na afloop de aanbevolen volgende stappen aan het einde van dit artikel.  

## <a name="before-you-begin"></a>Voordat u begint

Wij raden u aan om ter voorbereiding van uw implementatie de volgende VMware-documentatie te raadplegen:

* [Gebruikershandleiding VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)
* [Virtuele machines migreren met VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [Overwegingen bij de implementatie van VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* [VMware-blogserie: cloudmigratie](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) 
* [Netwerkpoorten die vereist zijn voor VMware HCX](https://ports.vmware.com/home/VMware-HCX)


## <a name="prerequisites"></a>Vereisten

Als u van plan bent VMware HCX Enterprise te gebruiken, moet u ervoor zorgen dat u activering hebt aangevraagd via de ondersteuningskanalen van Azure VMware Solution.


### <a name="on-premises-vsphere-environment"></a>On-premises vSphere-omgeving

Zorg ervoor dat uw on-premises vSphere-omgeving (bronomgeving) voldoet aan de [minimale vereisten](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html). 

### <a name="network-and-ports"></a>Netwerk en poorten

* [Azure ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md) is geconfigureerd tussen on-premises en Azure VMware-oplossing Private Cloud ExpressRoute-circuits.

* [Alle vereiste poorten](https://ports.vmware.com/home/VMware-HCX) zijn geopend voor communicatie tussen on-premises onderdelen en Azure VMware-oplossingen persoonlijk.

### <a name="ip-addresses"></a>IP-adressen

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="download-the-vmware-hcx-connector-ova"></a>De VMware HCX-connector OVA downloaden

Voordat u het virtuele apparaat in uw on-premises vCenter implementeert, moet u de VMware HCX Connector OVA downloaden.  

1. Selecteer de Azure VMware Solution-privécloud in Azure Portal. 

1. Selecteer **Manage** > **Connectivity** en vervolgens het tabblad **HCX** om vast te stellen wat het IP-adres van Azure VMware Solution HCX Manager is. 

   :::image type="content" source="media/tutorial-vmware-hcx/find-hcx-ip-address.png" alt-text="Schermopname van het IP-adres van VMware HCX." lightbox="media/tutorial-vmware-hcx/find-hcx-ip-address.png":::

1. Selecteer   >  **identiteit** beheren. 

   De URL's en gebruikersreferenties voor vCenter en NSX-T-beheer van de privécloud worden weergegeven.

   > [!TIP]
   > Het vCenter-wachtwoord is gedefinieerd tijdens het instellen van de privécloud. Het is hetzelfde wachtwoord dat u gebruikt om u aan te melden bij de Azure VMware Solution HCX Manager. U kunt **een nieuw wacht woord genereren** selecteren om nieuwe vCenter-en NSX-T-wacht woorden te genereren.

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="URL's en referenties weergeven van vCenter en NSX-beheer van de privécloud." border="true":::


1. Open een browservenster, meld u aan bij Azure VMware Solution HCX Manager op `https://x.x.x.9`poort 443 met de gebruikersreferenties **cloudadmin\@vsphere.local**

1. Selecteer **Administration** > **System Updates** en selecteer vervolgens **Request Download Link**.

1. Selecteer de door u gewenste optie om het VMware HCX-connector OVA-bestand te downloaden.

## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>De VMware HCX Connector OVA on-premises implementeren

1. Selecteer in het on-premises vCenter een [OVF-sjabloon](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) om de VMware HCX-connector te implementeren in uw on-premises vCenter. 

   > [!TIP]
   > U gaat het OVA-bestand selecteren dat u in de vorige sectie hebt gedownload.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Schermopname van bladeren naar een OVF-sjabloon." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Selecteer een naam en locatie en vervolgens een resource of cluster waar u de HCX-connector implementeert. Bekijk vervolgens de details en de vereiste resources en selecteer **Next**.  

1. Bekijk de licentievoorwaarden. Als u akkoord gaat, selecteert u de vereiste opslag en het gewenste netwerk en selecteert u vervolgens **Next**.

1. Selecteer de opslag en selecteer **Next**.

1. Selecteer het netwerksegment VMware HCX Manager dat u eerder hebt gedefinieerd in de sectie [IP addresses prerequisites](#ip-addresses) .  Selecteer vervolgens **Volgende**.

1. Voer in **Customize template** alle vereiste gegevens in en selecteer **Next**. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Schermopname van de vakken voor het aanpassen van een sjabloon." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Controleer de configuratie en selecteer **Finish** om de VMware HCX-connector OVA te implementeren.
   
   > [!IMPORTANT]
   > U moet het virtuele apparaat handmatig inschakelen.  Wacht, nadat u het hebt ingeschakeld, 10 tot 15 minuten voordat u verdergaat met de volgende stap.

Bekijk voor een volledig overzicht van deze procedure de video [Azure VMware Solution: HCX Appliance Deployment](https://www.youtube.com/embed/UKmSTYrL6AY). 


## <a name="activate-vmware-hcx"></a>VMware HCX activeren

Nadat u de VMware HCX-connector OVA on-premises hebt geïmplementeerd en het apparaat hebt gestart, bent u klaar om te activeren. U moet eerst een licentiesleutel ophalen van de Azure VMware Solution-portal.

1. Ga in de Azure VMware Solution-portal naar **Beheren** > **Connectiviteit**, selecteer het tabblad **HCX** en selecteer **Toevoegen**.

1. Gebruik de **admin**-gebruikersreferenties om u aan te melden bij de on-premises VMware HCX Manager via `https://HCXManagerIP:9443`. 

   > [!TIP]
   > U hebt tijdens de implementatie van het VMware HCX Manager OVA-bestand het gebruikerswachtwoord van de **beheerder** gedefinieerd.

   > [!IMPORTANT]
   > Zorg ervoor dat u het `9443`-poortnummer met het IP-adres van VMware HCX Manager toevoegt.

1. Voer bij **Licensing** uw sleutel in bij **HCX Advanced Key** en selecteer **Activate**.  
   
    > [!NOTE]
    > VMware HCX Manager moet open internettoegang hebben of er moet een proxy voor zijn geconfigureerd.

1. Geef in **Datacenter Location** de dichtstbijzijnde locatie op voor on-premises installatie van VMware HCX Manager. Selecteer vervolgens **Doorgaan**.

1. Wijzig de naam bij **System Name** of accepteer de standaardwaarde en selecteer **Continue**.
   
1. Selecteer **Yes, Continue**.

1. Geef in **Connect your vCenter** de FQDN of het IP-adres van uw vCenter-server en de juiste referenties op en selecteer vervolgens **Continue**.
   
   > [!TIP]
   > De vCenter-server is waar u de VMware HCX-connector hebt geïmplementeerd in uw datacentrum.

1. Geef in **Configure SSO/PSC** de FQDN of het IP-adres van uw Platform Services Controller op en selecteer **Continue**.
   
   > [!NOTE]
   > Normaal gesproken is dit hetzelfde als uw FQDN of uw IP-adres voor vCenter.

1. Controleer of de ingevoerde gegevens juist zijn en selecteer **opnieuw starten**.
    
   > [!NOTE]
   > Er vindt een vertraging plaats na het opnieuw opstarten voordat u wordt gevraagd om de volgende stap.

Nadat de services opnieuw zijn gestart, zult u zien dat vCenter groen wordt weergegeven op het scherm dat verschijnt. Zowel vCenter als SSO moeten de juiste configuratieparameters hebben. Deze moeten hetzelfde zijn als in het vorige scherm.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Schermopname van het dashboard met de status groen voor vCenter." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Bekijk voor een volledig overzicht van deze procedure de video [Azure VMware Solution: Activeer de video over HCX ](https://www.youtube.com/embed/PnVg6SZkQsY?rel=0&amp;vq=hd720).

   > [!IMPORTANT]
   > Of u nu HCX Advanced of HCX Enter prise gebruikt, u moet mogelijk de patch installeren vanuit het [KB-artikel 81558](https://kb.vmware.com/s/article/81558)van VMware. 

## <a name="configure-the-vmware-hcx-connector"></a>VMware HCX-connector configureren

Nu bent u klaar om een sitekoppeling toe te voegen, een netwerk- en rekenprofiel te maken en services, zoals migratie, netwerkextensie of herstel na noodgevallen, in te schakelen. 

### <a name="add-a-site-pairing"></a>Een sitekoppeling toevoegen

U kunt de VMware HCX-cloudbeheerder in Azure VMware Solution verbinden met de VMware HCX-connector in uw datacentrum. 

1. Meld u aan bij uw on-premises vCenter en selecteer onder **Home** de optie **HCX**.

1. Selecteer onder **Infrastructure** de optie **Site Pairing** en selecteer vervolgens de optie **Connect To Remote Site** (in het midden van het scherm). 

1. Voer de Azure VMware-oplossing HCX Cloud Manager-URL of het IP-adres in die u eerder hebt genoteerd `https://x.x.x.9` , de Azure VMware-oplossing cloudadmin \@ vSphere. lokale gebruikers naam en het wacht woord. Selecteer vervolgens **Connect**.

   > [!NOTE]
   > Een site-paar maken:
   > * Uw VMware HCX-connector moet het IP-adres van uw HCX-cloudbeheerder kunnen omleiden via poort 443.
   >
   > * Gebruik hetzelfde wachtwoord dat u hebt gebruikt om u aan te melden bij vCenter. U hebt dit wachtwoord op het eerste implementatiescherm gedefinieerd.

   Er wordt een scherm weergegeven met een verbinding (koppeling) tussen uw VMware HCX-cloudbeheerder in Azure VMware Solution en uw on-premises VMware HCX-connector.

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Schermopname van de koppeling van HCX manager in Azure VMware Solution en de VMware HCX-connector.":::

Bekijk voor een volledig overzicht van deze procedure de video [Azure VMware Solution: Video over HCX-sitekoppeling](https://www.youtube.com/embed/jXOmYUnbWZY?rel=0&amp;vq=hd720).

### <a name="create-network-profiles"></a>Netwerkprofielen maken

De VMware HCX-connector implementeert een subset virtuele apparaten (geautomatiseerd) waarvoor meerdere IP-segmenten zijn vereist. Wanneer u uw netwerkprofielen maakt, gebruikt u de IP-segmenten die u hebt geïdentificeerd tijdens de [Voorbereidings- en planningsfase van VMware HCX-netwerksegmenten vóór de implementatie](production-ready-deployment-steps.md#vmware-hcx-network-segments).

U gaat vier netwerkprofielen maken:

   - Beheer
   - vMotion
   - Replicatie
   - Uplink

1. Onder **Infrastructure** selecteert u **Interconnect** > **Multi-Site Service Mesh** > **Network Profiles** > **Create Network Profile**.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Schermopname van selecties om een netwerkprofiel te gaan maken." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Voor elk netwerkprofiel selecteert u het netwerk en de poortgroep, geeft u een naam op en maakt u de IP-adresgroep voor het segment. Selecteer vervolgens **Maken**. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Schermopname van details voor een nieuw netwerkprofiel.":::

Bekijk voor een volledig overzicht van deze procedure de video [Azure VMware Solution: Video voor HCX-netwerkprofiel](https://www.youtube.com/embed/O0rU4jtXUxc).


### <a name="create-a-compute-profile"></a>Een rekenprofiel maken

1. Onder **Infrastructure** selecteert u **Interconnect** > **Compute Profiles** > **Create Compute Profile**.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Schermopname van de selecties om een rekenprofiel te gaan maken." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Voer een naam in voor het profiel en selecteer **Continue**.  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Schermopname met invoer van de naam van een rekenprofiel en de knop Continue." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Selecteer de services die u wilt inschakelen, zoals migratie, netwerkextensie of herstel na noodgevallen, en selecteer **Continue**.
  
   > [!NOTE]
   > Over het algemeen verandert hier niets.

1. Selecteer in **Select Service Resources** een of meer serviceresources (clusters) om de geselecteerde VMware HCX-services in te schakelen.  

1. Wanneer u de clusters in uw on-premises datacentrum ziet, selecteert u **Continue**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Schermopname van de geselecteerde serviceresources en de knop Continue." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. Selecteer in **Select Datastore** de gegevensopslagresource voor de implementatie van de VMware HCX Interconnect-apparaten. Selecteer vervolgens **Doorgaan**.

   Wanneer er meerdere resources zijn geselecteerd, gebruikt VMware HCX de eerste resource die is geselecteerd tot de capaciteit is uitgeput.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Schermopname van een geselecteerde resource voor gegevensopslag en de knop Continue." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. Selecteer bij **Select Management Network Profile** het beheernetwerkprofiel dat u in de vorige stappen hebt gemaakt. Selecteer vervolgens **Doorgaan**.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Schermopname van de selectie van een beheernetwerkprofiel en de knop Continue." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

1. Selecteer bij **Select Uplink Network Profile** het Uplink-netwerkprofiel dat u in de vorige stappen hebt gemaakt. Selecteer vervolgens **Doorgaan**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Schermopname van de selectie van een beheernetwerkprofiel en de knop Continue." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. Selecteer bij **Select vMotion Network Profile** het vMotion-netwerkprofiel dat u in de vorige stappen hebt gemaakt. Selecteer vervolgens **Doorgaan**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Schermopname van de selectie van een vMotion-netwerkprofiel en de knop Continue." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. Selecteer bij **Select vSphere Replication Network Profile** het replicatienetwerkprofiel dat u in de vorige stappen hebt gemaakt. Selecteer vervolgens **Doorgaan**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Schermopname van de selectie van een replicatienetwerkprofiel en de knop Continue." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. Selecteer bij **Select Distributed Switches for Network Extensions** de switches die de virtuele machines bevatten die moeten worden gemigreerd naar Azure VMware Solution op een uitgebreid netwerk in laag 2. Selecteer vervolgens **Doorgaan**.

   > [!NOTE]
   > Als u geen virtuele machines op laag 2 (L2) uitgebreide netwerken migreert, kunt u deze stap overs Laan.
   
   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Schermopname van de selectie van gedistribueerde virtuele switches en de knop Continue." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Controleer de verbindingsregels en selecteer **Continue**.  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Schermopname van de verbindingsregels en de knop Continue." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Selecteer **Finish** om het rekenprofiel te voltooien.


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Schermopname van gegevens van een rekenprofiel." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Bekijk voor een volledig overzicht van deze procedure de video [Azure VMware Solution: Video van rekenprofiel](https://www.youtube.com/embed/e02hsChI3b8).

### <a name="create-a-service-mesh"></a>Een service-mesh maken

Nu is het tijd om een service-net te configureren tussen on-premises en Azure VMware-oplossing privécloud.



> [!NOTE]
> Een service-mesh maken met Azure VMware Solution:
>
> * Poorten UDP 500/4500 zijn geopend tussen uw on-premises VMware HCX-connector 'uplink'-netwerkprofieladressen en de 'uplink'-netwerkprofieladressen voor Azure VMware Solution HCX Cloud.
>
> * Vergeet niet [De voor VMware HCX vereiste poorten](https://ports.vmware.com/home/VMware-HCX) te raadplegen.

1. Selecteer onder **Infrastructure** achtereenvolgens **Interconnect** > **Service Mesh** > **Create Service Mesh**.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Schermopname van selecties om een service-mesh te gaan maken." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Controleer de sites die vooraf zijn ingevuld en selecteer **Continue**. 

   > [!NOTE]
   > Als dit uw eerste service-meshconfiguratie is, hoeft u dit scherm niet te wijzigen.  

1. Selecteer de bron en externe rekenprofielen in de vervolgkeuzelijsten en selecteer **Continue**.  

   De selecties definiëren de resources waarin VM's VMware HCX-services kunnen gebruiken.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Schermopname van het selecteren van het bronrekenprofiel." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Schermopname van het selecteren van het externe rekenprofiel." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Controleer de services die worden ingeschakeld en selecteer **Continue**.  

1. Selecteer in **Advanced Configuration - Override Uplink Network profiles** de optie **Continue**.  

   Uplink-netwerkprofielen maken verbinding met het netwerk via welk de verbindingsapparaten van de externe site kunnen worden bereikt.  
  
1. Controleer de gegevens in **Advanced Configuration - Network Extension Appliance Scale Out** en selecteer **Continue**. 

   U kunt maximaal acht VLAN's per apparaat hebben, maar u kunt een ander apparaat implementeren om nog acht VLAN's toe te voegen. U moet ook over de IP-ruimte beschikken voor de extra apparaten en er geldt één IP per apparaat.  Zie [Limieten voor VMware HCX-configuratie](https://configmax.vmware.com/guest?vmwareproduct=VMware%20HCX&release=VMware%20HCX&categories=41-0,42-0,43-0,44-0,45-0) voor meer informatie.
   
   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png" alt-text="Schermopname die laat zien waar het aantal VLAN's moet worden verhoogd." lightbox="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png":::

1. Controleer de gegevens in **Advanced Configuration - Traffic Engineering**, breng de benodigde wijzigingen aan en selecteer **Continue**.

1. Bekijk de preview van de topologie en selecteer **Continue**.

1. Voer een gebruiksvriendelijke naam in voor deze service-mesh en selecteer **Finish** om de bewerking te voltooien.  

1. Selecteer **View Tasks** om de implementatie te controleren. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Schermopname met de knop voor het bekijken van taken.":::

   Wanneer de implementatie van de service-mesh is voltooid, worden de services groen weergegeven.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Schermopname met groene indicatoren bij services." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Verifieer de status van de service-mesh door de status van het apparaat te controleren. 

1. Selecteer **Interconnect** > **Appliances**.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Schermopname met selecties voor het controleren van de status van het apparaat." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Bekijk voor een volledig overzicht van deze procedure de video [Azure VMware Solution: Video over service-mesh](https://www.youtube.com/embed/COY3oIws108).

### <a name="optional-create-a-network-extension"></a>(Optioneel) Een netwerkextensie maken

Als u netwerken van uw on-premises omgeving wilt uitbreiden naar Azure VMware-oplossing, voert u de volgende stappen uit:

1. Selecteer onder **Services** de optie **Network Extension** > **Create a Network Extension**.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Schermopname van de selecties om een rekenprofiel te gaan maken." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Selecteer elk netwerk dat u wilt uitbreiden naar Azure VMware Solution en selecteer **Next**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Schermopname van de selectie van een netwerk.":::

1. Voer het IP-adres van de on-premises gateway in voor elk van de netwerken die u uitbreidt en selecteer vervolgens **Submit**. 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Schermopname van de vermelding van een gateway-IP-adres.":::

   Het duurt enkele minuten voordat de netwerkextensie is voltooid. Wanneer dit het geval is, ziet u dat de status is gewijzigd in **extension complete**.

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Schermopname van de status van Extensie voltooid." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Bekijk voor een volledig overzicht van deze procedure de video [Azure VMware Solution: Video van netwerkextensie](https://www.youtube.com/embed/gYR0nftKui0).


## <a name="next-steps"></a>Volgende stappen

Als de HCX Interconnect-tunnel status is **ingesteld** op groen, kunt u virtuele machines van Azure VMware-oplossingen migreren en beveiligen met behulp van VMware HCX. De Azure VMware Solution ondersteunt migraties van workloads (met of zonder een netwerkextensie). U kunt nog steeds workload migreren in uw vSphere-omgeving, naast het on-premises maken van netwerken en de implementatie van virtuele machines op die netwerken.  

Ga naar de technische documentatie voor VMware voor meer informatie over het gebruik van HCX:

* [Documentatie voor VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html)
* [Virtuele machines migreren met VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [Vereiste poorten voor HCX](https://ports.vmware.com/home/VMware-HCX)
* [Stel een HCX-proxy server in voordat u de licentie sleutel goedkeurt](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-920242B3-71A3-4B24-9ACF-B20345244AB2.html?hWord=N4IghgNiBcIA4CcD2APAngAgBIGEAaIAvkA)
