---
title: Een gekoppelde service beveiligen
description: Meer informatie over het inrichten en beveiligen van een gekoppelde service met beheerde VNet
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: fb3c7dd7b3d7e8e42e230eedcd0d1b43af104327
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452524"
---
# <a name="secure-a-linked-service-with-private-links"></a>Een gekoppelde service beveiligen met persoonlijke koppelingen

In dit artikel leert u hoe u een gekoppelde service in Synapse kunt beveiligen met een persoonlijk eind punt.

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: als u nog geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* **Azure Storage account**: u Azure data Lake gen 2 gebruiken als een *brongegevens* opslag. Als u geen opslag account hebt, raadpleegt u [een Azure Storage-account maken](../../storage/blobs/data-lake-storage-quickstart-create-account.md) om er een te maken. Zorg ervoor dat het opslag account de Synapse Studio-IP-filtering heeft voor toegang tot de service en dat u alleen **geselecteerde netwerken** toegang wilt geven tot het opslag account. De instelling onder de Blade **firewalls en virtuele netwerken** moet er ongeveer uitzien als in de onderstaande afbeelding.

![Account voor beveiligde opslag](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Een gekoppelde service maken met persoonlijke koppelingen

In Azure Synapse Analytics definieert u de verbindingsgegevens voor andere services in een gekoppelde service. In deze sectie voegt u Azure Synapse Analytics en Azure Data Lake gen 2 toe als gekoppelde services.

1. Open Azure Synapse Studio en ga naar het tabblad **beheren** .
1. Onder **externe verbindingen** selecteert u **gekoppelde services**.
1. Selecteer **Nieuw** om een gekoppelde service toe te voegen.
1. Selecteer de tegel Azure Data Lake Storage Gen2 in de lijst en selecteer **door gaan**.
1. Zorg ervoor dat u **Interactieve creatie** inschakelt. Het kan ongeveer 1 minuut duren voordat deze is ingeschakeld. 
1. Voer uw verificatie referenties in. Account sleutel, Service-Principal en beheerde identiteit worden momenteel ondersteunde verificatie typen. Selecteer verbinding testen om te controleren of uw referenties correct zijn.
1. Selecteer **verbinding testen**, de fout moet mislukken omdat het opslag account geen toegang tot de server inschakelt zonder een persoonlijk eind punt te maken en goed te keuren. In het fout bericht wordt een koppeling weer gegeven om een **persoonlijk eind punt** te maken dat u kunt volgen om naar het volgende deel te gaan. Sla het volgende gedeelte over als u deze koppeling volgt.
1. Selecteer **Maken** nadat dit is voltooid.

## <a name="create-a-managed-private-endpoint"></a>Een beheerd privé-eindpunt maken

Als u de Hyper link niet hebt geselecteerd bij het testen van de bovenstaande verbinding, volgt u het volgende pad. Maak een beheerd privé-eind punt dat u maakt om verbinding te maken met de gekoppelde service die hierboven is gemaakt.

1. Ga naar het tabblad **Beheren**.
1. Ga naar de sectie **beheerde virtuele netwerken** .
1. Selecteer **+ Nieuw** onder beheerd persoonlijk eind punt.
1. Selecteer de tegel Azure Data Lake Storage Gen2 in de lijst en selecteer **door gaan**.
1. Voer de naam van het opslagaccount in dat u hierboven hebt gemaakt.
1. Selecteer **Maken**
1. Na enkele seconden wachten moet u zien dat de privékoppeling een goedkeuring vereist.

## <a name="private-link-approval"></a>Goed keuring van persoonlijke koppelingen
1. Selecteer het privé-eindpunt dat u hierboven hebt gemaakt. U kunt een Hyper link zien waarmee u het persoonlijke eind punt kunt goed keuren op het niveau van het opslag account. *U kunt ook rechtstreeks naar het Azure Portal Storage-account gaan en naar de Blade **persoonlijke eindpunt verbindingen** gaan.*
1. Tik het persoonlijke eind punt dat u hebt gemaakt in de studio en selecteer **goed keuren**.
1. Een beschrijving toevoegen en **Ja** selecteren
1. Ga terug naar Synapse Studio in het gedeelte **beheerde virtuele netwerken** van het tabblad **beheren** .
1. Het duurt ongeveer 1 minuut voordat de goed keuring voor uw persoonlijke eind punt wordt weer gegeven.

## <a name="check-the-connection-works"></a>Controleer of de verbinding werkt
1. Ga naar het tabblad **beheren** en selecteer de gekoppelde service die u hebt gemaakt.
1. Zorg ervoor dat **interactieve ontwerpen** actief is.
1. Selecteer **Verbinding testen**. U ziet dat de verbinding slaagt.

U hebt nu een beveiligde en particuliere verbinding tot stand gebracht tussen Synapse en uw gekoppelde service.

## <a name="next-steps"></a>Volgende stappen


Zie [beheerde persoonlijke eind punten](../security/synapse-workspace-managed-private-endpoints.md)voor meer informatie over het beheerde persoonlijke eind punt in azure Synapse Analytics.


Zie voor meer informatie over gegevens integratie voor Azure Synapse Analytics de [opname gegevens in een Data Lake](data-integration-data-lake.md) -artikel.
