---
title: Beperkingen van Azure Arc enabled PostgreSQL grootschalige
description: Beperkingen van Azure Arc enabled PostgreSQL grootschalige
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: b1a56c8acf1789690c01f1c16b7c37a237720e39
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100417807"
---
# <a name="limitations-of-azure-arc-enabled-postgresql-hyperscale"></a>Beperkingen van Azure Arc enabled PostgreSQL grootschalige

In dit artikel worden de beperkingen beschreven van Azure Arc enabled PostgreSQL grootschalige. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="backup-and-restore"></a>Back-up en herstel

- Herstel naar een bepaald tijdstip (zoals het herstellen naar een specifieke datum en tijd) naar dezelfde server groep wordt niet ondersteund. Wanneer u een herstel tijdstip uitvoert, moet u herstellen op een andere server groep die u hebt geïmplementeerd voordat u deze herstelt. Na het herstellen naar de nieuwe server groep, kunt u de Server groep van oorsprong verwijderen.
- Het herstellen van de volledige inhoud van een back-up (in tegens telling tot een bepaald punt in de tijd) naar dezelfde server groep wordt ondersteund voor PostgreSQL versie 12. Het wordt niet ondersteund voor PostgreSQL versie 11 vanwege een beperking van de PostgreSQL-engine met tijd lijnen. Als u de volledige inhoud van een back-up voor een PostgreSQL-Server groep van versie 11 wilt herstellen, moet u deze herstellen naar een andere server groep.


## <a name="databases"></a>Databases

Het hosten van meer dan één data base in een server groep wordt niet ondersteund.


## <a name="security"></a>Beveiliging

Het beheren van gebruikers en rollen wordt niet ondersteund. Ga nu verder met het gebruik van de post gres-standaard gebruiker.

## <a name="roles-and-responsibilities"></a>Rollen en verantwoordelijkheden

De rollen en verantwoordelijkheden tussen micro soft en haar klanten verschillen van Azure PaaS Services (platform as A Service) en Azure Hybrid (zoals Azure Arc enabled PostgreSQL grootschalige). 

### <a name="frequently-asked-questions"></a>Veelgestelde vragen

De onderstaande tabel bevat een overzicht van de antwoorden op veelgestelde vragen over de ondersteunings rollen en-verantwoordelijkheden.

| Vraag                      | Azure-platform als een service (PaaS) | Hybride services van Azure Arc |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| Wie levert de infra structuur?  | Microsoft                          | Klant                  |
| Wie levert de software? *       | Microsoft                          | Microsoft                 |
| Wie doet de bewerkingen? | Microsoft                          | Klant                  |
| Biedt micro soft Sla's?      | Ja                                | Nee                        |
| Wie is er verantwoordelijk voor de Sla's? | Microsoft                          | Klant                  |

\* Azure-Services

__Waarom biedt micro soft geen Sla's voor hybride services van Azure Arc?__ Omdat micro soft niet de eigenaar van de infra structuur is en deze niet werkt. Klanten doen dat.

## <a name="next-steps"></a>Volgende stappen

- **Probeer het uit.** Ga snel aan de slag met [Azure Arc](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) direct op Azure Kubernetes service (AKS), AWS elastische Kubernetes service (EKS), Google Cloud Kubernetes Engine (GKE) of in een Azure-VM. 

- **Maak uw eigen.** Volg deze stappen voor het maken van uw eigen Kubernetes-cluster: 
   1. [Installeer de client-hulpprogramma's](install-client-tools.md)
   2. [De Azure Arc-gegevens controller maken](create-data-controller.md)
   3. [Een Azure Database for PostgreSQL grootschalige-Server groep maken op Azure Arc](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Meer informatie over Azure Arc enabled Data Services](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Meer informatie over Azure Arc](https://aka.ms/azurearc)
