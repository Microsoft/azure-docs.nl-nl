---
title: Container voor opslag FSLogix-profiel Windows virtueel bureau blad-Azure
description: Opties voor het opslaan van uw FSLogix-profiel voor Windows virtueel bureau blad op Azure Storage.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1f8a82eddfdc7a2a4899c4ee836687df26101bdc
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221888"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Opslag opties voor FSLogix-profiel containers in virtueel bureau blad van Windows

Azure biedt meerdere opslag oplossingen die u kunt gebruiken om uw FSLogix-profiel container op te slaan. In dit artikel worden opslag oplossingen vergeleken die door Azure worden geboden voor FSLogix-gebruikers profiel containers voor Windows-virtueel bureau blad. We raden u aan om FSLogix-profiel containers op Azure Files voor de meeste van onze klanten op te slaan.

Windows Virtual Desktop biedt FSLogix-profiel containers als de aanbevolen gebruikers profiel oplossing. FSLogix is ontworpen om profielen te roamen in omgevingen met externe computers, zoals Windows Virtual Desktop. Bij het aanmelden wordt deze container dynamisch gekoppeld aan de computer omgeving met behulp van een systeem eigen ondersteunde virtuele harde schijf (VHD) en een virtuele harde schijf (VHDX) van Hyper-V. Het gebruikersprofiel is onmiddellijk beschikbaar en wordt in het systeem op dezelfde manier weergegeven als een systeemeigen gebruikersprofiel.

De volgende tabel bevat een vergelijking van de opslag oplossingen Azure Storage aanbiedingen voor FSLogix profiel container gebruikers profielen van Windows-virtueel bureau blad.

## <a name="azure-platform-details"></a>Details van het Azure-platform

