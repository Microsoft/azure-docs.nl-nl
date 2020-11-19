---
title: Veelgestelde vragen
description: Hier vindt u antwoorden op enkele veelgestelde vragen over de Azure VMware-oplossing.
ms.topic: conceptual
ms.date: 11/12/2020
ms.author: dikamath
ms.openlocfilehash: f83413bc54164107b8e8a4e6010f8a826a5fe327
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888416"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution"></a>Veelgestelde vragen over de Azure VMware-oplossing

Antwoorden op veelgestelde vragen over de Azure VMware-oplossing.

## <a name="general"></a>Algemeen

#### <a name="what-is-azure-vmware-solution"></a>Wat is Azure VMware Solution?

Als ondernemingen de strategie voor modernisatie verhogen om de flexibiliteit van het bedrijf te verbeteren, kosten te verlagen en innovatie te versnellen, zijn hybride Cloud platforms opgegroeid als belangrijkste digitale trans formatie van klanten. De oplossing Azure VMware combineert de Software-Defined Data Center-software van VMware (SDDC) met Microsoft Azure Global Cloud service-ecosysteem. De Azure VMware-oplossing wordt beheerd om te voldoen aan de vereisten voor prestaties, Beschik baarheid, beveiliging en naleving.

## <a name="azure-vmware-solution-service"></a>Azure VMware-oplossings service

#### <a name="where-is-azure-vmware-solution-available-today"></a>Waar is de Azure VMware-oplossing vandaag nog beschikbaar?

De service wordt doorlopend toegevoegd aan nieuwe regio's, dus Bekijk de [meest recente informatie over de service-Beschik baarheid](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) voor meer informatie. 

#### <a name="can-workloads-running-in-an-azure-vmware-solution-instance-consume-or-integrate-with-azure-services"></a>Kunnen werk belastingen die worden uitgevoerd in een exemplaar van de Azure VMware-oplossing worden gebruikt of worden geïntegreerd met Azure-Services?

Alle Azure-Services zijn beschikbaar voor klanten met Azure VMware-oplossingen. De beperkingen voor de prestaties en de beschik baarheid van specifieke services moeten per geval worden behandeld.

#### <a name="do-i-use-the-same-tools-that-i-use-now-to-manage-private-cloud-resources"></a>Gebruik ik dezelfde hulp middelen die ik nu gebruik voor het beheren van persoonlijke Cloud bronnen?

Ja. De Azure Portal wordt gebruikt voor implementatie en een aantal beheer bewerkingen. vCenter en NSX Manager worden gebruikt voor het beheren van vSphere-en NSX-T-resources.

#### <a name="can-i-manage-a-private-cloud-with-my-on-premises-vcenter"></a>Kan ik een privécloud beheren met mijn on-premises vCenter?

Bij het starten ondersteunt de Azure VMware-oplossing geen enkele beheer ervaring in on-premises en particuliere cloud omgevingen. Particuliere cloud clusters worden beheerd met vCenter en NSX Manager lokaal naar een privécloud.

#### <a name="can-i-use-vrealize-suite-running-on-premises"></a>Kan ik vRealize-Suite gebruiken die on-premises wordt uitgevoerd? 

Specifieke integratie-en gebruiks voorbeelden kunnen per geval worden geëvalueerd.

#### <a name="can-i-migrate-vsphere-vms-from-on-premises-environments-to-azure-vmware-solution-private-clouds"></a>Kan ik vSphere-Vm's migreren van on-premises omgevingen naar persoonlijke Clouds van Azure VMware?

Ja. VM-migratie en vMotion kunnen worden gebruikt om Vm's naar een privécloud te verplaatsen als aan de standaard vereisten voor cross vCenter van [vMotion](https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=2106952) wordt voldaan.

#### <a name="is-a-specific-version-of-vsphere-required-in-on-premises-environments"></a>Is een specifieke versie van vSphere vereist in on-premises omgevingen?

Alle Cloud omgevingen worden geleverd met VMware HCX, vSphere 5,5 of hoger in on-premises omgevingen voor vMotion.

#### <a name="what-does-the-change-control-process-look-like"></a>Wat ziet het wijzigings controle proces eruit?

Updates die in de service zelf zijn aangebracht, volgen het standaard proces voor wijzigings beheer van Microsoft Azure. Klanten zijn verantwoordelijk voor werkbelasting beheer taken en de bijbehorende processen voor wijzigings beheer.

