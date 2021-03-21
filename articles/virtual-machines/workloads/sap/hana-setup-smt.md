---
title: Een SMT-server instellen voor SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: SMT-server instellen voor SAP HANA op Azure (grote exemplaren).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 990274f34dac0234c2c2d521ca6a260f999671bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674515"
---
# <a name="set-up-smt-server-for-suse-linux"></a>Een SMT-server instellen voor SUSE Linux
Grote instanties van SAP HANA hebben geen directe verbinding met internet. Het is geen eenvoudig proces om een dergelijke eenheid te registreren bij de provider van het besturings systeem en om updates te downloaden en toe te passen. Een oplossing voor SUSE Linux is het instellen van een SMT-server in een virtuele machine van Azure. Host de virtuele machine in een virtueel Azure-netwerk dat is verbonden met de HANA grote instantie. Met een dergelijke SMT-server kan de HANA-eenheid voor grote instanties updates registreren en downloaden. 

Voor meer documentatie over SUSE raadpleegt u het [hulp programma voor abonnements beheer voor SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

De vereisten voor het installeren van een SMT-server die voldoet aan de taak voor HANA grote instanties zijn:

- Een virtueel Azure-netwerk dat is verbonden met het ExpressRoute-circuit van de HANA-grote instantie.
- Een SUSE-account dat is gekoppeld aan een organisatie. De organisatie moet een geldig SUSE-abonnement hebben.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>Een SMT-server op een virtuele Azure-machine installeren

Meld u eerst aan bij het [SuSE-klanten centrum](https://scc.suse.com/).

Ga naar de referenties van de **organisatie**  >  . In deze sectie vindt u de referenties die nodig zijn voor het instellen van de SMT-server.

Installeer vervolgens een SUSE Linux-VM in het virtuele Azure-netwerk. Als u de virtuele machine wilt implementeren, maakt u een SLES 12 SP2 galerie-afbeelding van Azure (Selecteer BYOS SUSE-afbeelding). In het implementatie proces definieert u geen DNS-naam en gebruikt u geen statische IP-adressen.

![Scherm afbeelding van de implementatie van de virtuele machine voor een SMT-server](./media/hana-installation/image3_vm_deployment.png)

De geïmplementeerde virtuele machine is kleiner en het interne IP-adres in het virtuele Azure-netwerk van 10.34.1.4. De naam van de virtuele machine is *smtserver*. Na de installatie wordt de verbinding met de HANA-eenheid voor grote instanties of eenheden gecontroleerd. Afhankelijk van hoe u de naam omzetting hebt georganiseerd, moet u mogelijk de resolutie van de HANA grote instantie-eenheden in etc/hosts van de virtuele machine van Azure configureren. 

Voeg een schijf toe aan de virtuele machine. U gebruikt deze schijf om de updates te bewaren en de opstart schijf zelf kan te klein zijn. Hier is de schijf gekoppeld aan/srv/www/htdocs, zoals weer gegeven in de volgende scherm afbeelding. Een schijf van 100 GB moet voldoende zijn.

![Scherm afbeelding toont de toegevoegde schijf in het PuTTy-venster.](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Meld u aan bij de HANA grote instantie-eenheid of-eenheden, onderhoud bestand/etc/hosts en controleer of u de virtuele machine van Azure kunt bereiken waarop de SMT-server moet worden uitgevoerd via het netwerk.

Nadat u deze controle hebt uitgevoerd, meldt u zich aan bij de virtuele machine van Azure die de SMT-server moet uitvoeren. Als u putty gebruikt om u aan te melden bij de virtuele machine, voert u deze reeks opdrachten uit in uw bash-venster:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Start de bash opnieuw op om de instellingen te activeren. Start vervolgens YAST.

Verbind uw VM (smtserver) met de SUSE-site.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

Nadat de virtuele machine is verbonden met de SUSE-site, installeert u de SMT-pakketten. Gebruik de volgende putty-opdracht om de SMT-pakketten te installeren.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


U kunt ook het YAST-hulp programma gebruiken om de SMT-pakketten te installeren. Ga in YAST naar **software onderhoud** en zoek naar SMT. Selecteer **SMT**, waarmee automatisch wordt overgeschakeld naar YaST2-SMT.

![Scherm opname van SMT in YAST](./media/hana-installation/image5_smt_in_yast.PNG)


Accepteer de selectie voor installatie op de smtserver. Nadat de installatie is voltooid, gaat u naar de configuratie van de SMT-server. Voer de referenties van de organisatie in vanuit het SUSE-klanten centrum dat u eerder hebt opgehaald. Voer ook de hostnaam van uw virtuele Azure-machine in als de URL van de SMT-server. In deze demonstratie is het https: \/ /smtserver.

![Scherm afbeelding van de configuratie van een SMT-server](./media/hana-installation/image6_configuration_of_smtserver1.png)

Test nu of de verbinding met het SUSE-klanten centrum werkt. Zoals u ziet in de volgende scherm afbeelding, in dit voor beeld is het werk.

![Scherm opname van het testen van de verbinding naar het SUSE-klanten centrum](./media/hana-installation/image7_test_connect.png)

Nadat de SMT-installatie is gestart, geeft u een database wachtwoord op. Omdat het een nieuwe installatie is, moet u het wacht woord definiëren, zoals wordt weer gegeven in de volgende scherm afbeelding.

![Scherm afbeelding van het definiëren van een wacht woord voor de data base](./media/hana-installation/image8_define_db_passwd.PNG)

De volgende stap is het maken van een certificaat.

![Scherm afbeelding van het maken van een certificaat voor een SMT-server](./media/hana-installation/image9_certificate_creation.PNG)

Aan het einde van de configuratie kan het enkele minuten duren om de synchronisatie controle uit te voeren. Na de installatie en configuratie van de SMT-server, moet u de Directory opslag plaats vinden onder het koppel punt/srv/www/htdocs/. Er zijn ook enkele submappen onder opslag plaats. 

Start de SMT-server en de bijbehorende services opnieuw met deze opdrachten.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>Pakketten downloaden naar een SMT-server

Nadat alle services opnieuw zijn opgestart, selecteert u de juiste pakketten in SMT-beheer met behulp van YAST. De selectie van het pakket is afhankelijk van de installatie kopie van het besturings systeem van de HANA-server voor grote instanties. De selectie van het pakket is niet afhankelijk van de SLES-release of-versie van de virtuele machine waarop de SMT-server wordt uitgevoerd. De volgende scherm afbeelding toont een voor beeld van het selectie scherm.

![Scherm opname van het selecteren van pakketten](./media/hana-installation/image10_select_packages.PNG)

Vervolgens start u de eerste kopie van de Select-pakketten naar de SMT-server die u hebt ingesteld. Deze kopie wordt geactiveerd in de shell met behulp van de opdracht SMT-mirror.

![Scherm opname van het downloaden van pakketten naar een SMT-server](./media/hana-installation/image11_download_packages.PNG)

De pakketten moeten worden gekopieerd naar de mappen die zijn gemaakt onder het koppel punt/srv/www/htdocs. Dit proces kan een uur of langer duren, afhankelijk van het aantal pakketten dat u selecteert. Wanneer dit proces is voltooid, gaat u naar de installatie van de SMT-client. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>De SMT-client instellen op HANA grote instantie-eenheden

De client of clients in dit geval zijn de HANA grote instantie-eenheden. Het installatie programma van de SMT-server heeft het script clientSetup4SMT.sh gekopieerd naar de virtuele machine van Azure. Kopieer dat script naar de HANA-eenheid voor grote instanties die u wilt verbinden met de server van het SMT-apparaat. Start het script met de-h-optie en geef de naam van uw SMT-server op als para meter. In dit voor beeld is de naam *smtserver*.

![Scherm opname van het configureren van de SMT-client](./media/hana-installation/image12_configure_client.PNG)

Het is mogelijk dat het certificaat van de server door de client wordt geladen, maar dat de registratie mislukt, zoals wordt weer gegeven in de volgende scherm afbeelding.

![Scherm opname van client registratie fout](./media/hana-installation/image13_registration_failed.PNG)

Als de registratie mislukt, raadpleegt u het [SuSE-ondersteunings document](https://www.suse.com/de-de/support/kb/doc/?id=7006024)en voert u de stappen uit die hier worden beschreven.

> [!IMPORTANT] 
> Geef voor de server naam de naam van de virtuele machine (in dit geval *smtserver*) op zonder de Fully Qualified Domain name. 

Nadat u deze stappen hebt uitgevoerd, voert u de volgende opdracht uit op de HANA-eenheid voor grote instanties:

```
SUSEConnect –cleanup
```

> [!Note] 
> Wacht enkele minuten nadat deze stap is uitgevoerd. Als u clientSetup4SMT.sh onmiddellijk uitvoert, wordt er mogelijk een fout bericht weer geven.

Als u een probleem ondervindt dat u moet oplossen op basis van de stappen van het artikel SUSE, start u clientSetup4SMT.sh opnieuw op de HANA-eenheid voor grote instanties. Nu moet de service worden voltooid.

![Scherm opname van client registratie geslaagd](./media/hana-installation/image14_finish_client_config.PNG)

U hebt de SMT-client van de HANA-eenheid voor grote instanties geconfigureerd om verbinding te maken met de SMT-server die u hebt geïnstalleerd op de virtuele machine van Azure. U kunt nu ' Zypper up ' of ' Zypper in ' gebruiken om updates van het besturings systeem te installeren op HANA grote instanties of extra pakketten te installeren. U kunt alleen updates ophalen die u eerder hebt gedownload op de SMT-server.

## <a name="next-steps"></a>Volgende stappen
- [Hana-installatie op HLI](hana-example-installation.md).











