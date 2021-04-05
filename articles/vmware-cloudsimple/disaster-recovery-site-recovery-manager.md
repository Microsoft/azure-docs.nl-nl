---
title: 'Azure VMware-oplossing door CloudSimple: Stel de Privécloud in als een nood herstel site met behulp van VMware Site Recovery Manager'
description: Hierin wordt beschreven hoe u uw CloudSimple-Privécloud instelt als een nood herstel site met behulp van VMware Site Recovery Manager.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3d71f8aec1f35514ac6c10b17b6f7b69b79b05bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97897906"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>Een Privécloud instellen als een nood herstel doel met VMware Site Recovery Manager

U kunt uw persoonlijke cloud van CloudSimple gebruiken als een nood herstel site (DR) voor on-premises VMware-workloads.

De oplossing DR is gebaseerd op vSphere-replicatie en VMware Site Recovery Manager Een soort gelijke benadering kan worden gevolgd om uw Privécloud in te scha kelen als een primaire site die wordt beveiligd door uw on-premises herstel site.

De CloudSimple-oplossing:

* Elimineert de nood zaak om een Data Center in te stellen dat specifiek is voor DR.
* Met kunt u gebruikmaken van de Azure-locaties waar CloudSimple wordt geïmplementeerd voor wereld wijde geografische tolerantie.
* Biedt u een optie om implementatie kosten en total cost of ownership voor het instellen van DR te verlagen.

Voor de CloudSimple-oplossing moet u het volgende doen:

* Installeer, Configureer en beheer vSphere-replicatie en-beveiligings-en hand-out in uw Privécloud.
* Geef uw eigen licenties voor de beveiligings-en-website op als de Privécloud de beveiligde site is. U hebt geen extra-CloudSimple-licenties nodig voor de site van de website die als herstel site wordt gebruikt.

Met deze oplossing hebt u volledige controle over vSphere-replicatie en-de-veel-------- De vertrouwde interface-, API-en CLI-interfaces maken gebruik van uw bestaande scripts en hulpprogram ma's mogelijk.

![Site Recovery Manager-implementatie](media/srm-deployment.png)

U kunt alle versies van vRA en een-runtime-versie gebruiken die compatibel zijn met uw persoonlijke Cloud-en on-premises omgevingen. In de voor beelden in deze hand leiding wordt gebruikgemaakt van vRA 6,5 en 6,5. Deze versies zijn compatibel met vSphere 6,5, die wordt ondersteund door CloudSimple.

## <a name="deploy-the-solution"></a>De oplossing implementeren

In de volgende secties wordt beschreven hoe u een DR-oplossing implementeert in uw Privécloud.

