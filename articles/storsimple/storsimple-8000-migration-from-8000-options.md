---
title: Opties voor gegevens migratie van StorSimple 8000 Series-apparaten
description: Hierin wordt een overzicht gegeven van de opties voor het migreren van gegevens uit de StorSimple 8000-serie.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 03/25/2020
ms.author: alkohli
ms.openlocfilehash: 4839f8211e678f5fc2fb3572c7eaa545fbee6c6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94961189"
---
# <a name="options-to-migrate-data-from-storsimple-8000-series"></a>Opties voor het migreren van gegevens uit de StorSimple 8000-serie

> [!IMPORTANT]
> Op 31 december 2022 heeft de StorSimple 8000-serie de status van het einde van de ondersteuning bereikt. We raden aan dat klanten van de StorSimple 8000-serie migreren naar een van de alternatieven die in het document worden beschreven.

De StorSimple 8000-serie bereikt het [einde van de ondersteuning](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) in december 2022. De klanten die de StorSimple 8000-serie uitvoeren, hebben de mogelijkheid om een upgrade uit te voeren naar andere hybride services van de eerste partij van Azure. In dit artikel worden de hybride opties voor Azure beschreven die beschikbaar zijn voor het migreren van gegevens.

## <a name="migration-options"></a>Migratieopties

De klanten die gebruikmaken van de StorSimple 8000-serie beschikken over opties van Azure of van derden.

### <a name="azure-options"></a>Azure-opties

#### <a name="migrate-to-azure-file-sync"></a>Migreren naar Azure File Sync

Met deze gloed nieuwe migratie optie kunnen klanten de bestands shares van hun organisatie opslaan in de Azure Files. Deze bestands shares worden vervolgens gecentraliseerd voor on-premises toegang met behulp van Azure File Sync (AFS). AFS kan worden geïmplementeerd op een Windows Server-host. De daad werkelijke gegevens migratie wordt vervolgens uitgevoerd als een host-kopie of met het hulp programma voor migratie.

Ga naar [Azure file sync StorSimple 8100 en 8600](../storage/files/storage-files-migration-storsimple-8000.md)voor meer informatie over het migreren van gegevens naar Azure file sync.

### <a name="third-party-options"></a>Opties van derden

#### <a name="migrate-to-panzura-freedom-nas"></a>Migreren naar Panzura vrijheid NAS

StorSimple 5000-7000 series en StorSimple 8000 Series-klanten kunnen ervoor kiezen om te migreren naar Panzura vrijheid NAS om hun gegevens in azure te blijven gebruiken. De oplossing Panzura vrijheid biedt een NAS-oplossing die data centers, kant oren, open bare en persoonlijke Clouds omvat. De oplossing maakt lokale, hybride en in-Cloud gegevens werk stromen mogelijk voor NFS-, SMB-en mobiele clients.

