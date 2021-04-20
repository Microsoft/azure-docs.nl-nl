---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 958c40ed311ebe8b2a83ce0d4d394d00f48d1afa
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732402"
---
Ga naar het opslagaccount waarvoor u een privé-eindpunt wilt maken. Selecteer in de **inhoudsopgave** voor het opslagaccount **Netwerken,** Privé-eindpuntverbindingen en vervolgens **+** Privé-eindpunt om een nieuw privé-eindpunt te maken. 

[![Een schermopname van de optie Privé-eindpuntverbindingen in de inhoudsopgave van het opslagaccount](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png)](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png#lightbox)

Er wordt een wizard gestart waarin u meerdere pagina's moet invullen.

Selecteer op de blade **Algemeen** de gewenste resourcegroep, geef een naam op en selecteer de regio voor het privé-eindpunt. U kunt hier kiezen wat u wilt. De waarden hoeven niet overeen te komen met die van het opslagaccount. U moet het privé-eindpunt wel maken in dezelfde regio als het virtuele netwerk waarin u het privé-eindpunt wilt maken.

![Een schermopname van het gedeelte Basis in het gedeelte Privé-eindpunt maken](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-1.png)

Schakel op de blade **Resource** het keuzerondje **Verbinding maken met een Azure-resource in mijn directory** in. Selecteer onder **Resourcetype** **Microsoft.Storage/storageAccounts** als het type resource. Geef in het veld **Resource** het opslagaccount op met de Azure-bestandsshare waarmee u verbinding wilt maken. Selecteer voor Subresource van doel de waarde **bestand**, aangezien dit voor Azure Files is.

Op de blade **Configuratie** kunt u het specifieke virtuele netwerk en het subnet selecteren waaraan u het privé-eindpunt wilt toevoegen. U moet een ander subnet selecteren dan het subnet waaraan u hierboven het service-eindpunt hebt toegevoegd. De blade Configuratie bevat ook de gegevens voor het maken/bijwerken van de privé-DNS-zone. We adviseren om de standaardzone `privatelink.file.core.windows.net` te gebruiken.

![Een schermopname van de sectie Configuratie](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-2.png)

Klik op **Beoordelen en maken** om het privé-eindpunt te maken. 
