---
title: Gegevenslocatie
description: Gegevens locatie en informatie over Azure Arc-servers.
ms.topic: reference
ms.date: 02/16/2021
ms.custom: references_regions
ms.openlocfilehash: d4764772473bbf2e5aafe2607a9462c9a6a15203
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100559509"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Servers die geschikt zijn voor Azure-Arc: data locatie

In dit artikel wordt het concept van gegevens locatie uitgelegd en hoe dit van toepassing is op servers met Azure Arc.

Servers met Azure Arc is **[beschikbaar](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** in de **Verenigde Staten, Europa, het Verenigd Konink rijk, Australië en Azië en Stille Oceaan**.

## <a name="data-residency"></a>Gegevenslocatie

Azure Arc enabled-servers slaan configuratie-instellingen voor [Azure VM-extensies](manage-vm-extensions.md) (dat wil zeggen, eigenschaps waarden) die voor de uitbrei ding moeten worden opgegeven voordat ze op de verbonden computer proberen in te scha kelen. Wanneer u bijvoorbeeld de VM-extensie Log Analytics inschakelt, wordt u gevraagd om de Log Analytics **werk ruimte-id** en de **primaire sleutel**.

Er wordt ook informatie over de meta gegevens van de verbonden computer verzameld. Met name:

* Naam, type en versie van het besturings systeem
* Computernaam
* Volledig gekwalificeerde domeinnaam (FQDN) van de computer
* Versie van Connected Machine-agent
* Active Directory-en DNS-Fully Qualified Domain Name (FQDN)
* UUID (BIOS-ID)
* Heartbeat van verbonden machine-agent
* Versie van Connected Machine-agent
* Open bare sleutel voor beheerde identiteit
* Status en Details van beleids naleving (als Azure Policy gast configuratie beleid wordt gebruikt)

Met Arc ingeschakelde servers kunt u de regio opgeven waar uw gegevens worden opgeslagen. Micro soft kan repliceren naar andere regio's voor gegevens tolerantie, maar micro soft repliceert of verplaatst geen gegevens buiten de geografie. Deze gegevens worden opgeslagen in de regio waar de Azure Arc machine-resource is geconfigureerd. Als de computer bijvoorbeeld is geregistreerd met de Arc in de regio VS-Oost, worden deze gegevens opgeslagen in de regio vs.

Zie voor meer informatie over onze regionale tolerantie en nalevings ondersteuning [Azure geografie](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het ontwerpen van [Azure-tolerantie](/azure/architecture/reliability/architect).
