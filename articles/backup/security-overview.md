---
title: Overzicht van beveiligings functies
description: Meer informatie over de beveiligings mogelijkheden in Azure Backup waarmee u uw back-upgegevens kunt beschermen en voldoen aan de beveiligings behoeften van uw bedrijf.
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 9aa1909f1590b477d9a7f7a09ad0c2b1936e3e29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96325652"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Overzicht van beveiligings functies in Azure Backup

Een van de belangrijkste stappen die u kunt nemen om uw gegevens te beschermen, is een betrouw bare back-upinfrastructuur. Maar het is net zo belang rijk om ervoor te zorgen dat uw gegevens op een veilige manier worden opgeslagen en dat uw back-ups te allen tijde worden beveiligd. Azure Backup biedt beveiliging voor uw back-upomgeving-zowel wanneer uw gegevens onderweg zijn als in rust. Dit artikel bevat een overzicht van de beveiligings mogelijkheden in Azure Backup die u helpen uw back-upgegevens te beschermen en te voldoen aan de beveiligings behoeften van uw bedrijf.

## <a name="management-and-control-of-identity-and-user-access"></a>Beheer en controle van identiteits-en gebruikers toegang

Opslag accounts die worden gebruikt door Recovery Services kluizen, zijn geïsoleerd en kunnen niet worden geopend door gebruikers voor schadelijke doel einden. De toegang is alleen toegestaan via Azure Backup beheer bewerkingen, zoals herstellen. Met Azure Backup kunt u de beheerde bewerkingen beheren door middel van verfijnde toegang met behulp [van Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](./backup-rbac-rs-vault.md). Met Azure RBAC kunt u taken binnen uw team gescheiden houden en alleen de hoeveelheid toegang verlenen aan gebruikers die nodig zijn om hun taken uit te voeren.

Azure Backup biedt drie [ingebouwde rollen](../role-based-access-control/built-in-roles.md) voor het beheren van bewerkingen voor het beheer van back-ups:

* Back-upinzender: voor het maken en beheren van back-ups, behalve het verwijderen van Recovery Services kluis en het verlenen van toegang tot anderen
* Back-upoperator: alles wat een bijdrager is behalve back-ups verwijderen en back-upbeleid beheren
* Back-uplezer: machtigingen voor het weer geven van alle bewerkingen voor back-upbeheer

Meer informatie over [toegangs beheer op basis van rollen voor Azure om Azure backup te beheren](./backup-rbac-rs-vault.md).

Azure Backup heeft verschillende beveiligings mechanismen die zijn ingebouwd in de service om beveiligings problemen te voor komen, te detecteren en op te lossen. Meer informatie over [beveiligings controles voor Azure backup](./security-baseline.md).

## <a name="separation-between-guest-and-azure-storage"></a>Schei ding tussen gast en Azure Storage

Met Azure Backup, dat back-ups van virtuele machines en SQL-en SAP HANA in back-ups van de VM omvat, worden de back-upgegevens opgeslagen in azure Storage en heeft de gast geen directe toegang tot back-upopslag of de inhoud ervan.  Met back-up van de virtuele machine wordt het maken en opslaan van back-upmomentopnames uitgevoerd door Azure Fabric, waarbij de gast geen andere betrokkenheid heeft dan de werk belasting voor toepassings consistente back-ups stilt.  Met SQL en SAP HANA krijgt de back-upextensie tijdelijke toegang om te schrijven naar specifieke blobs.  Op deze manier, zelfs in een gemanipuleerde omgeving, kunnen bestaande back-ups niet worden gewijzigd of verwijderd door de gast.

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Internet connectiviteit niet vereist voor Azure VM-back-up

Voor het maken van een back-up van virtuele Azure-machines is het verplaatsen van gegevens van de schijf naar de Recovery Services kluis vereist. Alle vereiste communicatie-en gegevens overdrachten worden echter alleen uitgevoerd op het Azure-backbone-netwerk zonder dat daarvoor toegang nodig is tot het virtuele netwerk. Daarom is het niet vereist dat voor back-ups van Azure-Vm's in beveiligde netwerken toegang tot IP-adressen of FQDN-namen wordt toegestaan.

## <a name="private-endpoints-for-azure-backup"></a>Privé-eind punten voor Azure Backup

U kunt nu [persoonlijke eind punten](../private-link/private-endpoint-overview.md) gebruiken om een back-up te maken van uw gegevens op een veilige manier van servers in een virtueel netwerk naar uw Recovery Services kluis. Het persoonlijke eind punt gebruikt een IP-adres van de VNET-schijf ruimte voor uw kluis. u hoeft uw virtuele netwerken dus niet beschikbaar te maken voor open bare Ip's. Privé-eind punten kunnen worden gebruikt voor het maken van back-ups en het herstellen van uw SQL-en SAP HANA-data bases die worden uitgevoerd in uw Azure-Vm's. Het kan ook worden gebruikt voor uw on-premises servers met behulp van de MARS-agent.

Meer informatie over privé-eindpunten voor Azure Backup vindt u [hier](./private-endpoints.md).

## <a name="encryption-of-data"></a>Versleuteling van gegevens

Versleuteling beschermt uw gegevens en helpt u om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie. Gegevens versleuteling vindt in veel fasen plaats in Azure Backup:

* Binnen Azure worden gegevens in transit tussen Azure Storage en de kluis [beveiligd door https](backup-support-matrix.md#network-traffic-to-azure). Deze gegevens blijven op het Azure-backbone-netwerk.

* Back-upgegevens worden automatisch versleuteld met sleutels die door het [platform worden beheerd](backup-encryption.md)en u hoeft geen expliciete actie uit te voeren om het bestand in te scha kelen. U kunt ook uw back-upgegevens versleutelen met door de [klant beheerde sleutels](encryption-at-rest-with-cmk.md) die zijn opgeslagen in de Azure Key Vault. Dit is van toepassing op alle werk belastingen waarvan een back-up wordt gemaakt naar uw Recovery Services kluis.

* Azure Backup ondersteunt back-ups en herstel bewerkingen van Azure-Vm's waarvan het besturings systeem/de gegevens schijven zijn versleuteld met [Azure Disk Encryption (ADE)](backup-azure-vms-encryption.md#encryption-support-using-ade) en [vm's met CMK versleutelde schijven](backup-azure-vms-encryption.md#encryption-using-customer-managed-keys). Meer informatie vindt u in de [versleutelde Azure vm's en Azure backup](./backup-azure-vms-encryption.md).

* Wanneer er een back-up van gegevens van on-premises servers met de MARS-agent wordt gemaakt, worden gegevens versleuteld met een wachtwoordzin voordat ze worden geüpload naar Azure Backup en ontsleuteld nadat deze is gedownload van Azure Backup. Meer informatie over [beveiligings functies voor het beveiligen van hybride back-ups](#security-features-to-help-protect-hybrid-backups).

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>Beveiliging van back-upgegevens tegen onbedoelde verwijderingen

Azure Backup biedt beveiligings functies voor het beveiligen van back-upgegevens, zelfs na verwijdering. Als de gebruiker de back-up van een virtuele machine verwijdert, wordt de back-up met de optie voorlopig verwijderd gedurende 14 extra dagen bewaard, zodat het back-upitem zonder gegevens verlies kan worden hersteld. De extra 14 dagen retentie van back-upgegevens in de status ' voorlopig verwijderen ' brengt geen kosten voor u in rekening. Meer [informatie over zacht verwijderen](backup-azure-security-feature-cloud.md).

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>Bewaking en waarschuwingen van verdachte activiteiten

Azure Backup biedt [ingebouwde mogelijkheden voor bewaking en waarschuwingen](./backup-azure-monitoring-built-in-monitor.md) voor het weer geven en configureren van acties voor gebeurtenissen die betrekking hebben op Azure backup. [Back-uprapporten](./configure-reports.md) fungeren als een eenmalige bestemming voor het bijhouden van het gebruik, het controleren van back-ups en herstel bewerkingen en het identificeren van de belangrijkste trends op verschillende niveaus. Met behulp van de hulpprogram ma's voor controle en rapportage van Azure Backup kunt u een waarschuwing ontvangen voor elke onbevoegde, verdachte of schadelijke activiteit zodra deze zich voordoen.

## <a name="security-features-to-help-protect-hybrid-backups"></a>Beveiligings functies voor het beveiligen van hybride back-ups

Azure Backup-Service gebruikt de Microsoft Azure Recovery Services-agent (MARS) om back-ups te maken van bestanden, mappen en het volume of de systeem status van een on-premises computer naar Azure. MARS biedt nu beveiligings functies waarmee hybride back-ups kunnen worden beveiligd. Deze functies zijn onder andere:

* Er wordt een extra beveiligingslaag toegevoegd wanneer een kritieke bewerking wordt uitgevoerd, zoals het wijzigen van een wachtwoordzin. Deze validatie is om ervoor te zorgen dat dergelijke bewerkingen alleen kunnen worden uitgevoerd door gebruikers die geldige Azure-referenties hebben. Meer [informatie over de functies die aanvallen verhinderen](./backup-azure-security-feature.md#prevent-attacks).

* Verwijderde back-upgegevens worden nog 14 dagen na de verwijdering bewaard. Dit waarborgt de herstel baarheid van de gegevens binnen een bepaalde periode, waardoor er geen gegevens verloren gaan, zelfs als er een aanval plaatsvindt. Daarnaast worden er voor het beveiligen van beschadigde gegevens een groter aantal minimale herstel punten onderhouden. [Meer informatie over het herstellen van verwijderde back-upgegevens](./backup-azure-security-feature.md#recover-deleted-backup-data).

* Voor gegevens waarvan een back-up is gemaakt met behulp Azure Backup van de MARS-agent (Microsoft Azure Recovery Services) wordt een wachtwoordzin gebruikt om ervoor te zorgen dat gegevens worden versleuteld voordat ze naar Azure Backup worden gedecodeerd en alleen worden ontsleuteld na het downloaden De gegevens van de wachtwoordzin zijn alleen beschikbaar voor de gebruiker die de wachtwoordzin heeft gemaakt en de agent die ermee is geconfigureerd. Er wordt niets verzonden of gedeeld met de service. Dit zorgt voor een volledige beveiliging van uw gegevens, omdat alle gegevens die per ongeluk worden blootgesteld (zoals een man-in-the-middle-aanval op het netwerk) niet kunnen worden gebruikt zonder de wachtwoordzin en de wachtwoordzin niet via het netwerk wordt verzonden.

## <a name="compliance-with-standardized-security-requirements"></a>Naleving van gestandaardiseerde beveiligings vereisten

Om organisaties te helpen bij de naleving van nationale, regionale en branchespecifieke vereisten voor het verzamelen en gebruiken van gegevens van individuen, Microsoft Azure & Azure Backup een uitgebreide set certificeringen en verklaringen bieden. [Bekijk de lijst met nalevings certificeringen](compliance-offerings.md)

## <a name="next-steps"></a>Volgende stappen

* [Beveiligings functies voor het beveiligen van Cloud werkbelastingen die gebruikmaken van Azure Backup](backup-azure-security-feature-cloud.md)
* [Beveiligings functies voor het beveiligen van hybride back-ups die gebruikmaken van Azure Backup](backup-azure-security-feature.md)