#### <a name="how-is-this-different-from-azure-vmware-solution-by-cloudsimple"></a>Wat is het verschil tussen de Azure VMware-oplossing en CloudSimple?

Met de nieuwe Azure VMware-oplossing beschikt micro soft en VMware over een direct partnerschap voor de Cloud provider. De nieuwe oplossing is volledig ontworpen, gebouwd en ondersteund door micro soft en goedgekeurd door VMware. In architectuur zijn de oplossingen consistent, met de VMware-technologie stack die wordt uitgevoerd op een specifieke Azure-infra structuur.



#### <a name="can-azure-vmware-solution-vms-be-managed-by-vmrc"></a>Kunnen Vm's van Azure VMware-oplossingen worden beheerd door VMRC?
Ja, op voor waarde dat het systeem waarop het is geïnstalleerd, toegang kan krijgen tot de privécloud en de open bare DNS gebruikt om ESXi-hostnamen op te lossen.

#### <a name="are-there-special-instructions-for-installing-and-using-vmrc-with-azure-vmware-solution-vms"></a>Zijn er speciale instructies voor het installeren en gebruiken van VMRC met virtuele machines met Azure VMware-oplossingen?
Nee, gebruik de [instructies van VMware](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-89E7E8F0-DB2B-437F-8F70-BA34C505053F.html) en voer de vereiste VM-vereisten in deze instructies uit. 

#### <a name="is-vmware-hcx-supported-on-vpns"></a>Worden VMware-HCX ondersteund op Vpn's?
Nee, vanwege de vereisten voor band breedte en latentie.

#### <a name="can-azure-bastion-be-used-for-connecting-to-azure-vmware-solution-vms"></a>Kan Azure Bastion worden gebruikt om verbinding te maken met virtuele machines van Azure VMware-oplossingen?
Azure Bastion is de aanbevolen service om verbinding te maken met het Jump box om te voor komen dat de Azure VMware-oplossing aan Internet wordt blootgesteld. U kunt Azure Bastion niet gebruiken om verbinding te maken met virtuele machines van Azure VMware-oplossingen omdat ze geen Azure IaaS-objecten zijn.

#### <a name="can-azure-load-balancer-internal-be-used-for-azure-vmware-solution-vms"></a>Kan Azure Load Balancer intern worden gebruikt voor virtuele machines met Azure VMware-oplossingen?
Nee. Azure Load Balancer intern ondersteunt alleen virtuele Azure IaaS-machines. Azure Load Balancer biedt geen ondersteuning voor back-endservers op basis van IP-adressen; alleen Azure-Vm's of objecten voor virtuele-machine schaal sets waarin Azure VMware-oplossings-Vm's geen Azure-objecten zijn.

#### <a name="can-an-existing-expressroute-gateway-be-used-to-connect-to-azure-vmware-solution"></a>Kan een bestaande ExpressRoute-gateway worden gebruikt om verbinding te maken met de Azure VMware-oplossing?
Ja, u kunt een bestaande ExpressRoute-gateway gebruiken om verbinding te maken met de Azure VMware-oplossing zolang deze de limiet van vier ExpressRoute-circuits per virtueel netwerk niet overschrijdt.  Om toegang te krijgen tot Azure VMware Solution on-premises via ExpressRoute, moet u beschikken over ExpressRoute Global Reach, omdat de ExpressRoute-gateway geen transitieve routering biedt tussen de verbonden circuits.

## <a name="compute-network-storage-and-backup"></a>Berekenings-, netwerk-, opslag-en back-ups

#### <a name="is-there-more-than-one-type-of-host-available"></a>Is er meer dan één type host beschikbaar?

Er is slechts één type host beschikbaar.

#### <a name="what-are-the-cpu-specifications-in-each-type-of-host"></a>Wat zijn de CPU-specificaties in elk type host?

De servers hebben een Dual 18 Core 2,3 GHz Intel Cpu's.

#### <a name="how-much-memory-is-in-each-host"></a>Hoeveel geheugen bevindt zich in elke host?

De servers hebben 576 GB RAM-geheugen.

#### <a name="what-is-the-storage-capacity-of-each-host"></a>Wat is de opslag capaciteit van elke host?

Elke ESXi-host heeft twee vSAN-diskgroups met een capaciteits categorie van 15,2 TB en een NVMe-cache-laag van 3,2-TB (1,6 TB in elke diskgroup).

#### <a name="how-much-network-bandwidth-is-available-in-each-esxi-host"></a>Hoeveel netwerk bandbreedte is beschikbaar op elke ESXi-host?