Deze migratie wordt ondersteund door Panzura en klanten kunnen aan de slag gaan door de ondersteuning voor migratie van de [Panzura-website](https://panzura.com/migrate-storsimple-panzura/)aan te vragen.

#### <a name="migrate-to-nasuni"></a>Migreren naar Nasuni

Het is eenvoudig om uw hele StorSimple-omgeving te verplaatsen naar een stabiel, veilig platform voor bestands Services met hoge prestaties en Nasuni. Nasuni biedt de beveiliging en prestaties van on-premises bestands opslag en combineert deze met de schaal baarheid en duurzaamheid van Azure.  Als een toonaangevende Azure Independent Software Vendor (ISV) biedt Nasuni alle hulpprogram ma's die nodig zijn om uw StorSimple-gegevens te verplaatsen naar een modern platform waarmee u uw bestanden op meerdere locaties kunt delen en samen werken.

Ga vandaag nog aan de slag: [Nasuni-website](https://info.nasuni.com/storsimple8000-webinar).

<!-- 04/09/2020 v-grpr (priestlg) - As per request, commenting out this section because the information that will go into this section is forthcoming
#### Migrate to Cohesity

Cohesity enables you to migrate data from your current StorSimple 5000–7000 to the Cohesity Data Platform on Azure. The Cohesity Data Platform is a software-defined web-scale solution that consolidates files, backups, objects, and VMs onto a single cloud-native solution. After migration to the Data Platform, you can manage, protect, and provision data and apps from cloud to core through a single pane of glass. With Cohesity, start with as few as three nodes. 

Learn more on [migration to the Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### Migrate to Nasuni

Nasuni makes it easy for StorSimple 5000-7000 customers to migrate and keep their data in Azure.  Nasuni is a leading Azure-based NAS storage solution, giving customers the performance and security they expect from on-prem solutions, with cloud economics and scale.  In addition to high performance file storage, Nasuni and Azure handle backup and DR, while allowing you to share and collaborate on your data around the globe with centralized file storage management. 

Nasuni has the experience to make your migration easy – get started today: https://info.nasuni.com/nasuni-storsimple-migration

#### Migrate to Talon FAST

Talon makes it easy for StorSimple 5000-7000 customers to continue to leverage the benefits they valued so much in the StorSimple platform (small on-site footprint backed by unlimited cloud resources) with even greater function.  With the Talon FAST solution, customers can migrate and keep their data in Azure, while now having an even smaller software-only onsite footprint and adding benefits such as global file locking, global namespace, and multi-site collaboration.  Talon is a leading Azure ecosystem solution, working with global customers to migrate their on-premises file server workloads into a consolidated, Azure-based footprint without compromising user workflow or experience.  

Learn more about how to evolve to a cloud-consolidated enterprise at https://www.talonstorage.com/alliances/microsoft-storsimple.
-->

## <a name="migration---frequently-asked-questions"></a>Migratie: veelgestelde vragen

### <a name="q-when-do-the-storsimple-8000-series-devices-reach-end-of-service"></a>V. Wanneer zijn de StorSimple 8000 Series-apparaten end of service bereikt?

A. StorSimple 8000-serie is een bereik [van de ondersteuning](https://support.microsoft.com/[lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) in december 2022. Het einde van de ondersteuning impliceert dat micro soft geen ondersteuning meer kan bieden voor hardware en software van deze apparaten na december 2022. We raden u ten zeerste aan om te beginnen met het formuleren van een plan om de gegevens van uw apparaten nu te migreren.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>V. Wat gebeurt er met de gegevens die ik in azure heb opgeslagen?  

A. U kunt de gegevens in azure blijven gebruiken nadat u deze naar een nieuwere service hebt gemigreerd.

### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>V. Wat gebeurt er met de gegevens die ik lokaal heb opgeslagen op mijn StorSimple-apparaat?

A. De gegevens die zich op het lokale apparaat bevindt, kunnen naar de nieuwere service worden gekopieerd, zoals beschreven in de migratie documenten.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-8000-series-appliance"></a>V. Wat gebeurt er als ik mijn StorSimple 8000-serie apparaat wil blijven gebruiken?

A. Hoewel de services kunnen blijven werken, kan micro soft geen hardware-en software ondersteuning meer bieden. Migratie wordt ten zeerste aanbevolen voor bedrijfs continuïteit.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-8000-series-devices"></a>V. Welke opties zijn beschikbaar voor het migreren van gegevens van de StorSimple 8000 Series-apparaten?

A. Afhankelijk van het scenario, hebben gebruikers van de StorSimple 8000-serie de volgende migratie opties:

* **Migreren naar Azure file sync**: gebruik deze optie als u wilt overschakelen naar de native Azure-indeling. U kunt Azure File Sync gebruiken voor gecentraliseerd beheer van bestands shares.

* **Andere opties**: u kunt contact opnemen met Microsoft ondersteuning voor het bespreken van migratie opties die hier niet worden vermeld.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>V. Wordt de migratie naar andere opslag oplossingen ondersteund?

A. Ja. Migratie naar andere opslag oplossingen met behulp van de kopie van de gegevens wordt ondersteund.

### <a name="q-is-migration-supported-by-microsoft"></a>V. Wordt migratie ondersteund door micro soft?

A. De migratie van de 8000-serie is een volledig ondersteunde bewerking. Micro soft raadt u aan om ondersteuning te bereiken voordat u de migratie start. Migratie is momenteel een ondersteunings bewerking. [Neem contact op met de ondersteuning van StorSimple](mailto:storsimp@microsoft.com)als u van plan bent om gegevens te migreren van uw StorSimple 8000 Series-apparaat.

### <a name="q-what-is-the-pricing-model-for-migration-to-azure-file-sync"></a>V. Wat is het prijs model voor migratie naar Azure File Sync?

A. Wanneer u Azure File Sync gebruikt, kunnen de abonnements kosten voor de service van toepassing zijn. Klanten moeten ook doorlopende opslag kosten betalen. Raadpleeg de [AFS-prijzen]( https://azure.microsoft.com/pricing/details/storage/files/) voor een schatting.

### <a name="q-how-long-does-it-take-to-complete-a-migration"></a>V. Hoe lang duurt het om een migratie te volt ooien?

A. De tijd voor het migreren van gegevens is afhankelijk van de hoeveelheid gegevens en de geselecteerde upgrade-optie.

## <a name="next-steps"></a>Volgende stappen

* [Gegevens migreren van een StorSimple 8000-serie naar Azure File Sync](../storage/files/storage-files-migration-storsimple-8000.md)