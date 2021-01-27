---
title: Overzicht van Azure Disk Backup
description: Meer informatie over de back-upoplossing voor Azure-schijven.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 4db2a5f3f02322f18fcf9203c3560905cde86996
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98915505"
---
# <a name="overview-of-azure-disk-backup-in-preview"></a>Overzicht van back-ups van Azure-schijf (in preview-versie)

>[!IMPORTANT]
>Azure Disk Backup is in Preview zonder service level agreement en wordt niet aanbevolen voor productie werkbelastingen. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. Zie de [ondersteunings matrix](disk-backup-support-matrix.md)voor de beschik baarheid van regio's.
>
>[Vul dit formulier](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) in om u aan te melden voor de preview-versie.

Azure Disk Backup is een systeem eigen, cloud-gebaseerde back-upoplossing die uw gegevens beveiligt op beheerde schijven. Het is een eenvoudige, veilige en kosteneffectieve oplossing waarmee u in een paar stappen beveiliging voor beheerde schijven kunt configureren. Zo weet u zeker dat u uw gegevens kunt herstellen in een nood geval.

Azure Disk Backup biedt een kant-en-klare oplossing voor het beheer van de duur van moment opnamen voor beheerde schijven door het periodiek maken van moment opnamen te automatiseren en deze te bewaren voor de geconfigureerde duur met behulp van het back-upbeleid. U kunt de moment opnamen van de schijf met nul kosten van de infra structuur beheren, zonder dat u hiervoor aangepaste scripts of beheer overhead nodig hebt. Dit is een crash consistente back-upoplossing die tijdgebonden back-ups maakt van een beheerde schijf met behulp van [incrementele moment opnamen](../virtual-machines/disks-incremental-snapshots.md) met ondersteuning voor meerdere back-ups per dag. Het is ook een oplossing zonder agent en heeft geen invloed op de prestaties van productie toepassingen. Het ondersteunt back-ups en herstel van zowel besturings systeem-als gegevens schijven (inclusief gedeelde schijven), ongeacht of ze momenteel zijn gekoppeld aan een actieve virtuele machine van Azure.

Als u een toepassings consistente back-up van de virtuele machine met de gegevens schijven of een optie voor het herstellen van een volledige virtuele machine met een back-up nodig hebt, kunt u een bestand of map herstellen of herstellen naar een secundaire regio en vervolgens de back-upoplossing voor [Azure VM](backup-azure-vms-introduction.md) gebruiken. Azure Backup biedt naast [Azure VM-back-](./backup-azure-vms-introduction.md) upoplossingen naast elkaar ondersteuning voor het maken van back-ups van Managed disks met behulp van schijf back-ups. Dit is handig wanneer u eenmalige toepassings consistente back-ups van virtuele machines en frequentere back-ups van besturingssysteem schijven of een specifieke gegevens schijf die consistent zijn, niet van invloed zijn op de prestaties van de productie toepassing.

Azure-schijf back-up is geïntegreerd in Back-upcentrum. Dit biedt een **uniforme beheer ervaring** in azure voor ondernemingen om de back-ups op schaal te beheren, te bewaken, te beheren en te analyseren.

## <a name="key-benefits-of-disk-backup"></a>Belangrijkste voor delen van schijf back-ups

Azure schijf back-up is een agentloze en crash consistente oplossing die gebruikmaakt van [incrementele moment opnamen](../virtual-machines/disks-incremental-snapshots.md) en biedt de volgende voor delen:

- Frequentere en snelle back-ups zonder de virtuele machine te onderbreken.
- Heeft geen invloed op de prestaties van de productie toepassing.
- Geen beveiligings problemen omdat er geen aangepaste scripts hoeven te worden uitgevoerd of agents worden geïnstalleerd.
- Een kosteneffectieve oplossing voor het maken van back-ups van specifieke schijven in vergelijking met het maken van een back-up van de volledige virtuele machine.

De oplossing voor back-ups van Azure-schijven is handig in de volgende scenario's:

- Er zijn frequente back-ups per dag nodig zonder dat de quiescent van de toepassing wordt gemaakt
- Apps die worden uitgevoerd in een cluster scenario: zowel Windows Server-failovercluster als Linux-clusters schrijven naar gedeelde schijven
- Specifieke nood zaak voor back-up zonder agent vanwege beveiligings-of prestatie problemen in de toepassing
- Toepassings consistente back-up van de virtuele machine is niet haalbaar, omdat line-of-Business-Apps geen ondersteuning bieden voor Volume Shadow Copy Service (VSS).

Overweeg Azure Disk Backup in scenario's waarin:

- Er wordt een essentiële toepassing uitgevoerd op een virtuele machine van Azure die meerdere back-ups per dag vereist om te voldoen aan de Recovery Point Objective, maar zonder dat dit van invloed is op de productie omgeving of de prestaties van de toepassing
- uw organisatie-of bedrijfs regelgeving beperkt het installeren van agents vanwege beveiligings problemen
- het uitvoeren van aangepaste voor-of post-scripts en het aanroepen van bevriezen en ontdooien op virtuele Linux-machines om toepassings consistente back-ups te verkrijgen.
- container toepassingen die worden uitgevoerd op de Azure Kubernetes-service (AKS-cluster) gebruiken beheerde schijven als permanente opslag. U moet nu een back-up maken van de beheerde schijf via automatiserings scripts die moeilijk te beheren zijn.
- een beheerde schijf houdt essentiële Bedrijfs gegevens in, gebruikt als bestands share, of bevat back-upbestanden van de data base, en u wilt de back-upkosten optimaliseren door niet te investeren in azure VM backup
- U hebt veel Linux-en Windows-virtuele machines met één schijf (dat wil zeggen, een virtuele machine met alleen een besturingssysteem schijf en geen gegevens schijven) die als host fungeren voor webserver of zonder status-minder computers of die fungeert als een faserings omgeving met configuratie-instellingen van de toepassing. u hebt een voordelige oplossing voor back-ups nodig om de besturingssysteem schijf te beveiligen. Als u bijvoorbeeld een snelle back-up op aanvraag wilt activeren voordat u de virtuele machine bijwerkt of repareert
- een virtuele machine voert een besturingssysteem configuratie uit die niet wordt ondersteund door de back-upoplossing van Azure VM (bijvoorbeeld Windows 2008 32-bits server)