Elke ESXi-host in de Azure VMware-oplossing is geconfigureerd met 4 25-Gbps Nic's, twee Nic's die zijn ingericht voor het ESXi-systeem verkeer en twee Nic's die zijn ingericht voor werkbelasting verkeer. 

#### <a name="is-data-stored-on-the-vsan-datastores-encrypted-at-rest"></a>Worden gegevens opgeslagen op de vSAN-data stores die zijn versleuteld op rest?

Ja, alle vSAN-gegevens worden standaard versleuteld met sleutels die zijn opgeslagen in Azure Key Vault.

#### <a name="you-document-that-commvault-veritas-and-veeam-have-extended-their-backup-solutions-to-work-with-azure-vmware-solution-what-about-other-independent-software-vendor-isv-backup-solutions"></a>U documenteert dat CommVault, Veritas en Veeam hun back-upoplossingen hebben uitgebreid om met de Azure VMware-oplossing te werken. Hoe zit het met andere back-upoplossingen voor onafhankelijke software leveranciers (ISV)?

Net als we weten, moeten alle back-upoplossingen die gebruikmaken van VMware VADP met de HotAdd-transport modus, direct worden gebruikt in het vak op de Azure VMware-oplossing.

#### <a name="what-about-support-for-isv-backup-solutions"></a>Hoe zit het met ondersteuning voor ISV-back-upoplossingen?

Omdat deze back-upoplossingen worden geïnstalleerd en beheerd door klanten, kunnen ze contact met de desbetreffende ISV vinden voor ondersteuning. 

#### <a name="what-is-the-correct-storage-policy-for-the-dedupe-setup"></a>Wat is het juiste opslag beleid voor de instelling voor ontdubbeling?

Gebruik het *thin_provision* -opslag beleid voor uw VM-sjabloon.  De standaard waarde is *thick_provision*.

#### <a name="are-the-snmp-infrastructure-logs-shared"></a>Worden de logboeken voor de SNMP-infra structuur gedeeld?

Nee.

## <a name="hosts-clusters-and-private-clouds"></a>Hosts, clusters en privéclouds

#### <a name="is-the-underlying-infrastructure-shared"></a>Wordt de onderliggende infra structuur gedeeld?

Nee, hosts en clusters in de privécloud zijn toegewezen en veilig verwijderd voor en na gebruik.

#### <a name="what-are-the-minimum-and-the-maximum-number-of-hosts-per-cluster"></a>Wat is het minimum en het maximum aantal hosts per cluster?

Clusters kunnen worden geschaald tussen 3-en 16 ESXi-hosts. Proef clusters zijn beperkt tot drie hosts.

#### <a name="can-i-scale-my-private-cloud-clusters"></a>Kan ik mijn persoonlijke Cloud clusters schalen?

Ja, clusters worden geschaald tussen het minimum en het maximum aantal ESXi-hosts. Proef clusters zijn beperkt tot drie hosts.

#### <a name="what-are-trial-clusters"></a>Wat zijn proef clusters?

Proef clusters zijn drie host-clusters die worden gebruikt voor evaluaties van Azure VMware-oplossingen met persoonlijke Clouds voor één maand.

#### <a name="can-i-use-high-end-hosts-for-trial-clusters"></a>Kan ik hoogwaardige hosts gebruiken voor proef clusters?

Nee. High-end ESXi-hosts zijn gereserveerd voor gebruik in productie clusters.

## <a name="azure-vmware-solution-and-vmware-software"></a>Azure VMware-oplossing en VMware-software

#### <a name="what-versions-of-vmware-software-is-used-in-private-clouds"></a>Welke versies van VMware-software worden gebruikt in persoonlijke Clouds?

Persoonlijke Clouds gebruiken vSphere 6,7, vSAN 6,7, VMware HCX en versie 2,5 van NSX-T.  

#### <a name="do-private-clouds-use-vmware-nsx"></a>Gebruiken persoonlijke Clouds VMware NSX?

Ja, NSX-T 2,5 wordt gebruikt voor het door software gedefinieerde netwerk in persoonlijke Clouds van Azure VMware.

#### <a name="can-i-use-vmware-nsx-v-in-a-private-cloud"></a>Kan ik VMware NSX-V in een privécloud gebruiken?

Nee. NSX-T is de enige ondersteunde versie van NSX.

