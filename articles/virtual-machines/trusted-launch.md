---
title: "Preview: vertrouwde start voor Azure Vm's"
description: Meer informatie over het betrouw bare starten van Azure virtual machines.
author: khyewei
ms.author: khwei
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: conceptual
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: template-concept; references_regions
ms.openlocfilehash: 01c5d4aaa3896e05bc743be309df050471ece5ae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582048"
---
# <a name="trusted-launch-for-azure-virtual-machines-preview"></a>Vertrouwde Lance ring voor virtuele machines van Azure (preview-versie)

Azure biedt een naadloze manier om de beveiliging van virtuele machines van de [2e generatie](generation-2.md) te verbeteren. Trusted Launch beschermt tegen geavanceerde en permanente aanvals technieken. Trusted Launch bestaat uit verschillende, gecoördineerde infrastructuur technologieën die onafhankelijk kunnen worden ingeschakeld. Elke technologie biedt een andere beveiligingslaag tegen geavanceerde bedreigingen.

> [!IMPORTANT]
> Voor het maken van een vertrouwde start moeten nieuwe virtuele machines worden gemaakt. U kunt geen vertrouwde start inschakelen op bestaande virtuele machines die in eerste instantie zijn gemaakt zonder deze te maken.
>
> Vertrouwde start is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. 
>
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.


## <a name="benefits"></a>Voordelen 

- Implementeer virtuele machines veilig met gecontroleerde opstart laad Programma's, OS-kernels en stuur Programma's.
- Beveilig sleutels, certificaten en geheimen veilig op de virtuele machines.
- Krijg inzicht in en vertrouwen in de integriteit van de hele opstart keten.
- Zorg ervoor dat werk belastingen worden vertrouwd en kunnen worden geverifieerd.

## <a name="public-preview-limitations"></a>Beperkingen voor openbare preview

**Ondersteuning voor grootte**: alle VM-grootten van de [tweede generatie](generation-2.md) , met uitzonde ring van:

- Lsv2-serie 
- M-serie 
- Mv2-serie 
- NDv4-serie 
- NVv4-serie

**Ondersteuning voor besturings systeem**:
- Red Hat Enter prise Linux 8,3
- SUSE 15 SP2
- Ubuntu 20,04 LTS
- Ubuntu 18.04 LTS
- Windows Server 2019
- Windows Server 2016
- Windows 10 Pro
- Windows 10 Enterprise

**Regio's**: 
- VS - zuid-centraal
- Europa - noord

**Prijzen**: geen extra kosten voor bestaande VM-prijzen.

**De volgende functies worden niet ondersteund in deze preview-versie**:
- Backup
- Azure Site Recovery
- Galerie met gedeelde installatiekopieën
- Tijdelijke besturingssysteem schijf
- Gedeelde schijf
- Beheerde installatiekopie
- Azure Dedicated Host 

## <a name="secure-boot"></a>Beveiligd opstarten

De basis van vertrouwde start is beveiligd opstarten voor uw VM. Deze modus, die wordt geïmplementeerd in platform firmware, beveiligt de installatie van op malware gebaseerde rootkits en opstart Kits. Beveiligd opstarten werkt om ervoor te zorgen dat alleen ondertekende besturings systemen en stuur Programma's kunnen worden opgestart. Er wordt een "basis vertrouwens relatie" voor de software stack op uw virtuele machine gemaakt. Als beveiligd opstarten is ingeschakeld, moeten alle opstart onderdelen van het besturings systeem (boot loader, kernel, kernel-Stuur Programma's) worden ondertekend door vertrouwde uitgevers. Zowel Windows als Linux-distributies ondersteunen beveiligd opstarten. Als beveiligd opstarten niet kan verifiëren dat de installatie kopie is ondertekend door een vertrouwde uitgever, mag de virtuele machine niet worden opgestart. Zie [Secure Boot](/windows-hardware/design/device-experiences/oem-secure-boot) voor meer informatie.

## <a name="vtpm"></a>vTPM