## <a name="how-the-backup-and-restore-process-works"></a>Hoe het back-up-en herstel proces werkt

- De eerste stap bij het configureren van back-ups voor Azure Managed disks is het maken van een [back-upkluis](backup-vault-overview.md). De kluis biedt een geconsolideerde weer gave van de back-ups die zijn geconfigureerd voor verschillende werk belastingen.

- Maak vervolgens een back-upbeleid waarmee u de back-upfrequentie en de Bewaar periode kunt configureren.

- Als u een back-up wilt configureren, gaat u naar de back-upkluis, wijst u een back-upbeleid toe, selecteert u de beheerde schijf waarvan een back-up moet worden gemaakt en geeft u een resource groep op waar de moment opnamen moeten worden opgeslagen en beheerd. Azure Backup geplande back-uptaken worden automatisch geactiveerd die een incrementele moment opname van de schijf maken op basis van de back-upfrequentie. Oudere moment opnamen worden verwijderd volgens de Bewaar duur die is opgegeven in het back-upbeleid.

- Azure Backup gebruikt [incrementele moment opnamen](../virtual-machines/disks-incremental-snapshots.md#restrictions) van de beheerde schijf. Incrementele moment opnamen zijn een kosteneffectieve, punt-in-time back-up van beheerde schijven die worden gefactureerd voor de Delta wijzigingen op de schijf sinds de laatste moment opname. Ze worden altijd opgeslagen op de meest rendabele opslag, standaard HDD-opslag, ongeacht het opslag type van de bovenliggende schijven. De eerste moment opname van de schijf neemt de gebruikte grootte van de schijf in beslag en opeenvolgende incrementele moment opnamen verschillen wijzigingen in de schijf sinds de laatste moment opname.

- Zodra u de back-up van een beheerde schijf hebt geconfigureerd, wordt er een back-upexemplaar gemaakt binnen de back-upkluis. Met behulp van het back-upexemplaar kunt u de status van back-upbewerkingen vinden, back-ups op aanvraag activeren en herstel bewerkingen uitvoeren. U kunt ook de status van back-ups in meerdere kluizen en back-upinstanties weer geven met behulp van Back-upcentrum, waarmee u één deel venster met glas kunt weer geven.

- Als u wilt herstellen, selecteert u het herstel punt van waaruit u de schijf wilt herstellen. Geef de resource groep op waarin de herstelde schijf moet worden gemaakt vanuit de moment opname. Azure Backup biedt een onmiddellijke herstel ervaring omdat de moment opnamen lokaal zijn opgeslagen in uw abonnement.

- Backup-kluis maakt gebruik van beheerde identiteit voor toegang tot andere Azure-resources. Voor het configureren van een back-up van een beheerde schijf en voor het herstellen van de back-up van de back-upkluis is een set machtigingen vereist voor de bron schijf, de momentopname resource groep waar moment opnamen worden gemaakt en beheerd en de doel resource groep waar u de back-up wilt terugzetten. U kunt machtigingen verlenen aan de beheerde identiteit door gebruik te maken van Azure op rollen gebaseerd toegangs beheer (Azure RBAC). Beheerde identiteit is een service-principal van een speciaal type dat alleen kan worden gebruikt met Azure-resources. Meer informatie over [beheerde identiteiten](../active-directory/managed-identities-azure-resources/overview.md).

- Momenteel ondersteunt Azure-schijf back-ups operationele back-ups van beheerde schijven en worden de back-ups niet gekopieerd naar back-upkluis opslag. Raadpleeg de [ondersteunings matrix](disk-backup-support-matrix.md)voor een gedetailleerde lijst met ondersteunde en niet-ondersteunde scenario's, en beschik baarheid van regio's.

## <a name="pricing"></a>Prijzen

Azure Backup biedt een oplossing voor het beheer van de moment opname levenscyclus voor het beveiligen van Azure-schijven. De moment opnamen van de schijf die zijn gemaakt door Azure Backup worden opgeslagen in de resource groep binnen uw Azure-abonnement en maken kosten voor de **opslag van moment opnamen** . U kunt de [prijs van een beheerde schijf](https://azure.microsoft.com/pricing/details/managed-disks/) bezoeken voor meer informatie over de prijzen van de moment opname. Omdat de moment opnamen niet worden gekopieerd naar de back-upkluis, brengt Azure Backup geen kosten in rekening voor het **beveiligde exemplaar** en zijn de **back-upopslagkosten** niet van toepassing. Daarnaast nemen incrementele moment opnamen verschillen sinds de laatste moment opname en worden ze altijd opgeslagen in de standaard opslag, ongeacht het opslag type van de bovenliggende en beheerde schijven, en worden er kosten in rekening gebracht op basis van de prijzen van de standaard opslag. Dit maakt back-ups van Azure-schijven een rendabele oplossing.

## <a name="next-steps"></a>Volgende stappen

- [Azure Disck Backup-ondersteuningsmatrix](disk-backup-support-matrix.md)
