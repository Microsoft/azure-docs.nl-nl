---
title: Problemen met replicatie van virtuele Azure-machines met Azure Site Recovery oplossen
description: Problemen met de replicatie in azure VM-nood herstel oplossen met Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 04/03/2020
ms.openlocfilehash: dc14334668b76ee8cbb81e48abfe1eecf17fa138
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007355"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Problemen met replicatie in nood herstel voor Azure VM oplossen

In dit artikel worden veelvoorkomende problemen in Azure Site Recovery beschreven wanneer u Azure virtual machines (VM) repliceert en herstelt van de ene regio naar een andere regio. Ook wordt uitgelegd hoe u veelvoorkomende problemen oplost. Zie de [ondersteunings matrix voor het repliceren van virtuele Azure-machines](./azure-to-azure-support-matrix.md)voor meer informatie over ondersteunde configuraties.

Azure Site Recovery gegevens van de bron regio consistent repliceren naar de herstel regio voor nood gevallen. Er wordt elke vijf minuten een crash-consistent herstel punt gemaakt. Als Site Recovery geen herstel punten voor 60 minuten kunt maken, wordt u hiervan op de hoogte door deze informatie:

```plaintext
Error message: "No crash consistent recovery point available for the VM in the last 60 minutes."

Error ID: 153007
```

In de volgende secties worden oorzaken en oplossingen beschreven.

## <a name="high-data-change-rate-on-the-source-virtual-machine"></a>Hoge snelheid voor gegevenswijzigingen op de virtuele bronmachine

Azure Site Recovery maakt een gebeurtenis als de wijzigings frequentie van de gegevens op de virtuele bron machine hoger is dan de ondersteunde limieten. Als u wilt weten of het probleem wordt veroorzaakt door een hoog verloop, gaat u naar **gerepliceerde items**  >  **VM**  >  **-gebeurtenissen-afgelopen 72 uur**.
U ziet de **wijzigings frequentie voor gebeurtenis gegevens die groter is dan de ondersteunde limieten**:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png" alt-text="Azure Site Recovery pagina met een hoge wijzigings frequentie voor gegevens die te hoog is.":::

Als u de gebeurtenis selecteert, ziet u de exacte schijf gegevens:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png" alt-text="Pagina waarop de details van de gegevens wijzigings frequentie worden weer gegeven.":::

### <a name="azure-site-recovery-limits"></a>Azure Site Recovery-limieten

De volgende tabel bevat de Azure Site Recovery-limieten. Deze limieten zijn gebaseerd op onze tests, maar ze kunnen niet alle mogelijke combi Naties van invoer uitvoer (I/O) voor toepassingen bedekken. De werkelijke resultaten kunnen variëren op basis van uw toepassings-I/O-combinatie.

Er zijn twee limieten die u moet overwegen: gegevens verloop per schijf en gegevens verloop per virtuele machine. Laten we eens kijken naar de Premium P20-schijf in de volgende tabel voor een voor beeld. Voor één virtuele machine kan Site Recovery 5 MB/s aan verloop per schijf verwerken met een maximum van vijf dergelijke schijven. Site Recovery heeft een limiet van 54 MB/s voor het totale verloop per VM.

**Doel van replicatie opslag** | **Gemiddelde I/O-grootte voor de bron schijf** |**Gemiddeld gegevens verloop van bron schijf** | **Totaal gegevens verloop per dag voor bron gegevens schijf**
---|---|---|---
Standard Storage | 8 kB    | 2 MB/s | 168 GB per schijf
Premium P10 of P15 schijf | 8 kB    | 2 MB/s | 168 GB per schijf
Premium P10 of P15 schijf | 16 kB | 4 MB/s |    336 GB per schijf
Premium P10 of P15 schijf | 32 kB of meer | 8 MB/s | 672 GB per schijf
Premium P20 of P30 of P40 of P50 schijf | 8 kB    | 5 MB/s | 421 GB per schijf
Premium P20 of P30 of P40 of P50 schijf | 16 kB of meer |20 MB/s | 1684 GB per schijf

### <a name="solution"></a>Oplossing

Azure Site Recovery heeft limieten voor de wijzigings snelheid van gegevens, afhankelijk van het type schijf. Als u wilt zien of dit probleem terugkerend of tijdelijk is, zoekt u de gegevens wijzigings frequentie van de betreffende virtuele machine. Ga naar de virtuele bron machine, zoek de metrische gegevens onder **bewaking** en voeg de metrische gegevens toe zoals in deze scherm opname wordt weer gegeven:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/churn.png" alt-text="Pagina met het proces met drie stappen voor het zoeken van de gegevens wijzigings frequentie.":::