Trusted Launch introduceert ook vTPM voor Azure-Vm's. Dit is een gevirtualiseerde versie van een hardware [Trusted Platform Module](/windows/security/information-protection/tpm/trusted-platform-module-overview)die voldoet aan de TPM 2.0-specificatie. Het fungeert als een speciale beveiligde kluis voor sleutels en metingen. Met Trusted Launch wordt uw virtuele machine voorzien van een eigen toegewezen TPM-exemplaar, dat wordt uitgevoerd in een beveiligde omgeving buiten het bereik van een virtuele machine. De vTPM maakt [Attestation](/windows/security/information-protection/tpm/tpm-fundamentals#measured-boot-with-support-for-attestation) mogelijk door de volledige opstart keten van uw virtuele machine te meten (UEFI, OS, System en stuur Programma's). 

Voor een vertrouwde start wordt de vTPM gebruikt voor het uitvoeren van externe Attestation door de Cloud. Dit wordt gebruikt voor platform status controles en voor het maken van op vertrouwen gebaseerde beslissingen. Als een status controle kan een vertrouwde start cryptografisch certificeren dat uw VM correct is opgestart. Als het proces mislukt, mogelijk omdat uw virtuele machine een niet-geautoriseerd onderdeel uitvoert, worden er in Azure Security Center integriteits waarschuwingen weer geven. De waarschuwingen bevatten details over de onderdelen waarvoor integriteits controles niet zijn geslaagd.

## <a name="virtualization-based-security"></a>Beveiliging op basis van virtualisatie

Op [basis van virtualisatie beveiliging](/windows-hardware/design/device-experiences/oem-vbs) (VBS) wordt de Hyper Visor gebruikt voor het maken van een beveiligde en geïsoleerd geheugen gebied. Windows gebruikt deze regio's voor het uitvoeren van verschillende beveiligings oplossingen met verhoogde bescherming tegen beveiligings problemen en kwaad aardige aanvallen. Met Trusted Launch kunt u Hyper Visor code Integrity (HVCI) en Windows Defender Credential Guard inschakelen.

HVCI is een krachtige systeem beperking voor het beveiligen van de kernel-modus van Windows tegen injectie en uitvoering van schadelijke of niet-geverifieerde code. Hiermee worden Stuur Programma's en binaire bestanden voor de kernelmodus gecontroleerd voordat ze worden uitgevoerd, en wordt voor komen dat niet-ondertekende bestanden in het geheugen worden geladen. Dit zorgt ervoor dat een dergelijke uitvoer bare code niet kan worden gewijzigd nadat deze is toegestaan. Zie [beveiliging op basis van virtualisatie (VBS) en Hyper Visor afgedwongen code-integriteit (HVCI)](https://techcommunity.microsoft.com/t5/windows-insider-program/virtualization-based-security-vbs-and-hypervisor-enforced-code/m-p/240571)voor meer informatie over vbs en HVCI.

Met Trusted Launch en VBS kunt u Windows Defender Credential Guard inschakelen. Met deze functie worden geheimen geïsoleerd en beschermd, zodat alleen bevoegde systeem software deze kan openen. Het helpt te voor komen dat onbevoegde toegang tot geheimen en aanvallen op referentie diefstal, zoals Pass-the-hash-aanvallen (PtH). Zie [Credential Guard](/windows/security/identity-protection/credential-guard/credential-guard)voor meer informatie.


## <a name="security-center-integration"></a>Integratie van Security Center

Trusted Launch is geïntegreerd met Azure Security Center om ervoor te zorgen dat uw virtuele machines op de juiste wijze zijn geconfigureerd. Met Azure Security Center worden compatibele Vm's voortdurend geëvalueerd en worden relevante aanbevelingen gedaan.

- **Aanbeveling om beveiligd opstarten in te scha kelen** : deze aanbeveling is alleen van toepassing op vm's die ondersteuning bieden voor vertrouwde start. Azure Security Center identificeert Vm's die beveiligd opstarten kunnen inschakelen, maar dat ze zijn uitgeschakeld. Er wordt een aanbeveling met weinig ernst uitgegeven om deze functie in te scha kelen.
- **Aanbeveling om vTPM in te scha kelen** : als voor uw virtuele machine vTPM is ingeschakeld, kan Azure Security Center deze gebruiken om gast verklaringen uit te voeren en geavanceerde bedreigings patronen te identificeren. Als Azure Security Center Vm's identificeert die ondersteuning bieden voor vertrouwde start en vTPM uitgeschakeld hebben, wordt er een aanbeveling met lage Ernst verleend om deze functie in te scha kelen. 
- **Beoordeling van de Attestation-status** : als voor uw virtuele machine vTPM is ingeschakeld, kan een uitbrei ding van Azure Security Center op afstand controleren of uw VM op een gezonde manier is opgestart. Dit staat bekend als externe Attestation. Azure Security Center geeft een evaluatie aan, wat de status van externe Attestation aangeeft.

