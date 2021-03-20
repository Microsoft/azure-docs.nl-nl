---
title: Afschaffing van Azure Site Recovery-functie voor gegevens versleuteling | Microsoft Docs
description: Details regarig Azure Site Recovery gegevens versleutelings functie
services: site-recovery
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: sharrai
ms.openlocfilehash: 5860928d71c0e7431190908d5df5d7496e8ffb17
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89426313"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>Afschaffing van Site Recovery-functie voor gegevens versleuteling

In dit document worden de details van de afschaffing en de herstel actie beschreven die u moet uitvoeren als u de functie voor gegevens versleuteling van Site Recovery gebruikt tijdens het configureren van herstel na nood geval voor virtuele Hyper-V-machines naar Azure. 

## <a name="deprecation-information"></a>Gegevens van afschaffing


De functie voor gegevens versleuteling van Site Recovery is beschikbaar voor klanten die virtuele Hyper-V-machines beveiligen om ervoor te zorgen dat de gerepliceerde gegevens worden beschermd tegen beveiligings Risico's. deze functie wordt op **30 April 2022** afgeschaft. Het wordt vervangen door de geavanceerde functie [versleuteling op rest](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) , die gebruikmaakt van [Storage service Encryption](../storage/common/storage-service-encryption.md) (SSE). Met SSE worden gegevens versleuteld voordat ze persistent worden gemaakt en ontsleuteld op het moment dat ze worden opgehaald. bij een failover naar Azure worden uw Vm's uitgevoerd vanaf de versleutelde opslag accounts, waardoor een verbeterde herstel tijd (RTO) mogelijk is.

Als u een bestaande klant bent die gebruikmaakt van deze functie, hebt u communicatie ontvangen met de details van de afschaffing en de herstels tappen. 


## <a name="what-are-the-implications"></a>Wat zijn de gevolgen?

Na **30 April 2022** kunnen vm's die nog steeds gebruikmaken van de buiten gebruik gestelde versleutelings functie, geen failover uitvoeren. 

## <a name="required-action"></a>Vereiste actie
Voer de volgende stappen uit om geslaagde failover-bewerkingen voort te zetten:

Volg deze stappen voor elke virtuele machine: 
1.  [Schakel replicatie uit](./site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).
2.  [Maak een nieuw replicatie beleid](./hyper-v-azure-tutorial.md#set-up-a-replication-policy).
3.  [Schakel replicatie in](./hyper-v-vmm-azure-tutorial.md#enable-replication) en selecteer een opslag account waarvoor SSE is ingeschakeld.

Na het volt ooien van de initiële replicatie naar opslag accounts waarvoor SSE is ingeschakeld, wordt voor uw virtuele machines versleuteling in rust gebruikt met Azure Site Recovery.


## <a name="next-steps"></a>Volgende stappen
Plan voor het uitvoeren van de herstels tappen en voer deze zo snel mogelijk uit. Als u query's hebt over deze afschaffing, neemt u contact op met Microsoft Ondersteuning. Zie [hier](hyper-v-vmm-architecture.md)voor meer informatie over Hyper-V naar Azure scenario.