1. Selecteer **metrische gegevens toevoegen** en voeg de **besturingssysteem schijf toe geschreven bytes per seconde** en **geschreven bytes per seconde voor de gegevens schijf**.
1. De piek bewaken, zoals weer gegeven in de scherm opname.
1. Bekijk het totale aantal schrijf bewerkingen dat wordt uitgevoerd op de schijven van het besturings systeem en alle gegevens schijven gecombineerd. Deze metrische gegevens geven mogelijk geen informatie op het niveau per schijf, maar geven het totale patroon van het verloop van gegevens aan.

Een piek in de gegevens wijzigings verhouding kan afkomstig zijn van een incidentele gegevens burst. Als de waarde voor het wijzigen van de gegevens groter is dan 10 MB/s (voor Premium) of 2 MB/s (voor Standard) en is uitgeschakeld, wordt de replicatie weer gegeven. Als het verloop consistent is naast de ondersteunde limiet, kunt u een van de volgende opties overwegen:

- Sluit de schijf uit die een hoge mate van gegevens wijziging veroorzaakt: Schakel eerst de replicatie uit. Vervolgens kunt u de schijf uitsluiten met behulp van [Power shell](azure-to-azure-exclude-disks.md).
- De laag van de opslag schijf voor nood herstel wijzigen: deze optie kan alleen worden uitgevoerd als het verloop van de schijf gegevens lager is dan 20 MB/s. Een virtuele machine met een P10-schijf heeft bijvoorbeeld een gegevens verloop van meer dan 8 MB/s, maar minder dan 10 MB/s. Als de klant een P30-schijf kan gebruiken voor doel opslag tijdens de beveiliging, kan het probleem worden opgelost. Deze oplossing is alleen mogelijk voor machines die Premium-Managed schijven gebruiken. Volg deze stappen:

  1. Ga naar **schijven** van de betreffende gerepliceerde machine en kopieer de naam van de replica schijf.
  1. Ga naar deze replica van de beheerde schijf.
  1. U ziet mogelijk een banner in **overzicht** met de melding dat er een SAS-URL is gegenereerd. Selecteer deze banner en Annuleer de export. Sla deze stap over als u de banner niet ziet.
  1. Zodra de SAS-URL is ingetrokken, gaat u naar **configuratie** voor de beheerde schijf. Verg root de grootte zodat Site Recovery het waargenomen verloop op de bron schijf ondersteunt.

## <a name="network-connectivity-problems"></a>Problemen met de netwerk verbinding

### <a name="network-latency-to-a-cache-storage-account"></a>Netwerk latentie naar een cache-opslag account

Site Recovery worden gerepliceerde gegevens naar het cache-opslag account verzonden. U kunt de netwerk latentie ondervinden als het uploaden van de gegevens van een virtuele machine naar het cache-opslag account langzamer is dan 4 MB in drie seconden.

Gebruik [AzCopy](../storage/common/storage-use-azcopy-v10.md)om te controleren of er een probleem is met de latentie. U kunt dit opdracht regel hulpprogramma gebruiken voor het uploaden van gegevens van de virtuele machine naar het cache-opslag account. Als de latentie hoog is, controleert u of u een virtueel netwerk apparaat (NVA) gebruikt om het uitgaande netwerk verkeer van Vm's te beheren. Het apparaat kan worden beperkt als alle replicatie verkeer via de NVA wordt door gegeven.