## <a name="azure-defender-integration"></a>Integratie met Azure Defender

Als uw Vm's correct zijn ingesteld met behulp van vertrouwde start, kan Azure Defender u problemen met de VM-status detecteren en waarschuwen.

- **Waarschuwing voor VM-Attestation-fout** : Azure Defender voert regel matig een Attestation-bewerking uit op uw vm's. Dit gebeurt ook nadat de VM is opgestart. Als de Attestation mislukt, wordt een waarschuwing over een gemiddeld Ernst gegenereerd.
    VM-Attestation kan om de volgende redenen mislukken:
    - De attestende informatie, met inbegrip van een opstart logboek, wijkt af van een vertrouwde basis lijn. Dit kan erop duiden dat niet-vertrouwde modules zijn geladen en dat het besturings systeem kan worden aangetast.
    - De Attestation-offerte kan niet worden geverifieerd om afkomstig te zijn van de vTPM van de attested VM. Dit kan erop wijzen dat er malware aanwezig is en het verkeer naar de vTPM kan worden onderschept.
    - De Attestation-extensie op de VM reageert niet. Dit kan duiden op een denial-of-service-aanval door malware of een OS-beheerder.

    > [!NOTE]
    >  Deze waarschuwing is beschikbaar voor Vm's waarop vTPM is ingeschakeld en de Attestation-extensie is geïnstalleerd. Voor het door geven van de attest moet beveiligd opstarten zijn ingeschakeld. Attestation mislukt als beveiligd opstarten is uitgeschakeld. Als u beveiligd opstarten wilt uitschakelen, kunt u deze waarschuwing onderdrukken om valse positieven te voor komen.

- **Waarschuwing voor niet-vertrouwde Linux kernel-module** : voor vertrouwde start met beveiligd opstarten is het mogelijk dat een virtuele machine wordt opgestart, zelfs als een kernelstuurprogramma niet kan worden geauthenticeerd en niet kan worden geladen. Als dit het geval is, geeft Azure Defender een waarschuwing met lage urgentie. Hoewel er geen onmiddellijke bedreiging is, omdat het niet-vertrouwde stuur programma niet is geladen, moeten deze gebeurtenissen worden onderzocht. Overweeg de volgende:
    - Welk kernel-stuur programma is mislukt? Ben ik bekend met dit stuur programma en krijg ik verwacht dat het wordt geladen?
    - Is dit de exacte versie van het stuur programma dat ik verwacht? Zijn de binaire bestanden van het stuur programma intact? Als dit een stuur programma van een derde partij is, laat de leverancier dan de tests van het besturings systeem controleren om deze te laten ondertekenen?


## <a name="faq"></a>Veelgestelde vragen

Veelgestelde vragen over Trusted Launch.

### <a name="why-should-i-use-trusted-launch-what-does-trusted-launch-guard-against"></a>Waarom moet ik een vertrouwde Start gebruiken? Wat houdt Trusted Launch Guard tegen?

Vertrouwde start beveiligingen tegen opstart kits, Root kits en malware op kernel-niveau. Deze geavanceerde typen schadelijke software worden uitgevoerd in de kernelmodus en blijven verborgen voor gebruikers. Bijvoorbeeld:
- Firmware-Rootkit: deze kits overschrijven de firmware van het BIOS van de virtuele machine, zodat de rootkit vóór het besturings systeem kan beginnen. 
- Opstart Kits: deze kits vervangen de bootloader van het besturings systeem, zodat de virtuele machine de opstart Kit voor het besturings systeem laadt.
- Root kits van de kernel: deze kits vervangen een deel van de kernel van het besturings systeem, zodat de rootkit automatisch kan worden gestart wanneer het besturings systeem wordt geladen.
- Pakket namen van Stuur Programma's: deze kits Pretend een van de vertrouwde Stuur Programma's die door het besturings systeem worden gebruikt om te communiceren met de onderdelen van de virtuele machine.

