---
title: HANA installeren op SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: HANA installeren op SAP HANA op Azure (grote exemplaren).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 994130ce2ecb4578a8d34f1f77b310bb558d3c7a
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967904"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>HANA installeren op SAP HANA op Azure (grote exemplaren)

Als u HANA wilt installeren op SAP HANA op Azure (grote exemplaren), moet u eerst het volgende doen:
- U geeft micro soft alle gegevens die u voor u wilt implementeren op een SAP HANA grote instantie.
- U ontvangt de SAP HANA grote instantie van micro soft.
- U maakt een virtueel Azure-netwerk dat is verbonden met uw on-premises netwerk.
- U verbindt het ExpressRoute-circuit voor HANA grote instanties naar hetzelfde virtuele Azure-netwerk.
- U installeert een virtuele Azure-machine die u als een Jump box voor HANA grote instanties gebruikt.
- U zorgt ervoor dat u verbinding kunt maken vanuit het Jump box naar uw HANA grote instantie-eenheid, en omgekeerd.
- U controleert of alle benodigde pakketten en patches zijn geïnstalleerd.
- U leest de SAP-opmerkingen en documentatie over HANA-installatie op het besturings systeem dat u gebruikt. Controleer of de HANA-versie van Choice wordt ondersteund op de release van het besturings systeem.

In de volgende sectie ziet u een voor beeld van het downloaden van de HANA-installatie pakketten naar de virtuele machine met het Jump box. In dit geval is het besturings systeem Windows.

## <a name="download-the-sap-hana-installation-bits"></a>De SAP HANA-installatie-bits downloaden
De HANA-eenheden voor grote instanties zijn niet rechtstreeks verbonden met internet. U kunt de installatie pakketten niet rechtstreeks downloaden van SAP naar de virtuele machine van het HANA-grote exemplaar. In plaats daarvan downloadt u de pakketten naar de virtuele machine met het Jump box.

U hebt een SAP S-gebruiker of een andere gebruiker nodig, waarmee u de SAP Marketplace kunt openen.