1. [Controleren of de VMware-product versies compatibel zijn](#verify-that-vmware-product-versions-are-compatible)
2. [De grootte van uw DR-omgeving schatten](#estimate-the-size-of-your-dr-environment)
3. [Een Privécloud maken voor uw omgeving](#create-a-private-cloud-for-your-environment)
4. [Particuliere Cloud netwerken instellen voor de oplossing van de beveiligings-en hand-out](#set-up-private-cloud-networking-for-the-srm-solution)
5. [Stel een site-naar-site-VPN-verbinding in tussen uw on-premises netwerk en de Privécloud en open de vereiste poorten.](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [Infrastructuur services in uw Privécloud instellen](#set-up-infrastructure-services-in-your-private-cloud)
7. [VSphere-replicatie apparaat installeren in uw on-premises omgeving](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [VSphere-replicatie apparaat installeren in uw Privécloud](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [Een server van het (en) installeren in uw on-premises omgeving](#install-srm-server-in-your-on-premises-environment)
10. [Een server van de beveiligings-of de-Cloud installeren](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Controleren of de VMware-product versies compatibel zijn

De configuraties in deze hand leiding zijn onderhevig aan de volgende compatibiliteits vereisten:

* U moet dezelfde versie van de beveiligings-en/of-versies implementeren in uw Privécloud en uw on-premises omgeving.
* Dezelfde versie van vSphere-replicatie moet in uw Privécloud en uw on-premises omgeving worden geïmplementeerd.
* De versies van de platform Services-controller (PSC) in uw Privécloud en uw on-premises omgeving moeten compatibel zijn.
* De versies van vCenter in uw Privécloud en uw on-premises omgeving moeten compatibel zijn.
* De versies van de vSphere-versie en-replicatie moeten compatibel zijn met elkaar en met de versies van PSC en vCenter.

Ga naar de documentatie van [vmware site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html) voor koppelingen naar relevante informatie over VMware-documentatie en-compatibiliteit.

Open de CloudSimple-Portal om de versies van vCenter en PSC in uw Privécloud te vinden. Ga naar **resources**, selecteer uw privécloud en klik op het tabblad **VSphere-beheer netwerk** .

![een vCenter-versie & PSC-versies in een Privécloud](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>De grootte van uw DR-omgeving schatten

1. Controleer of de geïdentificeerde on-premises configuratie binnen de ondersteunde limieten valt. De limieten voor de 6,5-eenheid worden beschreven in het Knowledge Base-artikel van VMware over de [operationele limieten voor site Recovery Manager 6,5](https://kb.vmware.com/s/article/2147110).
2. Zorg ervoor dat u voldoende netwerk bandbreedte hebt om te voldoen aan de grootte van de workload en de RPO-vereisten. Het VMware Knowledge Base-artikel over het berekenen van de [bandbreedte vereisten voor vSphere-replicatie](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) biedt richt lijnen voor bandbreedte limieten.
3. Gebruik het CloudSimple-grootte programma om de resources die nodig zijn op uw DR-site te ramen om uw on-premises omgeving te beveiligen.

### <a name="create-a-private-cloud-for-your-environment"></a>Een Privécloud maken voor uw omgeving

Maak een Privécloud vanuit de CloudSimple-portal door de instructies te volgen en aanbevelingen in te [stellen in een Privécloud maken](create-private-cloud.md).

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>Particuliere Cloud netwerken instellen voor de oplossing van de beveiligings-en hand-out

Open de CloudSimple-Portal om particuliere Cloud netwerken in te stellen voor de oplossing van de beveiligings-en hand-out.

Maak een VLAN voor het oplossings netwerk van de netwerkhardware en wijs hieraan een-CIDR toe. Zie [vlan's en subnetten maken en beheren](create-vlan-subnet.md)voor instructies.

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>Stel een site-naar-site-VPN-verbinding in tussen uw on-premises netwerk en de Privécloud en open de vereiste poorten.

Voor het instellen van een site-naar-site-verbinding tussen uw on-premises netwerk en uw Privécloud. Zie [een VPN-verbinding configureren voor de privécloud van CloudSimple](set-up-vpn.md)voor instructies.

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>Infrastructuur services in uw Privécloud instellen

Configureer infrastructuur services in de Privécloud om eenvoudig uw workloads en hulpprogram ma's te beheren.

U kunt een externe ID-provider toevoegen, zoals wordt beschreven in [Azure AD als id-provider voor vCenter op CloudSimple Private Cloud gebruiken](azure-ad.md) als u een van de volgende handelingen wilt uitvoeren:

* Gebruikers identificeren van uw on-premises Active Directory (AD) in uw Privécloud.
* Stel in uw Privécloud een AD in voor alle gebruikers.
* Gebruik Azure AD.

Als u IP-adressen wilt zoeken, IP-adres beheer en naamomzettingsservices voor uw workloads in de Privécloud, stelt u een DHCP-en DNS-server in, zoals wordt beschreven in [DNS-en DHCP-toepassingen en werk belastingen instellen in de privécloud van CloudSimple](dns-dhcp-setup.md).

Het domein *. cloudsimple.io wordt gebruikt door Management-Vm's en-hosts in uw Privécloud. Als u aanvragen voor dit domein wilt omzetten, configureert u het door sturen van DNS op de DNS-server zoals beschreven in [een voorwaardelijke forwarder maken](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>VSphere-replicatie apparaat installeren in uw on-premises omgeving

Installeer vSphere Replication Appliance (vRA) in uw on-premises omgeving door de VMware-documentatie te volgen. De installatie bestaat uit de volgende stappen op hoog niveau:

1. Bereid uw on-premises omgeving voor op de installatie van vRA.

2. Implementeer vRA in uw on-premises omgeving met behulp van de OVF in de VR-ISO van vmware.com. Voor vRA 6,5 bevat [deze VMware-blog](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) de relevante informatie.

3. Registreer uw on-premises vRA met vCenter single Sign-On op de on-premises site. Zie voor gedetailleerde instructies voor vSphere-replicatie 6,5 het VMware-document [VMware vSphere replicatie-6,5-installatie en-configuratie](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>VSphere-replicatie apparaat installeren in uw Privécloud

Controleer voordat u begint of u over het volgende beschikt:

* IP-bereik baarheid van subnetten in uw on-premises omgeving naar het beheer subnet van uw Privécloud
* IP-bereik baarheid van replicatie subnet in uw on-premises vSphere-omgeving naar het subsysteem van de oplossing voor de beveiligings-en hand

Zie [een VPN-verbinding configureren voor de privécloud van CloudSimple](set-up-vpn.md)voor instructies. De stappen zijn vergelijkbaar met die voor de on-premises installatie.

CloudSimple raadt u aan FQDN-namen te gebruiken in plaats van IP-adressen tijdens de installatie van vRA en. Open de CloudSimple-Portal om de FQDN-namen van vCenter en PSC in uw Privécloud te achterhalen. Ga naar **resources**, selecteer uw privécloud en klik op het tabblad **VSphere-beheer netwerk** .

![FQDN-namen van vCenter/PSC in de Privécloud zoeken](media/srm-resources.png)

CloudSimple vereist dat u vRA en niet installeert met de standaard gebruiker ' cloudowner ', maar in plaats daarvan een nieuwe gebruiker te maken. Dit wordt gedaan om te zorgen voor een hoge uptime en beschik baarheid voor uw Privécloud. De standaard cloudowner-gebruiker in de Privécloud does't heeft echter voldoende bevoegdheden om een nieuwe gebruiker met beheerders bevoegdheden te maken.

Voordat u vRA en een hoeveelheid van de-cloudowner installeert, moet u de vCenter-bevoegdheden van de gebruiker van de gebruikers beheerder escaleren en vervolgens een gebruiker met beheerders bevoegdheden maken in het vCenter-SSO-domein. Zie voor meer informatie over het standaard model gebruiker en machtiging van de privécloud [het machtigings model privécloud](learn-private-cloud-permissions.md).

De installatie bestaat uit de volgende stappen op hoog niveau:

1. [Bevoegdheden escaleren](escalate-private-cloud-privileges.md).
2. Maak een gebruiker in uw Privécloud voor vSphere-replicatie en-beveiligings-en-installatie. Hieronder wordt uitgelegd in de [vCenter-gebruikers interface: Maak een gebruiker in de privécloud voor vRA &-beveiligings-installatie](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation).
3. Bereid uw persoonlijke cloud omgeving voor op de installatie van vRA.
4. Implementeer vRA in uw Privécloud met behulp van de OVF in de VR. ISO van vmware.com. Voor vRA 6,5 bevat [deze VMware-blog](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) relevante informatie.
5. Firewall regels configureren voor vRA. Hieronder beschreven in [CloudSimple portal: Firewall regels configureren voor vRA](#cloudsimple-portal-configure-firewall-rules-for-vra).
6. Registreer vRA Sign-On voor de privécloud op de Privécloud-site.
7. Configureer vSphere-replicatie verbindingen tussen de twee apparaten. Zorg ervoor dat de vereiste poorten zijn geopend via de firewalls. Zie [dit artikel in de VMware Knowledge Base](https://kb.vmware.com/s/article/2087769) voor een lijst met poort nummers die open moeten zijn voor VSphere-replicatie 6,5.

Zie voor gedetailleerde installatie-instructies voor vSphere-replicatie 6,5 het VMware-document [VMware vSphere replicatie-6,5-installatie en-configuratie](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>vCenter-gebruikers interface: een gebruiker maken in een Privécloud voor vRA en de beveiligings-en hand-out

Meld u aan bij vCenter met cloudowner-gebruikers referenties na het escaleren van bevoegdheden van de CloudSimple-Portal.

Maak een nieuwe gebruiker `srm-soln-admin` in vCenter en voeg deze toe aan de groep Administrators in vCenter.
Meld u af bij vCenter als de cloudowner-gebruiker en meld u aan als de *soln-beheerder-* gebruiker.

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>CloudSimple-portal: Firewall regels configureren voor vRA

Configureer firewall regels zoals beschreven in [firewall tabellen en-regels instellen](firewall.md) voor het openen van poorten om communicatie mogelijk te maken tussen:

* vRA in de oplossing netwerk-en vCenter-en ESXi-hosts in het management-netwerk.
* vRA-apparaten op de twee sites.

Zie dit [artikel in de VMware Knowledge Base](https://kb.vmware.com/s/article/2087769) voor een lijst met poort nummers die open moeten zijn voor VSphere-replicatie 6,5.

### <a name="install-srm-server-in-your-on-premises-environment"></a>Een server van het (en) installeren in uw on-premises omgeving

Voordat u begint, controleert u het volgende:

* het vSphere-replicatie apparaat is geïnstalleerd in uw on-premises en particuliere cloud omgevingen.
* De vSphere-replicatie apparaten op beide sites zijn met elkaar verbonden.
* U hebt de VMware-informatie beoordeeld op vereisten en aanbevolen procedures. Voor de 6,5 van uw document kunt u verwijzen naar de vereisten voor VMware-documenten [en best practices voor de oplossings-6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

Volg de VMware-documentatie voor het uitvoeren van de installatie van een server met twee sites in het implementatie model met één vCenter-exemplaar per platform Services-controller, zoals beschreven in dit [VMware-document](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). De installatie-instructies voor de 6,5 is beschikbaar in het VMware-document [installatie van site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

### <a name="install-srm-server-in-your-private-cloud"></a>Een server van de beveiligings-of de-Cloud installeren

Voordat u begint, controleert u het volgende:

* het vSphere-replicatie apparaat is geïnstalleerd in uw on-premises en particuliere cloud omgevingen.
* De vSphere-replicatie apparaten op beide sites zijn met elkaar verbonden.
* U hebt de VMware-informatie beoordeeld op vereisten en aanbevolen procedures. U kunt voor de 6,5-versie van de [site Recovery Manager 6,5 server verwijzen naar vereisten en aanbevolen procedures](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

De volgende stappen beschrijven de Private-Cloud-beveiligings-installatie.

1. [vCenter-gebruikers interface: de installatie van de.](#vcenter-ui-install-srm)
2. [CloudSimple-portal: Firewall regels configureren voor de](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [vCenter UI: Configure](#vcenter-ui-configure-srm)
4. [CloudSimple-portal: de bevoegdheden voor deescaleren](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>vCenter-gebruikers interface: de installatie van de.

Nadat u zich hebt aangemeld bij vCenter met de soln-beheerders referenties, volgt u de VMware-documentatie voor het uitvoeren van de server installatie in het implementatie model ' twee-site topologie met één vCenter-exemplaar per platform Services-controller ', zoals beschreven in dit [VMware-document](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). De installatie-instructies voor de 6,5 is beschikbaar in het VMware-document [installatie van site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>CloudSimple-portal: Firewall regels configureren voor de

Configureer firewall regels zoals beschreven in [firewall tabellen en-regels instellen](firewall.md) om communicatie toe te staan tussen:

De server met de beveiligings-en de-en/of de in de privécloud.
De server-en-tijd servers op beide sites

Zie [dit artikel in de VMware Knowledge Base](https://kb.vmware.com/s/article/2087769) voor een lijst met poort nummers die open moeten zijn voor VSphere-replicatie 6,5.

#### <a name="vcenter-ui-configure-srm"></a>vCenter UI: Configure

Na de installatie van de beveiligings-of de de privécloud, voert u de volgende taken uit, zoals beschreven in de secties van de installatie-en configuratie handleiding voor VMware Site Recovery Manager. Voor de 6,5. de instructies zijn beschikbaar in het VMware-document [installatie van site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

1. Verbind de Site Recovery Manager server-exemplaren op de beveiligde en herstel sites.
2. Stel een client verbinding in met het externe Site Recovery Manager-Server exemplaar.
3. Installeer de licentie sleutel voor Site Recovery Manager.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple-portal: de bevoegdheden voor deescaleren

Als u de bevoegdheden wilt deescaleren, raadpleegt u [bevoegdheden deescaleren](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="ongoing-management-of-your-srm-solution"></a>Doorlopend beheer van uw oplossing

U hebt volledige controle over de vSphere-replicatie en de software-levens duur in uw Privécloud en u wordt verwacht het benodigde beheer van de software levenscyclus uit te voeren. Zorg ervoor dat er een nieuwe versie van de software compatibel is met de Privécloud en PSC voor Clouds voordat u de vSphere-replicatie of-beveiligings-of-upgrade uitvoert of bijwerkt.

> [!NOTE]
> CloudSimple is momenteel bezig met het verkennen van opties voor het aanbieden van een beheerde DR-service. 

## <a name="multiple-replication-configuration"></a>Configuratie van meerdere replicatie

 [Replicatie-en vSphere-replicatie technologieën kunnen zowel op de matrix als](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication) op dezelfde tijd worden gebruikt. Ze moeten echter worden toegepast op een afzonderlijke set virtuele machines (een bepaalde virtuele machine kan worden beveiligd door replicatie op basis van een matrix of vSphere, maar niet voor beide). Bovendien kan de CloudSimple-site worden geconfigureerd als een herstel site voor meerdere beveiligde sites. Zie de opties voor de meerdere locaties van de [website](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/) voor meer informatie over configuraties voor meerdere locaties.

## <a name="references"></a>Referenties

* [Documentatie voor VMware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Operationele limieten voor Site Recovery Manager 6,5](https://kb.vmware.com/s/article/2147110)
* [Bandbreedte vereisten voor vSphere-replicatie berekenen](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [OVF choices bij het implementeren van vSphere-replicatie 6,5](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere replicatie 6,5-installatie en-configuratie](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [Vereisten en aanbevolen procedures voor de 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Site Recovery manager in een Two-Site topologie met één vCenter Server Instance per platform Services-controller](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [Installatie-en configuratie handleiding voor VMware Site Recovery Manager 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [VMware-blog op de vSphere met replicatie op basis van een matrix versus replicatie van de](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [VMware-blog op een laag met meerdere locaties-opties](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [Poort nummers die open moeten zijn voor vSphere-replicatie 5,8. x, 6. x en 8](https://kb.vmware.com/s/article/2147112)
