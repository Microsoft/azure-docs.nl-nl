---
title: Infra structuur voor Red Hat-updates | Microsoft Docs
description: Meer informatie over de infra structuur voor Red Hat-updates voor Red Hat Enterprise Linux exemplaren op aanvraag in Microsoft Azure
author: asinn826
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 02/10/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: d4af869a3bf4ba7f454ae8e5c9c9f4eb81f5939f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957449"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat update-infra structuur voor Red Hat Enterprise Linux Vm's op aanvraag in azure
 Met de [Red Hat Update infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) kunnen cloud providers, zoals Azure, gehoste inhoud van een opslag plaats met Red Hat worden gespiegeld, aangepaste opslag plaatsen met Azure-specifieke inhoud maken en deze beschikbaar maken voor virtuele machines van eind gebruikers.

Red Hat Enterprise Linux (RHEL) PAYG-installatie kopieën (betalen per gebruik) zijn vooraf geconfigureerd voor toegang tot Azure RHUI. Er is geen aanvullende configuratie nodig. Als u de meest recente updates wilt downloaden, voert u uit `sudo yum update` nadat uw RHEL-exemplaar klaar is. Deze service is opgenomen als onderdeel van de kosten voor de RHEL PAYG-software.

Meer informatie over RHEL-installatie kopieën in azure, met inbegrip van publicatie-en bewaar beleid, is [hier](./redhat-images.md)beschikbaar.