#### <a name="is-nsx-required-in-on-premises-environments-or-networks-that-connect-to-a-private-cloud"></a>Is NSX vereist in on-premises omgevingen of netwerken die verbinding maken met een privécloud?

Nee, u hoeft NSX niet on-premises te gebruiken.

#### <a name="what-is-the-upgrade-and-update-schedule-for-vmware-software-in-a-private-cloud"></a>Wat is de upgrade en update planning voor VMware-software in een privécloud?

De software bundel upgrades van de privécloud worden uitgevoerd om de software binnen één versie van de meest recente software bundel versie van VMware te blijven gebruiken. De software versies van de privécloud kunnen afwijken van de meest recente versies van de afzonderlijke software onderdelen (ESXi, NSX-T, vCenter, vSAN).

#### <a name="how-often-will-the-private-cloud-software-stack-be-updated"></a>Hoe vaak wordt de software stack voor de privécloud bijgewerkt?

De software van de privécloud wordt bijgewerkt volgens een schema dat de release van de software bundel uit VMware bijhoudt. Uw privécloud vereist geen uitval tijd voor upgrades.

## <a name="connectivity"></a>Connectiviteit

#### <a name="what-network-ip-address-planning-is-required-to-incorporate-private-clouds-with-on-premises-environments"></a>Welk netwerk-IP-adres moet er worden gepland om persoonlijke Clouds te integreren met on-premises omgevingen?

Er is een privé netwerk/22 adres ruimte vereist voor het implementeren van een privécloud van Azure VMware-oplossing. Deze privé adres ruimte mag niet overlappen met andere virtuele netwerken in een abonnement of met on-premises netwerken.
 
#### <a name="how-do-i-connect-from-on-premises-environments-to-an-azure-vmware-solution-private-cloud"></a>Hoe kan ik verbinding maken vanuit on-premises omgevingen met een privécloud van Azure VMware-oplossing?

U kunt op een van de volgende twee manieren verbinding maken met de service: 

- Met een virtuele machine of toepassings gateway die is geïmplementeerd op een virtueel Azure-netwerk dat is gekoppeld via ExpressRoute aan de privécloud.
- Via ExpressRoute Global Reach van uw on-premises Data Center naar een Azure ExpressRoute-circuit.

#### <a name="how-do-i-connect-a-workload-vm-to-the-internet-or-an-azure-service-endpoint"></a>Hoe kan ik een werk belasting-VM met internet of een Azure service-eind punt verbinden?

Schakel in het Azure Portal Internet connectiviteit in voor een privécloud. Maak met NSX-T-Manager een NSX-T T1-router en een logische switch. Vervolgens gebruikt u vCenter om een virtuele machine te implementeren op het netwerk segment dat is gedefinieerd door de logische switch. Die VM heeft netwerk toegang tot het internet en naar Azure-Services.

#### <a name="do-i-need-to-restrict-access-from-the-internet-to-vms-on-logical-networks-in-a-private-cloud"></a>Moet ik de toegang van Internet tot Vm's op logische netwerken in een privécloud beperken?

Nee. Netwerk verkeer van het Internet rechtstreeks naar persoonlijke Clouds is niet toegestaan.

#### <a name="do-i-need-to-restrict-internet-access-from-vms-on-logical-networks-to-the-internet"></a>Moet ik Internet toegang beperken van Vm's op logische netwerken naar het Internet?

Ja. U moet NSX-T-beheer gebruiken om een firewall te maken waarmee de toegang tot internet wordt beperkt.


#### <a name="can-azure-vmware-solution-use-azure-virtual-wan-hosted-expressroute-gateways"></a>Kan de Azure VMware-oplossing Azure Virtual WAN hosted ExpressRoute-gateways gebruiken?
Ja.

#### <a name="can-transit-connectivity-be-established-between-on-premises-and-azure-vmware-solution-through-azure-virtual-wan-over-expressroute-global-reach"></a>Kan Transit connectiviteit tot stand worden gebracht tussen on-premises en Azure VMware-oplossing via Azure Virtual WAN via ExpressRoute Global Reach?
Virtuele WAN-verbindingen van Azure bieden geen transitieve route ring tussen twee verbonden ExpressRoute-circuits en een niet-virtuele WAN ExpressRoute-gateway. Met behulp van ExpressRoute Global Reach maakt u verbinding tussen de on-premises en Azure VMware-oplossing, maar gaat u door het wereld wijde netwerk van micro soft in plaats van de virtuele WAN-hub.


## <a name="accounts-and-privileges"></a>Accounts en bevoegdheden