|Functies|Azure Files|Azure NetApp Files|Opslagruimten direct|
|--------|-----------|------------------|---------------------|
|Gebruiksvoorbeeld|Algemeen doel|Ultra prestaties of migratie van NetApp on-premises|Platformoverschrijdend|
|Platform service|Ja, Azure-systeem eigen oplossing|Ja, Azure-systeem eigen oplossing|Nee, zelf beheerd|
|Regionale beschikbaarheid|Alle regio's|[Regio's selecteren](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Alle regio's|
|Redundantie|Lokaal redundante/zone-redundante/geo-redundante/geo-zone-redundant|Lokaal redundant|Lokaal redundante/zone-redundant/geo-redundant|
|Lagen en prestaties| Standaard (trans actie geoptimaliseerd)<br>Premium<br>Maxi maal 100.000 IOPS per share met 10 GBps per share op ongeveer 3 MS latentie|Standard<br>Premium<br>Ultra<br>Tot 320k (16 KB) IOPS met 4,5 GBps per volume bij ongeveer 1 MS-latentie|Standard-HDD: Maxi maal 500 IOPS per schijf limieten<br>Standard-SSD: Maxi maal 4.000 IOPS per schijf limieten<br>Premium-SSD: Maxi maal 20.000 IOPS per schijf limieten<br>Premium-schijven voor Opslagruimten Direct worden aanbevolen|
|Capaciteit|100 TiB per aandeel, Maxi maal 5 PiB per algemeen doel account |100 TiB per volume, Maxi maal 12,5 PiB per abonnement|Maxi maal 32 TiB per schijf|
|Vereiste infra structuur|Minimale share grootte van 1 GiB|Minimale capaciteits pool 4 TiB, minimale volume grootte 100 GiB|Twee Vm's op Azure IaaS (+ Cloudwitness) of ten minste drie Vm's zonder en kosten voor schijven|
|Protocollen|SMB 3.0/2.1, NFSv 4.1 (preview), REST|NFSv3, NFSv 4.1 (preview), SMB 3. x/2. x|NFSv3, NFSv 4.1, SMB 3,1|

## <a name="azure-management-details"></a>Details van Azure-beheer

|Functies|Azure Files|Azure NetApp Files|Opslagruimten direct|
|--------|-----------|------------------|---------------------|
|Access|Cloud, on-premises en hybride (Azure-bestands synchronisatie)|Cloud, on-premises (via ExpressRoute)|Cloud, on-premises|
|Backup|Integratie van Azure backup snap shot|Moment opnamen Azure NetApp Files|Integratie van Azure backup snap shot|
|Beveiliging en naleving|[Alle door Azure ondersteunde certificaten](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO voltooid|[Alle door Azure ondersteunde certificaten](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Integratie van Azure Active Directory|[Systeem eigen Active Directory en Azure Active Directory Domain Services](../storage/files/storage-files-active-directory-overview.md)|[Azure Active Directory Domain Services en systeem eigen Active Directory](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Alleen systeem eigen Active Directory-of Azure Active Directory Domain Services-ondersteuning|

Als u uw opslag methode hebt gekozen, raadpleegt u de [prijzen voor Windows virtueel bureau blad](https://azure.microsoft.com/pricing/details/virtual-desktop/) voor informatie over onze prijs plannen.

## <a name="azure-files-tiers"></a>Azure Files lagen

Azure Files biedt twee verschillende opslag lagen: Premium en Standard. Met deze lagen kunt u de prestaties en kosten van uw bestands shares aanpassen om te voldoen aan de vereisten van uw scenario.

- Premium-bestands shares worden ondersteund door Ssd's (Solid-state drives) en worden geïmplementeerd in het type opslag account van FileStorage. Premium-bestands shares bieden consistente hoge prestaties en lage latentie voor intensieve i/o-werk belastingen op invoer en uitvoer. 

- Standaard bestands shares worden ondersteund door harde schijven (Hdd's) en worden geïmplementeerd in het type opslag account voor algemeen gebruik (versie 2) (GPv2). Standaard bestands shares bieden betrouw bare prestaties voor i/o-workloads die minder gevoelig zijn voor prestatie variaties, zoals bestands shares voor algemene doel einden en ontwikkel-en test omgevingen. Standaard bestands shares zijn alleen beschikbaar in een facturerings model met betalen per gebruik.

De volgende tabel geeft een overzicht van onze aanbevelingen voor de prestatie tier die moet worden gebruikt op basis van uw werk belasting. Deze aanbevelingen helpen u bij het selecteren van de prestatie-laag die voldoet aan uw prestatie doelen, budget en regionale overwegingen. We hebben deze aanbevelingen gebaseerd op de voorbeeld scenario's van [extern bureaublad typen werk belastingen](/windows-server/remote/remote-desktop-services/remote-desktop-workloads). 

| Type werk belasting | Aanbevolen bestands niveau |
|--------|-----------|
| Light (minder dan 200 gebruikers) | Standaardbestandsshares |
| Licht (meer dan 200 gebruikers) | Premium-bestands shares of standaard met meerdere bestands shares |
|Normaal|Premiumbestandsshares|
|Zwaar|Premiumbestandsshares|
|Stroom|Premiumbestandsshares|

Zie [Bestands share en bestands schaal doelen](../storage/files/storage-files-scale-targets.md#azure-files-scale-targets)voor meer informatie over de prestaties van Azure files. Zie [Azure files prijzen](https://azure.microsoft.com/pricing/details/storage/files/)voor meer informatie over prijzen.

## <a name="next-steps"></a>Volgende stappen

Zie de tabel in [FSLogix-profiel containers en Azure files](fslogix-containers-azure-files.md)voor meer informatie over FSLogix-profiel containers, gebruikers profiel schijven en andere gebruikers profiel technologieën.

Als u klaar bent om uw eigen FSLogix-profiel containers te maken, kunt u aan de slag met een van deze zelf studies:

- [Aan de slag met FSLogix-profiel containers op Azure Files in Windows virtueel bureau blad](create-file-share.md)
- [Een FSLogix-profiel container maken voor een hostgroep met Azure NetApp-bestanden](create-fslogix-profile-container.md)
- De instructies [voor het implementeren van een met twee knoop punten opslagruimten direct scale-out Bestands server voor UPD-opslag in azure](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) , zijn ook van toepassing wanneer u een FSLogix-profiel container gebruikt in plaats van een gebruikers profiel schijf

U kunt ook beginnen met het begin en uw eigen virtueel-bureaublad oplossing van Windows instellen bij het [maken van een Tenant in Windows virtueel bureau blad](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).