U kunt het beste een netwerk service-eind punt in uw virtuele netwerk maken voor ' opslag ', zodat het replicatie verkeer niet naar de NVA gaat. Zie [configuratie van virtueel netwerk apparaat](azure-to-azure-about-networking.md#network-virtual-appliance-configuration)voor meer informatie.

### <a name="network-connectivity"></a>Netwerkverbinding

Site Recovery replicatie werkt alleen als de virtuele machine een uitgaande verbinding met specifieke Url's of IP-adresbereiken heeft. Mogelijk hebt u uw virtuele machine achter een firewall of gebruikt u NSG-regels (netwerk beveiligings groep) om de uitgaande verbinding te beheren. Als dat het geval is, kunnen er problemen optreden. Zie [uitgaande connectiviteit voor url's](azure-to-azure-about-networking.md#outbound-connectivity-for-urls)om ervoor te zorgen dat alle url's zijn verbonden.

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>Fout-ID 153006-geen app-consistent herstel punt beschikbaar voor de virtuele machine in de afgelopen "X" minuten

Hier volgen enkele van de meest voorkomende problemen.

### <a name="known-issue-in-sql-server-20082008-r2"></a>Bekend probleem in SQL Server 2008/2008 R2

**Oplossen:** Er is een bekend probleem met SQL Server 2008/2008 R2. Raadpleeg het artikel [Azure site Recovery agent of andere VSS-back-ups van niet-onderdeel zijn mislukt voor een server die als host fungeert voor SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2).

### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Azure Site Recovery-taken mislukken op servers die als host fungeren voor elke versie van SQL Server instanties met AUTO_CLOSE Db's

**Oplossen:** Raadpleeg het artikel [VSS-back-ups van niet-onderdelen, zoals Azure site Recovery taken mislukken op servers die SQL Server instanties hosten met AUTO_CLOSE db's](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser).

### <a name="known-issue-in-sql-server-2016-and-2017"></a>Bekend probleem in SQL Server 2016 en 2017

**Oplossen**: Raadpleeg het artikel [cumulatieve Update 16 voor SQL Server 2017](https://support.microsoft.com/help/4508218/cumulative-update-16-for-sql-server-2017).

### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>U gebruikt Azure Storage Spaces direct-configuratie

**Oplossen**: Azure site Recovery kan geen toepassings consistent herstel punt maken voor opslagruimten direct configuratie. [Configureer het replicatie beleid](azure-to-azure-how-to-enable-replication-s2d-vms.md).

### <a name="app-consistency-not-enabled-on-linux-servers"></a>App-consistentie niet ingeschakeld op Linux-servers

**Oplossen** : Azure site Recovery voor Linux-besturings systeem ondersteunt aangepaste scripts voor toepassingen voor app-consistentie. Het aangepaste script met de voor-en post opties wordt gebruikt door de Azure Site Recovery Mobility-agent voor app-consistentie. [Hier](./site-recovery-faq.md#replication) volgen de stappen om deze functie in te scha kelen.

### <a name="more-causes-because-of-vss-related-issues"></a>Meer oorzaken vanwege VSS-gerelateerde problemen:

Als u verder wilt oplossen, controleert u de bestanden op de bron computer om de exacte fout code op te halen voor de fout:

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log`

Als u de fouten wilt vinden, opent u het bestand _vacp. log_ in een tekst editor en zoekt u naar de teken reeks **vacpError**.

```plaintext
Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|
```

In het vorige voor beeld is **2147754994** de fout code die aangeeft dat er een fout is opgetreden na deze zin.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS Writer is niet geïnstalleerd-fout 2147221164

**Oplossen**: als u een toepassings consistentie label wilt genereren, gebruikt Azure site Recovery Volume Shadow Copy service (VSS). Site Recovery installeert een VSS-provider voor de bewerking om moment opnamen van de app-consistentie te maken. Azure Site Recovery installeert deze VSS-provider als een service. Als VSS-provider niet is geïnstalleerd, mislukt het maken van de toepassings consistentie van de moment opname. De **0x80040154-klasse met fout-id is niet geregistreerd**. Raadpleeg het artikel over het [oplossen van problemen met de VSS Writer-installatie](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS Writer is uitgeschakeld-fout 2147943458

**Oplossen**: voor het genereren van de toepassings consistentie code Azure site Recovery gebruikt VSS. Site Recovery installeert een VSS-provider voor de bewerking om moment opnamen van de app-consistentie te maken. Deze VSS-provider is geïnstalleerd als een service. Als u de VSS-Provider service niet hebt ingeschakeld, mislukt het maken van de toepassings consistentie van de moment opname. De volgende fout wordt weer gegeven: **de opgegeven service is uitgeschakeld en kan niet worden gestart (0x80070422)**.

Als VSS is uitgeschakeld:

- Controleer of het opstart type van de VSS-Provider service is ingesteld op **automatisch**.
- Start de volgende services opnieuw:
  - VSS-service.
  - Azure Site Recovery VSS-provider.
  - VDS-service.

#### <a name="vss-provider-not_registered---error-2147754756"></a>NOT_REGISTERED van VSS-PROVIDER-fout 2147754756

**Oplossen**: voor het genereren van de toepassings consistentie code Azure site Recovery gebruikt VSS. Controleer of de Azure Site Recovery VSS-Provider service is geïnstalleerd.

Gebruik de volgende opdrachten om VSS-provider opnieuw te installeren:

1. Bestaande provider verwijderen:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd"`

1. VSS-provider opnieuw installeren:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Controleer of het opstart type van de VSS-Provider service is ingesteld op **automatisch**.

Start de volgende services opnieuw:

- VSS-service.
- Azure Site Recovery VSS-provider.
- VDS-service.