Informatie over Red Hat-ondersteunings beleid voor alle versies van RHEL vindt u op de pagina [levens cyclus van Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .

> [!IMPORTANT]
> RHUI is alleen bedoeld voor PAYG-installatie kopieën (betalen per gebruik). Voor aangepaste en industriële installatie kopieën, ook wel bekend als uw eigen abonnement (BYOS), moet het systeem aan RHSM of Satellite worden gekoppeld om updates te kunnen ontvangen. Zie het [artikel over Red Hat](https://access.redhat.com/solutions/253273) voor meer informatie.


## <a name="important-information-about-azure-rhui"></a>Belang rijke informatie over Azure RHUI

* Azure RHUI is de update-infra structuur die ondersteuning biedt voor alle RHEL PAYG-Vm's die zijn gemaakt in Azure. Dit verhindert u niet dat u uw PAYG RHEL-Vm's kunt registreren met abonnements Manager of satelliet of andere bron van updates, maar wel met een PAYG-VM, resulteert dit in indirect dubbel facturering. Zie het volgende punt voor meer informatie.
* Toegang tot de door Azure gehoste RHUI is inbegrepen bij de prijs van de RHEL PAYG-installatie kopie. Als u de registratie van een PAYG RHEL-VM ongedaan maakt vanuit de door Azure gehoste RHUI, wordt de virtuele machine niet geconverteerd naar een (BYOL) type VM. Als u dezelfde VM registreert bij een andere bron van updates, worden er mogelijk _indirecte_ dubbele kosten in rekening gebracht. De eerste keer dat u de kosten voor Azure RHEL-software betaalt. U betaalt de tweede keer voor Red Hat-abonnementen die eerder zijn gekocht. Als u een andere update-infra structuur dan een door Azure gehoste RHUI wilt gebruiken, kunt u overwegen om te registreren voor het gebruik van de [RHEL BYOS-installatie kopieën](./byos.md).

* RHEL SAP PAYG-installatie kopieën in azure (RHEL for SAP, RHEL for SAP HANA en RHEL for SAP Business Applications) zijn verbonden met toegewezen RHUI-kanalen die blijven gelden voor de specifieke RHEL-versie zoals vereist voor SAP-certificering.

* Toegang tot door Azure gehoste RHUI is beperkt tot de virtuele machines binnen de [IP-bereiken van het Azure-Data Center](https://www.microsoft.com/download/details.aspx?id=41653). Als u alle VM-verkeer via een on-premises netwerk infrastructuur wilt door geven, moet u mogelijk door de gebruiker gedefinieerde routes instellen voor de RHEL PAYG-Vm's om toegang te krijgen tot de Azure RHUI. Als dat het geval is, moeten door de gebruiker gedefinieerde routes worden toegevoegd voor _alle_ RHUI IP-adressen.


## <a name="image-update-behavior"></a>Gedrag van installatie kopie-updates

Met ingang van 2019 april biedt Azure RHEL installatie kopieën die zijn verbonden met EUS-opslag plaatsen (Extended update support) standaard en RHEL installatie kopieën die standaard worden aangesloten op de normale (niet-EUS) opslag plaatsen. Meer informatie over RHEL EUS vindt u in de documentatie van de [versie levenscyclus](https://access.redhat.com/support/policy/updates/errata) van Red Hat en de [Eus-documentatie](https://access.redhat.com/articles/rhel-eus). Het standaard gedrag van `sudo yum update` is afhankelijk van de RHEL-installatie kopie die u hebt ingericht, omdat verschillende installatie kopieën zijn verbonden met verschillende opslag plaatsen.

Voor een volledige lijst met installatie kopieën voert `az vm image list --publisher redhat --all` u uit met behulp van de Azure cli.

### <a name="images-connected-to-non-eus-repositories"></a>Afbeeldingen die zijn verbonden met niet-EUS-opslag plaatsen

Als u een virtuele machine inricht vanuit een RHEL-installatie kopie die is verbonden met niet-EUS-opslag plaatsen, wordt u bijgewerkt naar de meest recente RHEL-versie wanneer u uitvoert `sudo yum update` . Als u bijvoorbeeld een virtuele machine inricht vanuit een RHEL 7,4 PAYG-installatie kopie en uitvoert `sudo yum update` , hebt u een RHEL 7,8 VM (de meest recente secundaire versie in de RHEL7-serie).

Installatie kopieën die zijn verbonden met niet-EUS-opslag plaatsen, bevatten geen secundair versie nummer in de SKU. De SKU is het derde element in de URN (volledige naam van de afbeelding). Zo worden alle volgende installatie kopieën gekoppeld aan niet-EUS-opslag plaatsen:

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

Houd er rekening mee dat de Sku's 7-LVM of 7-RAW zijn. De secundaire versie wordt aangegeven in de versie (vierde element in de URN) van deze installatie kopieën.

### <a name="images-connected-to-eus-repositories"></a>Installatie kopieën die zijn verbonden met EUS-opslag plaatsen

Als u een virtuele machine inricht vanuit een RHEL-installatie kopie die is verbonden met EUS-opslag plaatsen, wordt u niet bijgewerkt naar de meest recente secundaire versie van RHEL wanneer u uitvoert `sudo yum update` . De installatie kopieën die zijn verbonden met EUS-opslag plaatsen, zijn ook versie-vergrendeld op hun specifieke secundaire versie.

Afbeeldingen die zijn verbonden met EUS-opslag plaatsen, bevatten een secundair versie nummer in de SKU. Zo worden alle volgende installatie kopieën gekoppeld aan EUS-opslag plaatsen:

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS en versie-vergren delen RHEL Vm's

EUS-opslag plaatsen (Extended update support) zijn beschikbaar voor klanten die hun RHEL-Vm's mogelijk willen vergren delen met een bepaalde RHEL-kleine release na het inrichten van de virtuele machine. U kunt uw RHEL-VM met een specifieke secundaire versie vergren delen door de opslag plaatsen bij te werken zodat deze verwijzen naar de ondersteunings opslagplaatsen voor uitgebreide updates. U kunt ook de vergrendelings bewerking van de EUS-versie ongedaan maken.

>[!NOTE]
> EUS wordt niet ondersteund voor RHEL-Extra's. Dit betekent dat als u een pakket installeert dat doorgaans beschikbaar is via het RHEL Extrass-kanaal, u dit niet kunt doen wanneer u op EUS. De product levenscyclus van Red Hat extras wordt [hier](https://access.redhat.com/support/policy/updates/extras/)beschreven.

Op het moment van deze schrijf bewerking is de EUS-ondersteuning voor RHEL <= 7,4 beëindigd. Zie de sectie ' Red Hat Enterprise Linux uitgebreid onderhoud ' in de [documentatie van Red Hat](https://access.redhat.com/support/policy/updates/errata/#Long_Support) voor meer informatie.
* RHEL 7,4 EUS-ondersteuning eindigt op 31 augustus 2019
* RHEL 7,5 EUS support eindigt op 30 april 2020
* RHEL 7,6 EUS-ondersteuning eindigt op 31 mei 2021
* RHEL 7,7 EUS-ondersteuning eindigt op 30 augustus 2021

### <a name="switch-a-rhel-vm-7x-to-eus-version-lock-to-a-specific-minor-version"></a>Overschakelen van een RHEL VM 7. x naar EUS (versie-vergren delen naar een specifieke secundaire versie)
Gebruik de volgende instructies om een RHEL 7. x-VM te vergren delen naar een bepaalde kleine release (uitvoeren als root):

>[!NOTE]
> Dit geldt alleen voor RHEL 7. x-versies waarvoor EUS beschikbaar is. Op het moment van deze schrijf bewerking omvat dit RHEL 7.2-7,7. Meer informatie vindt u op de pagina [Red Hat Enterprise Linux levens cyclus](https://access.redhat.com/support/policy/updates/errata) .

1. Niet-EUS-opslag plaatsen uitschakelen:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. EUS opslag plaatsen toevoegen:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. De variabele vergren delen `releasever` (als basis uitvoeren):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > Met de bovenstaande instructie wordt de RHEL-secundaire release vergrendeld op de huidige secundaire release. Voer een specifieke kleine release in als u een upgrade wilt uitvoeren en wilt vergren delen naar een latere secundaire versie die niet het meest recent is. `echo 7.5 > /etc/yum/vars/releasever`Uw RHEL-versie wordt bijvoorbeeld vergrendeld op RHEL 7,5.

1. Uw RHEL-VM bijwerken
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-8x-to-eus-version-lock-to-a-specific-minor-version"></a>Overschakelen van een RHEL VM 8. x naar EUS (versie-vergren delen naar een specifieke secundaire versie)
Gebruik de volgende instructies om een RHEL 8. x-VM te vergren delen met een bepaalde kleine release (uitvoeren als root):

>[!NOTE]
> Dit geldt alleen voor RHEL 8. x-versies waarvoor EUS beschikbaar is. Op het moment van deze schrijf bewerking omvat dit RHEL 8.1-8.2. Meer informatie vindt u op de pagina [Red Hat Enterprise Linux levens cyclus](https://access.redhat.com/support/policy/updates/errata) .

1. Niet-EUS-opslag plaatsen uitschakelen:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel8'
    ```

1. Het EUS opslag plaatsen-configuratie bestand ophalen:
    ```bash
    wget https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel8-eus.config
    ```

1. EUS opslag plaatsen toevoegen:
    ```bash
    yum --config=rhui-microsoft-azure-rhel8-eus.config install rhui-azure-rhel8-eus
    ```

1. De variabele vergren delen `releasever` (als basis uitvoeren):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > Met de bovenstaande instructie wordt de RHEL-secundaire release vergrendeld op de huidige secundaire release. Voer een specifieke kleine release in als u een upgrade wilt uitvoeren en wilt vergren delen naar een latere secundaire versie die niet het meest recent is. `echo 8.1 > /etc/yum/vars/releasever`Uw RHEL-versie wordt bijvoorbeeld vergrendeld op RHEL 8,1.

    >[!NOTE]
    > Als er machtigings problemen zijn voor toegang tot de releasever, kunt u het bestand bewerken met behulp van nano/etc/yum/Vars/releaseve en de installatie kopie versie Details toevoegen en opslaan (Ctrl + o en vervolgens op ENTER drukken en vervolgens op CTRL + x).  

1. Uw RHEL-VM bijwerken
    ```bash
    sudo yum update
    ```


### <a name="switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock"></a>Overschakelen van een RHEL 7. x-VM naar een niet-EUS (een versie vergrendeling verwijderen)
Voer het volgende uit als root:
1. Verwijder het `releasever` bestand:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. EUS opslag plaatsen uitschakelen:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. RHEL VM configureren
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```

1. Uw RHEL-VM bijwerken
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-8x-vm-back-to-non-eus-remove-a-version-lock"></a>Een RHEL 8. x-VM weer overschakelen naar een niet-EUS (een versie vergrendeling verwijderen)
Voer het volgende uit als root:
1. Verwijder het `releasever` bestand:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. EUS opslag plaatsen uitschakelen:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel8-eus'
   ```

1. Het normale opslag plaatsen-configuratie bestand ophalen:
    ```bash
    wget https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel8.config
    ```

1. EUS opslag plaatsen toevoegen:
    ```bash
    yum --config=rhui-microsoft-azure-rhel8.config install rhui-azure-rhel8
    ```
    
1. Uw RHEL-VM bijwerken
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>De IP-adressen voor de RHUI content delivery servers

RHUI is beschikbaar in alle regio's waar RHEL on-demand installatie kopieën beschikbaar zijn. Het bevat momenteel alle open bare regio's die worden vermeld op de [Azure-status dashboard](https://azure.microsoft.com/status/) pagina, Azure Amerikaanse overheid en Microsoft Azure Duitsland regio's.

Als u een netwerk configuratie gebruikt om de toegang van RHEL PAYG Vm's verder te beperken, moet u ervoor zorgen dat de volgende IP-adressen zijn toegestaan, `yum update` afhankelijk van de omgeving waarin u zich bevindt:


```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193
13.72.14.155
52.244.249.194

# Azure Germany
51.5.243.77
51.4.228.145
```
>[!NOTE]
>De nieuwe installatie kopieën van Azure US Government, vanaf januari 2020, maken gebruik van een openbaar IP-adres dat is vermeld onder Azure Global-header hierboven.

>[!NOTE]
>Houd er ook rekening mee dat Azure Duitsland is afgeschaft ten gunste van open bare Duitse regio's. Aanbeveling voor Azure Duitsland-klanten is om aan de slag te gaan met de open bare [RHUI.](#manual-update-procedure-to-use-the-azure-rhui-servers)

## <a name="azure-rhui-infrastructure"></a>Azure RHUI-infra structuur


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Verlopen RHUI-client certificaat op een VM bijwerken

Als u een oudere RHEL-VM-installatie kopie gebruikt, bijvoorbeeld RHEL 7,4 (installatie kopie URN: `RedHat:RHEL:7.4:7.4.2018010506` ), treden er connectiviteits problemen op RHUI vanwege een nu verlopen TLS/SSL-client certificaat. De fout die u ziet, kan er als volgt uitzien _: "SSL-peer heeft uw certificaat afgewezen als verlopen"_ of _"fout: kan de meta gegevens van de opslag plaats (repomd.xml) niet ophalen voor de opslag plaats:... Controleer het pad en probeer het opnieuw_. Als u dit probleem wilt verhelpen, moet u het RHUI-client pakket op de VM bijwerken met de volgende opdracht:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

U kunt `sudo yum update` ook het client certificaat pakket bijwerken (afhankelijk van uw RHEL-versie), ondanks dat er fouten met het ' verlopen SSL-certificaat ' worden weer geven voor andere opslag plaatsen. Als deze update is geslaagd, moet de normale connectiviteit met andere RHUI-opslag plaatsen worden hersteld, zodat u kunt worden uitgevoerd `sudo yum update` .

Als er een 404-fout optreedt tijdens het uitvoeren van een `yum update` , voert u de volgende handelingen uit om de yum-cache te vernieuwen:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Verbindings problemen met Azure RHUI oplossen
Als u problemen ondervindt bij het verbinding maken met Azure RHUI via uw Azure RHEL PAYG VM, volgt u deze stappen:

1. Inspecteer de VM-configuratie voor het Azure RHUI-eind punt:

    1. Controleer of het `/etc/yum.repos.d/rh-cloud.repo` bestand een verwijzing bevat naar `rhui-[1-3].microsoft.com` in de `baseurl` sectie van `[rhui-microsoft-azure-rhel*]` het bestand. Als dit het geval is, gebruikt u de nieuwe Azure-RHUI.

    1. Als deze verwijst naar een locatie met het volgende patroon, `mirrorlist.*cds[1-4].cloudapp.net` is een configuratie-update vereist. U gebruikt de oude moment opname van de virtuele machine en u moet deze bijwerken zodat deze verwijst naar de nieuwe Azure-RHUI.

1. Toegang tot door Azure gehoste RHUI is beperkt tot Vm's binnen de [IP-bereiken van het Azure-Data Center](https://www.microsoft.com/download/details.aspx?id=41653).

1. Als u de nieuwe configuratie gebruikt, hebt u gecontroleerd of de virtuele machine verbinding maakt vanuit het Azure IP-bereik en nog steeds geen verbinding kan maken met Azure RHUI, waarbij u een ondersteunings aanvraag kunt doen met micro soft of Red Hat.

### <a name="infrastructure-update"></a>Infrastructuur update

In september 2016 hebben we een bijgewerkte Azure-RHUI geïmplementeerd. In april 2017 worden de oude Azure-RHUI afgesloten. Als u de RHEL PAYG-installatie kopieën (of de bijbehorende moment opnamen) van september 2016 of hoger gebruikt, maakt u automatisch verbinding met de nieuwe Azure-RHUI. Als u echter oudere moment opnamen op uw Vm's hebt, moet u de configuratie van de virtuele machines hand matig bijwerken om toegang te krijgen tot de Azure-RHUI, zoals wordt beschreven in een van de volgende secties.

De nieuwe Azure RHUI-servers worden geïmplementeerd met [azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). In Traffic Manager kan één eind punt (rhui-1.microsoft.com) worden gebruikt door elke virtuele machine, ongeacht de regio.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Procedure voor hand matige updates voor het gebruik van de Azure RHUI-servers
Deze procedure wordt alleen ter informatie bedoeld. RHEL PAYG-installatie kopieën hebben al de juiste configuratie om verbinding te maken met Azure RHUI. Voer de volgende stappen uit om de configuratie hand matig bij te werken voor het gebruik van de Azure RHUI-servers:

- Voor RHEL 6:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- Voor RHEL 7:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

- Voor RHEL 8:
    1. Een configuratie bestand maken:
        ```bash
        vi rhel8.config
        ```
    1. Voeg de volgende inhoud toe aan het configuratie bestand:
        ```bash
        [rhui-microsoft-azure-rhel8]
        name=Microsoft Azure RPMs for Red Hat Enterprise Linux 8
        baseurl=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-2.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-3.microsoft.com/pulp/repos/microsoft-azure-rhel8
        enabled=1
        gpgcheck=1
        gpgkey=https://rhelimage.blob.core.windows.net/repositories/RPM-GPG-KEY-microsoft-azure-release sslverify=1
        ```
    1. Sla het bestand op en voer de volgende opdracht uit:
        ```bash
        dnf --config rhel8.config install 'rhui-azure-rhel8'
        ```
    1. Uw VM bijwerken
        ```bash
        sudo dnf update
        ```


## <a name="next-steps"></a>Volgende stappen
* Ga naar de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RHEL_6)om een Red Hat Enterprise Linux VM te maken op basis van een installatie kopie van Azure Marketplace payg en Azure-gehoste RHUI te gebruiken.
* Ga naar de [documentatie pagina](./redhat-images.md)voor meer informatie over de Red Hat-afbeeldingen in Azure.
* Informatie over Red Hat-ondersteunings beleid voor alle versies van RHEL vindt u op de pagina [levens cyclus van Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .
