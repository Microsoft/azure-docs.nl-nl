---
title: De Azure Linux-agent bijwerken vanuit GitHub
description: Meer informatie over het bijwerken van de Azure Linux-agent voor uw virtuele Linux-machine in azure
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 08/02/2017
ms.openlocfilehash: a274435d6e0fc32bdf5b2ab04702ed971d7c5175
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558981"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>De Azure Linux-agent op een virtuele machine bijwerken

Als u uw [Azure Linux-agent](https://github.com/Azure/WALinuxAgent) op een virtuele Linux-machine in azure wilt bijwerken, moet u al beschikken over:

- Een actieve Linux-VM in Azure.
- Een verbinding met die virtuele Linux-machine met behulp van SSH.

U moet altijd controleren op een pakket in de Linux distributie-opslag plaats. Het is mogelijk dat het beschik bare pakket niet de meest recente versie is, maar het inschakelen van auto update zorgt ervoor dat de Linux-agent altijd de meest recente update krijgt. Als u problemen hebt met de installatie van de pakket beheerders, moet u de distributie-leverancier ondersteunen.

> [!NOTE]
> Zie [goedgekeurde Linux-distributies op Azure](../linux/endorsed-distros.md) voor meer informatie

Controleer de [minimale versie ondersteuning voor virtuele-machine agenten in azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) voordat u doorgaat.


## <a name="ubuntu"></a>Ubuntu

De huidige pakket versie controleren

```bash
apt list --installed | grep walinuxagent
```

Pakket cache bijwerken

```bash
sudo apt-get -qq update
```

Installeer de meest recente pakket versie

```bash
sudo apt-get install walinuxagent
```

Zorg ervoor dat automatische updates is ingeschakeld. Controleer eerst of deze is ingeschakeld:

```bash
cat /etc/waagent.conf
```

Zoek auto update. enabled. Als u deze uitvoer ziet, wordt deze ingeschakeld:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Uitvoeren inschakelen:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

De waagengt-service opnieuw starten voor 14,04

```bash
initctl restart walinuxagent
```

Waagent-service opnieuw starten voor 16,04/17,04

```bash
systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Red Hat/CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

De huidige pakket versie controleren

```bash
sudo yum list WALinuxAgent
```

Beschik bare updates controleren

```bash
sudo yum check-update WALinuxAgent
```

Installeer de meest recente pakket versie

```bash
sudo yum install WALinuxAgent
```

Controleren of automatisch bijwerken is ingeschakeld 

Controleer eerst of deze is ingeschakeld:

```bash
cat /etc/waagent.conf
```

Zoek auto update. enabled. Als u deze uitvoer ziet, wordt deze ingeschakeld:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Uitvoeren inschakelen:

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

De waagent-service opnieuw starten

```
sudo service waagent restart
```

## <a name="rhelcentos-7"></a>RHEL/CentOS 7

De huidige pakket versie controleren

```bash
sudo yum list WALinuxAgent
```

Beschik bare updates controleren

```bash
sudo yum check-update WALinuxAgent
```

Installeer de meest recente pakket versie

```bash
sudo yum install WALinuxAgent  
```

Zorg ervoor dat automatische updates is ingeschakeld. Controleer eerst of deze is ingeschakeld:

```bash
cat /etc/waagent.conf
```

Zoek auto update. enabled. Als u deze uitvoer ziet, wordt deze ingeschakeld:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Uitvoeren inschakelen:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

De waagent-service opnieuw starten

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

De huidige pakket versie controleren

```bash
zypper info python-azure-agent
```

Controleer de beschik bare updates. In de bovenstaande uitvoer wordt weer gegeven of het pakket up-to-date is.

Installeer de meest recente pakket versie

```bash
sudo zypper install python-azure-agent
```

Controleren of automatisch bijwerken is ingeschakeld 

Controleer eerst of deze is ingeschakeld:

```bash
cat /etc/waagent.conf
```

Zoek auto update. enabled. Als u deze uitvoer ziet, wordt deze ingeschakeld:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Uitvoeren inschakelen:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

De waagent-service opnieuw starten

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

De huidige pakket versie controleren

```bash
zypper info python-azure-agent
```

Beschik bare updates controleren

In de uitvoer van de bovenstaande wordt weer gegeven of het pakket up-to-date is.

Installeer de meest recente pakket versie

```bash
sudo zypper install python-azure-agent
```

Controleren of automatisch bijwerken is ingeschakeld 

Controleer eerst of deze is ingeschakeld:

```bash
cat /etc/waagent.conf
```

Zoek auto update. enabled. Als u deze uitvoer ziet, wordt deze ingeschakeld:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Uitvoeren inschakelen:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

De waagent-service opnieuw starten

```bash
sudo systemctl restart waagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-jesse-debian-7-stretch"></a>Debian 7 "Jesse"/Debian 7 "stretch"

De huidige pakket versie controleren

```bash
dpkg -l | grep waagent
```

Pakket cache bijwerken

```bash
sudo apt-get -qq update
```

Installeer de meest recente pakket versie

```bash
sudo apt-get install waagent
```

Automatische updates van de agent inschakelen deze versie van Debian heeft geen versie >= 2.0.16, daarom is auto update niet beschikbaar voor IT. Met de uitvoer van de bovenstaande opdracht wordt weer gegeven of het pakket up-to-date is.

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Jessie"/Debian 9 "stretch"

De huidige pakket versie controleren

```bash
apt list --installed | grep waagent
```

Pakket cache bijwerken

```bash
sudo apt-get -qq update
```

Installeer de meest recente pakket versie

```bash
sudo apt-get install waagent
```

Zorg ervoor dat automatisch bijwerken is ingeschakeld en controleer of dit is ingeschakeld:

```bash
cat /etc/waagent.conf
```

Zoek auto update. enabled. Als u deze uitvoer ziet, wordt deze ingeschakeld:

```bash
AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Uitvoeren inschakelen:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
Restart the waagent service
sudo systemctl restart walinuxagent.service
```

## <a name="oracle-linux-6-and-oracle-linux-7"></a>Oracle Linux 6 en Oracle Linux 7

Zorg ervoor dat de `Addons` opslag plaats is ingeschakeld voor Oracle Linux. Kies voor het bewerken van het bestand `/etc/yum.repos.d/public-yum-ol6.repo` (Oracle Linux 6) of `/etc/yum.repos.d/public-yum-ol7.repo` (Oracle Linux) en wijzig de regel in `enabled=0` `enabled=1` onder **[ol6_addons]** of **[ol7_addons]** in dit bestand.

Als u de nieuwste versie van de Azure Linux-agent wilt installeren, typt u:

```bash
sudo yum install WALinuxAgent
```

Als u de opslag plaats voor invoeg toepassingen niet vindt, kunt u deze regels aan het einde van het. opslag plaats-bestand toevoegen volgens uw Oracle Linux-release:

Voor virtuele machines van Oracle Linux 6:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=https://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=https://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Voor virtuele machines van Oracle Linux 7:

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=1
```

Typ vervolgens:

```bash
sudo yum update WALinuxAgent
```

Normaal gesp roken hoeft u alleen maar de volgende stappen uit te voeren als u deze om een bepaalde reden rechtstreeks wilt installeren https://github.com .


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>De Linux-agent bijwerken wanneer er geen agent pakket bestaat voor distributie

Wget installeren (er zijn enkele distributies die niet standaard worden geïnstalleerd, zoals Red Hat, CentOS en Oracle Linux versie 6,4 en 6,5) door te typen `sudo yum install wget` op de opdracht regel.

### <a name="1-download-the-latest-version"></a>1. down load de nieuwste versie
Open [de release van de Azure Linux-agent in github](https://github.com/Azure/WALinuxAgent/releases) op een webpagina en ontdek het meest recente versie nummer. (U kunt uw huidige versie vinden door te typen `waagent --version` .)

Voor versie 2.2. x of hoger typt u:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

In de volgende regel wordt versie 2.2.0 als voor beeld gebruikt:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. de Azure Linux-agent installeren

Voor versie 2.2. x gebruikt u het volgende: u moet het pakket mogelijk `setuptools` eerst installeren. Zie [hier](https://pypi.python.org/pypi/setuptools)voor meer informatie. Voer vervolgens

```bash
sudo python setup.py install
```

Zorg ervoor dat automatische updates is ingeschakeld. Controleer eerst of deze is ingeschakeld:

```bash
cat /etc/waagent.conf
```

Zoek auto update. enabled. Als u deze uitvoer ziet, wordt deze ingeschakeld:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Uitvoeren inschakelen:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Start de waagent-service opnieuw
Voor de meeste Linux-distributies:

```bash
sudo service waagent restart
```

Gebruik voor Ubuntu:

```bash
sudo service walinuxagent restart
```

Gebruik voor CoreOS:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Controleer de versie van de Azure Linux-agent
    
```bash
waagent -version
```

De bovenstaande opdracht werkt mogelijk niet voor CoreOS.

U zult zien dat de versie van de Azure Linux-agent is bijgewerkt naar de nieuwe versie.

Zie het [Leesmij-bestand voor Azure Linux-agent](https://github.com/Azure/WALinuxAgent)voor meer informatie over de Azure Linux-agent.
