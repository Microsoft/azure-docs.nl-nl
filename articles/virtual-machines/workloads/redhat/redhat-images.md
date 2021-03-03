---
title: Overzicht van Red Hat Enterprise Linuxe installatie kopieën in azure
description: Meer informatie over Red Hat Enterprise Linux installatie kopieën in Microsoft Azure.
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 03c033ac5d0f20bbfd4f5dccfb5c2900596d2a41
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673120"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Overzicht van Red Hat Enterprise Linux installatie kopieën

In dit artikel worden beschik bare installatie kopieën van Red Hat Enterprise Linux (RHEL) in azure Marketplace en het beleid beschreven om hun naam en retentie te behouden.

Zie [Red Hat Enterprise Linux levens cyclus](https://access.redhat.com/support/policy/updates/errata)voor meer informatie over Red Hat-ondersteunings beleid voor alle versies van RHEL. Zie [Azure prijs calculator](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)voor prijzen voor meer informatie.

>[!IMPORTANT]
> RHEL-installatie kopieën die momenteel beschikbaar zijn in azure Marketplace ondersteunen de licentie modellen van uw eigen abonnement (BYOS) of betalen naar gebruik. Dynamische switches tussen BYOS en betalen per gebruik kunnen worden uitgevoerd via [Azure Hybrid use Benefit](../../linux/azure-hybrid-benefit-linux.md).

>[!NOTE]
> Voor een probleem met betrekking tot RHEL-installatie kopieën in azure Marketplace kunt u een ondersteunings ticket indienen bij micro soft.

## <a name="view-images-available-in-azure"></a>Beschik bare installatie kopieën weer geven in azure

Wanneer u zoekt naar "Red Hat" in azure Marketplace of wanneer u een resource in de Azure Portal-gebruikers interface maakt, ziet u slechts een subset van alle beschik bare RHEL-installatie kopieën. U kunt altijd de volledige set beschik bare VM-installatie kopieën verkrijgen met behulp van de Azure CLI, Power shell en API.

Voer de volgende opdracht uit om de volledige set beschik bare Red Hat-afbeeldingen in azure weer te geven:

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Naamgevings Conventie

VM-installatie kopieën in azure zijn ingedeeld op uitgever, aanbieding, SKU en versie. De combi natie van Publisher: aanbieding: SKU: version is de afbeelding URN en identificeert de afbeelding die moet worden gebruikt.

`RedHat:RHEL:8-LVM:8.1.20200318`Verwijst bijvoorbeeld naar een RHEL 8,1 LVM-gepartitioneerde afbeelding die is gebouwd op 18 maart 2020.

Hier wordt een voor beeld gegeven van het maken van een RHEL 8,1 VM.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:8.1.20200318 --no-wait
```

### <a name="the-latest-moniker"></a>De ' meest recente ' moniker

De Azure REST API staat het gebruik van de moniker ' laatste ' toe voor de versie in plaats van de specifieke versie. De meest recente beschik bare installatie kopie voor de opgegeven Uitgever, aanbieding en SKU wordt met ' meest recent ' ingericht.

`RedHat:RHEL:8-LVM:latest`Verwijst bijvoorbeeld naar de meest recente LVM-gepartitioneerde afbeelding van RHEL 8 Family.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:latest --no-wait
```

>[!NOTE]
> In het algemeen is het vergelijken van versies om het meest recent te bepalen, de regels van de [methode CompareTo](/dotnet/api/system.version.compareto#system_version_compareto_system_version_).
Deze vergelijking van de afbeeldings versie wordt uitgevoerd door de waarden te vergelijken als een [versie](/dotnet/api/system.version.-ctor) object, niet als een teken reeks.

## <a name="rhel-6-image-types"></a>RHEL 6-afbeeldings typen

>[!NOTE]
> Vanaf 30 2020 december heeft RHEL 6,10 het einde van de levens duur bereikt (EOL). Voor voortdurende ondersteuning kunt u ELS inschakelen als onderdeel van de uitgebreide ondersteunings fase van de levens cyclus. Zie [Red Hat Els-documentatie](./redhat-extended-lifecycle-support.md)voor meer informatie.

Voor RHEL 6. x-installatie kopieën worden de afbeeldings typen weer gegeven in de volgende tabel.

|Uitgever | Aanbieding | SKU-waarde | Versie | Details
|----------|-------|-----------|---------|--------
|RedHat | RHEL | Secundaire versie (bijvoorbeeld 6,9) | Samengevoegde waarden van de RHEL secundaire versie en de publicatie datum (bijvoorbeeld 6.9.2018010506) | Alle Standard RHEL 6. x-installatie kopieën volgen deze Conventie.
|RedHat | RHEL-BYOS | RHEL-raw69 | Samengevoegde waarden van de RHEL secundaire versie en de publicatie datum (bijvoorbeeld 6.9.20181023) | Deze installatie kopie is een RHEL 6,9 BYOS-installatie kopie.
|RedHat | RHEL | RHEL-SAP-APPS | Samengevoegde waarden van de RHEL secundaire versie en de publicatie datum (bijvoorbeeld 6.8.2017053118) | Deze installatie kopie is een installatie kopie van RHEL 6,8 voor SAP-toepassingen. Het is in het bezit van toegang tot SAP-toepassingen opslag plaatsen en base RHEL-opslag plaatsen.
|RedHat | RHEL | RHEL-SAP-HANA | Samengevoegde waarden van de RHEL secundaire versie en de publicatie datum (bijvoorbeeld 6.7.2017053121) | Deze installatie kopie is een RHEL 6,7 voor SAP HANA-installatie kopie. Het is gerechtigd om toegang te krijgen tot SAP HANA opslagplaatsen en base RHEL-opslag plaatsen.

## <a name="rhel-7-image-types"></a>RHEL 7-afbeeldings typen

Voor RHEL 7. x-installatie kopieën zijn er een aantal verschillende typen installatie kopieën. In de volgende tabel ziet u de verschillende sets met installatie kopieën die wij bieden. Als u een volledige lijst wilt weer geven, gebruikt u de Azure CLI-opdracht `az vm image list --publisher redhat --all` .

>[!NOTE]
> Tenzij anders aangegeven, worden alle installatie kopieën LVM gepartitioneerd en wordt er verbinding gemaakt met gewone RHEL-opslag plaatsen. Dat wil zeggen dat de opslag plaatsen geen uitgebreide update ondersteuning (EUS) en geen Update Services voor SAP (E4S) zijn. We gaan nu door met het publiceren van alleen LVM-gepartitioneerde installatie kopieën, maar zijn geopend voor feedback over deze beslissing. Zie [Red Hat Enterprise Linux levens cyclus](https://access.redhat.com/support/policy/updates/errata)voor meer informatie over ondersteuning voor uitgebreide updates en Update Services voor SAP.

|Uitgever | Aanbieding | SKU-waarde | Versie | Details
|----------|-------|------------|---------|--------
|RedHat | RHEL | Secundaire versie (bijvoorbeeld 7,6) | Samengevoegde waarden van de RHEL secundaire versie en de publicatie datum (bijvoorbeeld 7.6.2019102813) | Afbeeldingen die vóór april 2019 zijn gepubliceerd, zijn gekoppeld aan standaard RHEL-opslag plaatsen. Installatie kopieën die zijn gepubliceerd na april 2019 zijn gekoppeld aan de EUS-opslag plaatsen van Red Hat zodat een specifieke secundaire versie kan worden vergrendeld. Klanten die normale opslag plaatsen willen, moeten de installatie kopieën gebruiken die 7-LVM of 7-RAW bevatten in de SKU-waarde (Details volgen). RHEL 7,7 en latere installatie kopieën van LVM worden gepartitioneerd. Alle andere installatie kopieën in deze categorie worden gepartitioneerd.
|RedHat | RHEL | 7-RAW | Samengevoegde waarden van de RHEL secundaire versie en de publicatie datum (bijvoorbeeld 7.6.2019102813) | Deze installatie kopieën worden gepartitioneerd (er zijn bijvoorbeeld geen logische volumes toegevoegd).
|RedHat | RHEL | 7-RAW-CI | Samengevoegde waarden van de RHEL secundaire versie en de publicatie datum (bijvoorbeeld 7.6.2019072418) | Deze installatie kopieën worden gepartitioneerd (er zijn bijvoorbeeld geen logische volumes toegevoegd) en u kunt Cloud-init gebruiken voor het inrichten.
|RedHat | RHEL | 7-LVM | Samengevoegde waarden van de RHEL secundaire versie en de publicatie datum (bijvoorbeeld 7.6.2019062414) | Deze installatie kopieën zijn LVM gepartitioneerd.
|RedHat | RHEL-BYOS | RHEL-{LVM, RAW} | Samengevoegde waarden van de RHEL secundaire versie en de publicatie datum (bijvoorbeeld 7.7.20190819) | Deze installatie kopieën zijn de RHEL 7 BYOS-installatie kopieën. Ze zijn niet gekoppeld aan opslag plaatsen en worden niet in rekening gebracht voor de kosten voor RHEL Premium. Als u geïnteresseerd bent in de RHEL BYOS-installatie kopieën, [vraagt u om toegang](https://aka.ms/rhel-byos). SKU-waarden eindigen met de secundaire versie en duiden aan of de afbeelding RAW of LVM is gepartitioneerd. Een SKU-waarde van RHEL-lvm77 duidt bijvoorbeeld op een LVM gepartitioneerde RHEL 7,7-installatie kopie.
|RedHat | RHEL | RHEL-SAP | Samengevoegde waarden van de RHEL secundaire versie en de publicatie datum (bijvoorbeeld 7.6.2019071300) | Deze installatie kopieën zijn RHEL voor SAP-installatie kopieën. Ze hebben het recht om toegang te krijgen tot de opslag plaatsen SAP HANA en toepassingen, evenals RHEL E4S-opslag plaatsen. Facturering bevat de RHEL Premium en de SAP Premium boven op de basis reken kosten.
|RedHat | RHEL | RHEL-SAP-HA | Samengevoegde waarden van de RHEL secundaire versie en de publicatie datum (bijvoorbeeld 7.6.2019062320) | Deze installatie kopieën zijn RHEL voor SAP met hoge Beschik baarheid en installatie kopieën van Update Services. Ze hebben het recht om toegang te krijgen tot de opslag plaatsen SAP HANA en toepassingen en de opslag plaatsen met hoge Beschik baarheid, evenals RHEL E4S-opslag plaatsen. Billing bevat de Premium van RHEL Premium, SAP Premium en hoge Beschik baarheid boven op de basis reken kosten.
|RedHat | RHEL | RHEL-HA | Samengevoegde waarden van de RHEL secundaire versie en de publicatie datum (bijvoorbeeld 7.6.2019062019) | Deze installatie kopieën zijn RHEL installatie kopieën die ook recht hebben op toegang tot de invoeg toepassing met hoge Beschik baarheid. Ze kosten iets extra boven op de RHEL en de basis reken kosten als gevolg van de Premium-premie voor hoge Beschik baarheid.
|RedHat | RHEL | RHEL-SAP-APPS | Samengevoegde waarden van de RHEL secundaire versie en de publicatie datum (bijvoorbeeld 7.3.2017053118) | Deze installatie kopieën zijn verouderd omdat de SAP-toepassingen en SAP HANA-opslag plaatsen zijn gecombineerd in de SAP-opslag plaatsen. Deze installatie kopieën zijn RHEL voor SAP-toepassingen installatie kopieën. Ze zijn gerechtigd om toegang te krijgen tot SAP-toepassings opslagplaatsen en RHEL-opslag plaatsen.
|RedHat | RHEL | RHEL-SAP-HANA | Samengevoegde waarden van de RHEL secundaire versie en de publicatie datum (bijvoorbeeld 7.3.2018051421) | Deze installatie kopieën zijn verouderd omdat de SAP-toepassingen en SAP HANA-opslag plaatsen zijn gecombineerd in de SAP-opslag plaatsen. Deze installatie kopieën zijn RHEL for SAP HANA installatie kopieën. Ze hebben toegang tot SAP HANA opslagplaatsen en basis RHEL-opslag plaatsen.

## <a name="rhel-8-image-types"></a>RHEL 8-afbeeldings typen

>[!NOTE]
> Red Hat raadt aan om grubby te gebruiken voor het configureren van kernel-opdracht regel parameters in RHEL 8 +. Meer informatie vindt u [hier](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/configuring-kernel-command-line-parameters_managing-monitoring-and-updating-the-kernel).

Details voor RHEL 8-afbeeldings typen vindt u hieronder.

|Uitgever | Aanbieding | SKU-waarde | Versie | Details
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | Samengevoegde waarden van de RHEL secundaire versie en de publicatie datum (bijvoorbeeld 8.0.20191023) | Deze installatie kopieën zijn RHEL 8 gepartitioneerde installatie kopieën die zijn verbonden met standaard Red Hat-opslag plaatsen.
|RedHat | RHEL | 8-Gen2 | Samengevoegde waarden van de RHEL secundaire versie en de publicatie datum (bijvoorbeeld 8.0.20191024) | Deze installatie kopieën zijn Hyper-V-generatie 2 RHEL 8 gepartitioneerde installatie kopieën die zijn verbonden met Standard Red Hat-opslag plaatsen. Zie [ondersteuning voor virtuele machines van de tweede generatie op Azure](../../generation-2.md)voor meer informatie over virtuele machines van de tweede generatie in Azure.

## <a name="rhel-extended-support-add-ons"></a>Uitgebreide ondersteuning voor RHEL-invoeg toepassingen

### <a name="extended-life-cycle-support"></a>Uitgebreide ondersteuning voor levens cyclus

De uitgebreide invoeg toepassing voor ondersteuning (ELS) is een optioneel abonnement waarmee essentiële en belang rijke beveiligingsfixes worden ingeschakeld voor releases die het einde van de levens duur (EOL) hebben bereikt. Meer informatie over RHEL EUS is beschikbaar in [de documentatie van Red Hat](https://access.redhat.com/support/policy/updates/errata#Extended_Life_Cycle_Support).

ELS is momenteel alleen beschikbaar voor RHEL 6,10. Voor betalen per gebruik-installatie kopieën kunt u ELS inschakelen door de stappen in de [Red Hat Els-documentatie](./redhat-extended-lifecycle-support.md)te volgen.

Als u werkt met een oudere versie, is een upgrade naar RHEL 6,10 vereist voordat u ELS kunt inschakelen.

### <a name="extended-update-support"></a>Ondersteuning voor uitgebreide updates

Vanaf 2019 april zijn er RHEL-installatie kopieën beschikbaar die standaard zijn gekoppeld aan de EUS-opslag plaatsen. Meer informatie over RHEL EUS is beschikbaar in [de documentatie van Red Hat](https://access.redhat.com/articles/rhel-eus).

Overschakelen naar EUS-opslag plaatsen is mogelijk en wordt ondersteund. Zie [RHEL Eus and versie-locking RHEL vm's](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms)(Engelstalig) voor instructies over het overschakelen van de virtuele machine naar Eus en meer informatie over Eus.

>[!NOTE]
> EUS wordt niet ondersteund voor RHEL-Extra's. Dit betekent dat als u een pakket installeert dat doorgaans beschikbaar is via het RHEL extras-kanaal, u dit niet kunt doen wanneer u op EUS. Zie [Red Hat Enterprise Linux extra's levens cyclus](https://access.redhat.com/support/policy/updates/extras/)voor meer informatie over de levens cyclus van Red Hat extras.

#### <a name="differentiate-between-regular-and-eus-images"></a>Onderscheid maken tussen reguliere en EUSe installatie kopieën

Klanten die installatie kopieën willen gebruiken die zijn gekoppeld aan EUS-opslag plaatsen, moeten de RHEL-installatie kopie gebruiken die een RHEL-secundair versie nummer in de SKU bevat.

U kunt bijvoorbeeld de volgende twee RHEL 7,4-installatie kopieën zien die beschikbaar zijn.

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

In dit geval `RedHat:RHEL:7.6:7.6.2019102813` wordt standaard gekoppeld aan Eus-opslag plaatsen. De SKU-waarde is 7,4. En `RedHat:RHEL:7-LVM:7.6.2019062414` is standaard gekoppeld aan niet-Eus-opslag plaatsen. De SKU-waarde is 7-LVM.

Als u normale (niet-EUS) opslag plaatsen wilt gebruiken, gebruikt u een installatie kopie die geen secundair versie nummer in de SKU bevat.

#### <a name="rhel-images-with-eus"></a>RHEL-installatie kopieën met EUS

De informatie in de volgende tabel is van toepassing op RHEL-installatie kopieën die zijn verbonden met EUS-opslag plaatsen.

>[!NOTE]
> Op het moment van schrijven hebben alleen RHEL 7,4 en latere secundaire versies EUS-ondersteuning. EUS wordt niet meer ondersteund voor RHEL <= 7,3.
>
> Zie [Red Hat Enterprise Linux levens cyclus](https://access.redhat.com/support/policy/updates/errata)voor meer informatie over de beschik BAARHEID van RHEL Eus.

Secundaire versie |Voor beeld van een EUS-afbeelding              |EUS-status                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7,4      |RedHat: RHEL: 7,4:7.4.2019041718 | Afbeeldingen die zijn gepubliceerd 2019 april en hoger zijn standaard EUS.|
RHEL 7.5      |RedHat: RHEL: 7,5:7.5.2019060305 | Afbeeldingen die zijn gepubliceerd in juni 2019 en hoger, zijn standaard EUS. |
RHEL 7,6      |RedHat: RHEL: 7,6:7.6.2019052206 | Afbeeldingen die zijn gepubliceerd, zijn mogelijk 2019 en hoger EUS. |
RHEL 8,0      |N.v.t.                            | Er is geen EUS beschikbaar in Red Hat.                               |

### <a name="update-services-for-sap"></a>Update Services voor SAP

De meest recente RHEL voor SAP-installatie kopieën worden aangesloten op de Update Services for SAP Solutions abonnementen (E4S). Zie de Red Hat- [documentatie](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)voor meer informatie over E4S.

#### <a name="rhel-images-with-e4s"></a>RHEL-installatie kopieën met E4S

Installatie kopieën van de volgende aanbiedingen die na december 2019 zijn gemaakt, zijn verbonden met E4S-opslag plaatsen:

* RHEL-SAP (RHEL voor SAP)
* RHEL-SAP-HA (RHEL voor SAP met hoge Beschik baarheid en Update Services)

## <a name="other-available-offers-and-skus"></a>Andere beschik bare aanbiedingen en Sku's

De volledige lijst met beschik bare aanbiedingen en Sku's bevat mogelijk extra installatie kopieën die groter zijn dan de voor gaande tabel. Een voorbeeld is `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Deze aanbiedingen kunnen worden gebruikt om ondersteuning te bieden voor specifieke Marketplace-oplossingen. Of ze kunnen worden gepubliceerd voor voor beelden en test doeleinden. Ze kunnen op elk gewenst moment zonder waarschuwing worden gewijzigd of verwijderd. Gebruik deze niet, tenzij hun aanwezigheid openbaar is gedocumenteerd door micro soft of Red Hat.

## <a name="publishing-policy"></a>Publicatie beleid

Micro soft en Red Hat update-installatie kopieën als nieuwe secundaire versies worden uitgebracht, indien nodig voor het oplossen van specifieke veelvoorkomende beveiligings lekken en bloot stellingen (CVEs) of voor incidentele configuratie wijzigingen of updates. We streven ernaar bijgewerkte installatie kopieën zo snel mogelijk binnen drie werk dagen na een release of Beschik baarheid van een CVE-oplossing te bieden.

Alleen de huidige secundaire versie van een bepaalde installatie kopie serie wordt bijgewerkt. Als er een nieuwere secundaire versie wordt uitgebracht, wordt de oudere secundaire versie niet meer bijgewerkt. Met de release van RHEL 7,6 worden RHEL 7,5-installatie kopieën bijvoorbeeld niet meer bijgewerkt.

>[!NOTE]
> Actieve Azure-Vm's die zijn ingericht op basis van RHEL betalen per gebruik-installatie kopieën zijn verbonden met de Azure-RHUI en kunnen updates en oplossingen ontvangen zodra deze door Red Hat worden vrijgegeven en worden gerepliceerd naar de Azure-RHUI. De timing is doorgaans minder dan 24 uur na de officiële release door Red Hat. Voor deze Vm's is geen nieuwe gepubliceerde installatie kopie vereist voor het ophalen van de updates. Klanten hebben volledige controle over wanneer ze de update kunnen initiëren.

## <a name="image-retention-policy"></a>Beleid voor het bewaren van installatie kopieën

Het huidige beleid is om alle eerder gepubliceerde installatie kopieën te blijven gebruiken. Wij behouden ons het recht voor om installatie kopieën te verwijderen waarvan bekend is dat ze problemen kunnen veroorzaken. Zo kunnen installatie kopieën met onjuiste configuraties door volgende platform-of onderdeel updates worden verwijderd. Installatie kopieën die kunnen worden verwijderd, volgen het huidige beleid van de Azure Marketplace om meldingen tot 30 dagen te geven voordat de installatie kopie wordt verwijderd.

## <a name="next-steps"></a>Volgende stappen

* Zie [Red Hat Enterprise Linux (RHEL)-installatie kopieën die beschikbaar zijn in azure](./redhat-imagelist.md)om de volledige lijst met RHEL-installatie kopieën in azure weer te geven.
* Zie voor meer informatie over de Azure Red Hat-update-infra [structuur voor Red Hat update voor on-demand RHEL-vm's in azure](./redhat-rhui.md).
* Voor meer informatie over de RHEL BYOS-aanbieding raadpleegt u [Red Hat Enterprise Linux uw Gold-installatie kopieën met uw eigen abonnement in azure](./byos.md).
* Zie [Red Hat Enterprise Linux levens cyclus](https://access.redhat.com/support/policy/updates/errata)voor meer informatie over Red Hat-ondersteunings beleid voor alle versies van RHEL.