### <a name="what-are-the-differences-between-secure-boot-and-measured-boot"></a>Wat zijn de verschillen tussen beveiligd opstarten en gemeten opstarten?

Bij een beveiligde opstart keten controleert elke stap in het opstart proces een cryptografische hand tekening van de volgende stappen. Met het BIOS wordt bijvoorbeeld een hand tekening op het laad programma gecontroleerd en het laad programma controleert hand tekeningen op alle kern objecten die worden geladen, enzovoort. Als een van de objecten is aangetast, komt de hand tekening niet overeen en wordt de virtuele machine niet opgestart. Zie [Secure Boot](/windows-hardware/design/device-experiences/oem-secure-boot) voor meer informatie. Gemeten opstarten stopt het opstart proces niet, de hash van de volgende objecten in de keten wordt gemeten of berekend en de hashes worden opgeslagen in de platform configuratie registers (PCRs) op de vTPM. Gemeten opstart records worden gebruikt voor de bewaking van de opstart integriteit.

### <a name="what-happens-when-an-integrity-fault-is-detected"></a>Wat gebeurt er als er een integriteits fout wordt gedetecteerd?

Vertrouwde Lance ring voor virtuele Azure-machines wordt bewaakt voor geavanceerde bedreigingen. Als dergelijke bedreigingen worden gedetecteerd, wordt een waarschuwing geactiveerd. Waarschuwingen zijn alleen beschikbaar in de [laag standaard](../security-center/security-center-pricing.md) van Azure Security Center.
Azure Security Center voert periodiek een Attestation-bewerking uit. Als de Attestation mislukt, wordt een waarschuwing over een gemiddeld risico gegenereerd. Het vertrouwd starten van de Attestation kan om de volgende redenen mislukken: 
- De attestende informatie, die een logboek van de betrouw bare Computing Base (TCB) bevat, wijkt af van een vertrouwde basis lijn (zoals wanneer beveiligd opstarten is ingeschakeld). Dit kan erop duiden dat niet-vertrouwde modules zijn geladen en dat het besturings systeem kan worden aangetast.
- De Attestation-offerte kan niet worden geverifieerd om afkomstig te zijn van de vTPM van de attested VM. Dit kan erop wijzen dat er malware aanwezig is en het verkeer naar de TPM kan worden onderschept. 
- De Attestation-extensie op de VM reageert niet. Dit kan duiden op een denial-of-service-aanval door malware of een OS-beheerder.

  
### <a name="how-does-trusted-launch-compared-to-hyper-v-shielded-vm"></a>Hoe wordt een vertrouwde start vergeleken met een Hyper-V-afgeschermde VM?
De Hyper-V afgeschermde VM is momenteel alleen beschikbaar voor Hyper-V. Een [Hyper-V afgeschermde VM](/windows-server/security/guarded-fabric-shielded-vm/guarded-fabric-and-shielded-vms) wordt meestal geïmplementeerd in combi natie met een beveiligde infra structuur. Een beveiligde Fabric bestaat uit een host Guardian-service (HGS), een of meer beveiligde hosts en een set afgeschermde Vm's. Hyper-V afgeschermde Vm's zijn bedoeld voor gebruik in infra structuren waarbij de gegevens en de status van de virtuele machine moeten worden beveiligd door infrastructuur beheerders en niet-vertrouwde software die op de Hyper-V-hosts kan worden uitgevoerd. Vertrouwde start op de andere kant kan worden geïmplementeerd als een zelfstandige virtuele machine of virtuele-machine schaal sets op Azure zonder extra implementatie en beheer van HGS. Alle vertrouwde start functies kunnen worden ingeschakeld met een eenvoudige wijziging in de implementatie code of een selectie vakje op het Azure Portal.  

### <a name="how-can-i-convert-existing-vms-to-trusted-launch"></a>Hoe kan ik bestaande Vm's converteren naar een vertrouwde start?
Voor virtuele machines van de tweede generatie is het migratie traject dat moet worden geconverteerd naar een vertrouwde start, gericht op algemene Beschik baarheid (GA).

## <a name="next-steps"></a>Volgende stappen

Implementeer een [vertrouwde start-VM met behulp van de portal](trusted-launch-portal.md).