#### <a name="what-accounts-and-privileges-will-i-get-with-my-new-azure-vmware-solution-private-cloud"></a>Welke accounts en bevoegdheden krijg ik bij mijn nieuwe Azure VMware-oplossing privécloud?

U kunt referenties opgeven voor een cloudadmin-gebruiker in vCenter en beheerders toegang op NSX-T-beheer. Er is ook een CloudAdmin-groep die kan worden gebruikt om Azure Active Directory op te nemen. Zie [toegangs-en identiteits concepten](concepts-identity.md)voor meer informatie.

#### <a name="can-have-administrator-access-to-esxi-hosts"></a>Kan beheerders toegang hebben tot ESXi-hosts?

Nee, beheerders toegang tot ESXi is beperkt om te voldoen aan de beveiligings vereisten van de oplossing.

#### <a name="what-privileges-and-permissions-will-i-have-in-vcenter"></a>Welke bevoegdheden en machtigingen heeft ik in vCenter?

U hebt CloudAdmin-groeps privileges. Zie [toegangs-en identiteits concepten](concepts-identity.md)voor meer informatie.

#### <a name="what-privileges-and-permissions-will-i-have-on-the-nsx-t-manager"></a>Welke bevoegdheden en machtigingen heeft ik op de NSX-T-Manager?

U hebt volledige beheerders bevoegdheden voor NSX-T en kan op rollen gebaseerd toegangs beheer op basis van vSphere beheren, zoals u dat ook zou doen met NSX-T Data Center. Zie [toegangs-en identiteits concepten](concepts-identity.md)voor meer informatie.

> [!NOTE]
> Er wordt een T0-router gemaakt en geconfigureerd als onderdeel van de implementatie van een privécloud. Elke wijziging van die logische router of de NSX van de virtuele machine van het rand knooppunt kan invloed hebben op de connectiviteit met uw privécloud.

## <a name="billing-and-support"></a>Facturering en ondersteuning

#### <a name="how-will-pricing-be-structured-for-azure-vmware-solution"></a>Hoe worden de prijzen gestructureerd voor de Azure VMware-oplossing?