1. Meld u aan en ga naar [SAP Service Marketplace](https://support.sap.com/en/index.html). Selecteer **Software**-  >  **installaties downloaden en upgraden**  >  **per alfabetische index**. Selecteer vervolgens **onder H – SAP Hana platform Edition**  >  **SAP Hana platform Edition 2,0**-  >  **installatie**. Down load de bestanden die worden weer gegeven in de volgende scherm afbeelding.

   ![Scherm afbeelding van de bestanden die moeten worden gedownload](./media/hana-installation/image16_download_hana.PNG)

2. In dit voor beeld hebben we SAP HANA 2,0-installatie pakketten gedownload. Vouw in de virtuele machine van Azure Jump box het zelfuitpakkende archief uit in de map zoals hieronder wordt weer gegeven.

   ![Scherm afbeelding van zelf-uitpak archief](./media/hana-installation/image17_extract_hana.PNG)

3. Wanneer de archieven worden uitgepakt, kopieert u de map die door de extractie is gemaakt (in dit geval 51052030). Kopieer de map van het volume van de HANA-/Hana/Shared met grote instanties naar een map die u hebt gemaakt.

   > [!Important]
   > Kopieer de installatie pakketten niet naar het hoofd-of opstart-LUN, omdat de ruimte beperkt is en ook door andere processen moet worden gebruikt.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>SAP HANA installeren op de HANA-eenheid voor grote instanties
Als u SAP HANA wilt installeren, meldt u zich aan als hoofdmap van de gebruiker. Alleen root heeft voldoende machtigingen om SAP HANA te installeren. Stel machtigingen in voor de map die u hebt gekopieerd in/Hana/Shared.

```
chmod –R 744 <Installation bits folder>
```

Als u SAP HANA wilt installeren met behulp van de Graphical User Interface installatie, moet het gtk2-pakket worden geïnstalleerd op HANA grote instanties. Voer de volgende opdracht uit om te controleren of deze is geïnstalleerd:

```
rpm –qa | grep gtk2
```

(In latere stappen geven we de SAP HANA-instellingen weer met de Graphical User Interface.)

Ga naar de installatiemap en navigeer naar de submap HDB_LCM_LINUX_X86_64. 

Open de volgende map:

```
./hdblcmgui 
```
Op dit moment gaat u door een reeks schermen waarin u de gegevens voor de installatie opgeeft. In dit voor beeld installeren we de SAP HANA-database server en de SAP HANA-client onderdelen. Daarom is onze selectie **SAP Hana data base**.

![Scherm afbeelding van het venster SAP HANA levenscyclus beheer, met SAP HANA geselecteerde data base](./media/hana-installation/image18_hana_selection.PNG)

Selecteer op het volgende scherm de optie **nieuw systeem installeren**.

![Scherm afbeelding van het venster SAP HANA levenscyclus beheer, waarbij nieuw systeem installeren is geselecteerd](./media/hana-installation/image19_select_new.PNG)

Selecteer vervolgens onder verschillende extra onderdelen die u kunt installeren.

![Scherm afbeelding van het venster SAP HANA levenscyclus beheer, met een lijst met extra onderdelen](./media/hana-installation/image20_select_components.PNG)

Hier kiezen we de SAP HANA-client en de SAP HANA Studio. Er wordt ook een scale-up-exemplaar geïnstalleerd. Kies vervolgens een **systeem met één host**. 

![Scherm afbeelding van het venster SAP HANA levenscyclus beheer, waarbij één hostsysteem is geselecteerd](./media/hana-installation/image21_single_host.PNG)

Geef vervolgens een aantal gegevens op.

![Scherm afbeelding van het venster SAP HANA levenscyclus beheer met de velden voor systeem eigenschappen die moeten worden gedefinieerd](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Als HANA-systeem-ID (SID) moet u dezelfde SID opgeven als u micro soft hebt geleverd toen u de implementatie van de HANA-grote instantie bestelde. Het kiezen van een andere SID leidt ertoe dat de installatie mislukt vanwege toegangs machtigingen voor de verschillende volumes.

Gebruik voor het installatiepad de/Hana/shared-map. In de volgende stap geeft u de locaties op voor de HANA-gegevens bestanden en de HANA-logboek bestanden.


![Scherm afbeelding van het venster SAP HANA levenscyclus beheer met de velden gegevens en logboek gebied](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> De SID die u hebt opgegeven bij het definiëren van systeem eigenschappen (twee schermen geleden) moet overeenkomen met de SID van de koppel punten. Als er niet overeenkomt, gaat u terug en past u de SID aan op de waarde die u hebt op de koppel punten.

Controleer in de volgende stap de hostnaam en pas deze uiteindelijk corrigeren. 

![Scherm afbeelding van het venster SAP HANA levenscyclus beheer, met hostnaam](./media/hana-installation/image24_review_host_name.PNG)

In de volgende stap moet u ook de gegevens ophalen die u aan micro soft hebt gegeven toen u de implementatie van de HANA-grote instanties bestelde. 

![Scherm afbeelding van het beheer van SAP HANA levenscyclus, met de velden van de systeem beheerder die u wilt definiëren](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Geef dezelfde **gebruikers-id** en **gebruikers groep** voor de systeem beheerder op die u aan micro soft hebt verstrekt, terwijl u de eenheids implementatie ordent. Anders mislukt de installatie van SAP HANA op de HANA-eenheid voor grote instanties.

De volgende twee schermen worden hier niet weer gegeven. Hiermee kunt u het wacht woord opgeven voor de systeem gebruiker van de SAP HANA-data base en het wacht woord voor de sapadm gebruiker. De laatste wordt gebruikt voor de SAP host-agent die wordt geïnstalleerd als onderdeel van de SAP HANA data base-instantie.

Nadat u het wacht woord hebt gedefinieerd, ziet u een bevestigings scherm. Controleer alle gegevens die worden weer gegeven en ga door met de installatie. U bereikt een voortgangs scherm waarin de voortgang van de installatie wordt gedocumenteerd, zoals deze:

![Scherm afbeelding van het venster SAP HANA levenscyclus beheer met voortgangs indicatoren](./media/hana-installation/image27_show_progress.PNG)

Als de installatie is voltooid, ziet u een scherm zoals deze:

![Scherm afbeelding van het venster SAP HANA levenscyclus beheer, waarmee wordt aangegeven dat de installatie is voltooid](./media/hana-installation/image28_install_finished.PNG)

Het SAP HANA-exemplaar moet nu actief en klaar zijn voor gebruik. U moet er verbinding mee kunnen maken vanuit SAP HANA Studio. Zorg er ook voor dat u de meest recente updates controleert en toepast.


## <a name="next-steps"></a>Volgende stappen

- [SAP HANA Large Instances hoge Beschik baarheid en herstel na nood geval op Azure](hana-overview-high-availability-disaster-recovery.md)