Zie de pagina met [prijzen](https://azure.microsoft.com/pricing/details/azure-vmware) voor Azure VMware-oplossingen voor algemene vragen over prijzen. 

#### <a name="can-azure-vmware-solution-be-purchased-through-a-microsoft-csp"></a>Kan de Azure VMware-oplossing worden aangeschaft via een micro soft CSP?

Ja, klanten kunnen Azure VMware-oplossing implementeren binnen een Azure-abonnement dat wordt beheerd door een CSP.

#### <a name="who-supports-azure-vmware-solution"></a>Wie ondersteunt de Azure VMware-oplossing?

Micro soft biedt ondersteuning voor de Azure VMware-oplossing. U kunt een [ondersteunings aanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)indienen. 

Voor door CSP beheerde abonnementen wordt het eerste ondersteunings niveau geboden door de oplossings provider op dezelfde manier als CSP voor andere Azure-Services.

#### <a name="what-accounts-do-i-need-to-create-an-azure-vmware-solution-private-cloud"></a>Welke accounts heb ik nodig om een privécloud van Azure VMware-oplossing te maken?

U hebt een Azure-account in een Azure-abonnement nodig.

#### <a name="are-red-hat-solutions-supported-on-azure-vmware-solution"></a>Worden Red Hat-oplossingen ondersteund op de Azure VMware-oplossing?

Micro soft en Red Hat delen een geïntegreerd ondersteunings team dat een uniform contact punt biedt voor Red Hat-ecosystemen die worden uitgevoerd op het Azure-platform.  Net als andere Azure-platform services die met Red Hat Enterprise Linux werken, valt de Azure VMware-oplossing onder de Cloud toegang en het geïntegreerde ondersteunings paraplu, en wordt Red Hat Enterprise Linux ondersteund voor het uitvoeren van een Azure VMware-oplossing in Azure.

#### <a name="is-vmware-hcx-enterprise-edition-available-and-if-so-how-much-does-it-cost"></a>Is VMware HCX Enter prise Edition beschikbaar, en zo ja, hoeveel kost het?

VMware HCX Enterprise Edition (EE) is beschikbaar met Azure VMware Solution als een *preview*-functie/-service. Hoewel VMware HCX EE voor de Azure VMware-oplossing in preview is, is het een gratis functie/service en onderhevig aan de voor waarden van de preview-versie van de service. Zodra de VMware HCX EE-service algemeen beschikbaar wordt, krijgt u een melding dat de facturering over 30 dagen wordt omgeschakeld. U kunt de service uitschakelen of u afmelden.

#### <a name="how-do-i-request-a-host-quota-increase-for-azure-vmware-solution"></a>Hoe kan ik een toename voor een quotum van een host voor een Azure VMware-oplossing aanvragen?

Voor door CSP beheerde abonnementen moet de klant de aanvraag indienen bij de partner. Het partner team gaat vervolgens samen met micro soft om het quotum voor het abonnement te verg root. Zie het [artikel Azure VMware Solution resource inschakelen](enable-azure-vmware-solution.md) voor meer informatie. 

Gebruik de volgende procedure voor EA-abonnementen. Eerst hebt u het volgende nodig:

* Een [Azure Enterprise Agreement (EA)](../cost-management-billing/manage/ea-portal-agreements.md) met micro soft.
* Een Azure-account in een Azure-abonnement.

Voordat u uw Azure VMware-oplossings resource maakt, moet u een ondersteunings ticket indienen om uw knoop punten toe te wijzen. Het duurt Maxi maal vijf werk dagen om uw aanvraag te bevestigen en uw knoop punten toe te wijzen. Als u een bestaande privécloud van Azure VMware Solution hebt en u meer knooppunten wilt toewijzen, dan volgt u hetzelfde proces.


1. Maak in uw Azure Portal onder **Help en ondersteuning** een **[nieuwe ondersteunings aanvraag](https://rc.portal.azure.com/#create/Microsoft.Support)** en geef de volgende informatie op voor het ticket:
   - **Type probleem:** Documentatie
   - **Abonnement:** Uw abonnement selecteren
   - **Service:** Alle services > Azure VMware-oplossing
   - **Resource:** Algemene vraag 
   - **Samen vatting:** Capaciteit nodig
   - **Probleem type:** Problemen met capaciteits beheer
   - **Subtype van probleem:** Klant aanvraag voor extra quotum/capaciteit van host

1. Geef in de **Beschrijving** van het ondersteunings ticket op het tabblad **Details** het volgende op:

   - HAALBAARHEIDs-of productie 
   - Naam regio
   - Aantal knooppunten
   - Eventuele andere gegevens

   >[!NOTE]
   >De Azure VMware-oplossing raadt ten minste drie knoop punten aan om uw privécloud te maken en voor redundantie-N + 1-knoop punten. 

1. Selecteer **beoordeling + maken** om de aanvraag in te dienen.

   Het duurt Maxi maal vijf werk dagen voor een ondersteunings medewerker om uw aanvraag te bevestigen.

   >[!IMPORTANT] 
   >Als u al een bestaande Azure VMware-oplossing hebt en u extra knoop punten aanvraagt, moeten we vijf werk dagen hebben om de knoop punten toe te wijzen. 

1. Voordat u uw knoop punten kunt inrichten, moet u ervoor zorgen dat u de resource provider **micro soft. AVS** registreert in de Azure Portal.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>

   For additional ways to register the resource provider, see [Azure resource providers and types](../azure-resource-manager/management/resource-providers-and-types.md). 

#### Are Reserved Instances available for purchasing through the Cloud Solution Provider (CSP) program?

Yes. CSP can purchase reserved instances for their customers. For more information, see the [Save costs with a reserved instance](reserved-instance.md) article. 

#### Does Azure VMware Solution offer multi-tenancy for hosting CSP partners?

No. Currently Azure VMware Solution doesn't offer multi-tenancy.

#### Will traffic between on-premises and Azure VMware Solution over ExpressRoute incur any outbound data transfer charge in the metered data plan?

Traffic in the Azure VMware Solution ExpressRoute circuit isn't metered in any way. Traffic from your ExpressRoute circuit connecting to your on-premises to Azure is charged according to ExpressRoute pricing plans.


## Customer communication

#### How can I receive an alert when Azure sends service health notifications to my Azure subscription?

Service issues, planned maintenance, health advisories, security advisories notifications are published through **Service Health** in the Azure portal.  You can take timely actions when you set up activity log alerts for these notifications. For more information, see [Create service health alerts using the Azure portal](../service-health/alerts-activity-log-service-notifications-portal.md#create-service-health-alert-using-azure-portal).

:::image type="content" source="media/service-health.png" alt-text="Screenshot of Service Health notifications":::



<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=21069522

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
