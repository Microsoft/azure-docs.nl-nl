---
title: Een gekoppelde service beveiligen
description: Meer informatie over het inrichten en beveiligen van een gekoppelde service met een beheerd VNet
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 6be76878a9a07c5f4a1e2a9348bb7b09cb1b10eb
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567584"
---
# <a name="secure-a-linked-service-with-private-links"></a>Een gekoppelde service beveiligen met privékoppelingen

In dit artikel leert u hoe u een gekoppelde service in Synapse beveiligt met een privé-eindpunt.

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement:** als u geen Azure-abonnement hebt, maakt u een gratis [Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* **Azure Storage:** u gebruikt Azure Data Lake Gen 2 als brongegevensopslag.  Als u geen opslagaccount hebt, zie Een opslagaccount [maken Azure Storage stappen](../../storage/common/storage-account-create.md) om er een te maken. Zorg ervoor dat het opslagaccount beschikt Synapse Studio IP-filtering voor toegang  en dat u alleen geselecteerde netwerken toegang tot het opslagaccount geeft. De instelling onder de blade **Firewalls en virtuele netwerken** moet er uitzien zoals in de onderstaande afbeelding.

![Beveiligd opslagaccount](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Een gekoppelde service maken met privékoppelingen

In Azure Synapse Analytics definieert u de verbindingsgegevens voor andere services in een gekoppelde service. In deze sectie voegt u een Azure Synapse Analytics Azure Data Lake Gen 2 toe als gekoppelde services.

1. Open Azure Synapse Studio en ga naar het **tabblad** Beheren.
1. Selecteer **onder Externe verbindingen** de optie **Gekoppelde services.**
1. Selecteer **Nieuw** om een gekoppelde service toe te voegen.
1. Selecteer de Azure Data Lake Storage Gen2 in de lijst en selecteer **Doorgaan.**
1. Zorg ervoor dat u **Interactieve creatie** inschakelt. Het kan ongeveer 1 minuut duren voordat deze is ingeschakeld. 
1. Voer uw verificatiereferenties in. Accountsleutel, service-principal en beheerde identiteit worden momenteel ondersteunde verificatietypen. Selecteer Verbinding testen om te controleren of uw referenties juist zijn.
1. Selecteer **Verbinding testen.** Dit zou moeten mislukken omdat het opslagaccount geen toegang tot de verbinding inschakelen zonder het maken en goedkeuren van een privé-eindpunt. In het foutbericht ziet u een koppeling om een privé-eindpunt **te** maken dat u kunt volgen om naar het volgende deel te gaan. Als u deze koppeling volgt, slaat u het volgende deel over.
1. Selecteer **Maken** nadat dit is voltooid.

## <a name="create-a-managed-private-endpoint"></a>Een beheerd privé-eindpunt maken

Als u niet hebt geselecteerd in de hyperlink tijdens het testen van de bovenstaande verbinding, volgt u het volgende pad. Maak een beheerd privé-eindpunt dat u verbindt met de gekoppelde service die hierboven is gemaakt.

1. Ga naar het tabblad **Beheren**.
1. Ga naar de **sectie Beheerde virtuele** netwerken.
1. Selecteer **+ Nieuw** onder Beheerd privé-eindpunt.
1. Selecteer de Azure Data Lake Storage Gen2 in de lijst en selecteer **Doorgaan.**
1. Voer de naam van het opslagaccount in dat u hierboven hebt gemaakt.
1. Selecteer **Maken**
1. Na enkele seconden wachten moet u zien dat de privékoppeling een goedkeuring vereist.

## <a name="private-link-approval"></a>Goedkeuring van private link
1. Selecteer het privé-eindpunt dat u hierboven hebt gemaakt. U ziet een hyperlink die u in staat maakt om het privé-eindpunt goed te keuren op het niveau van het opslagaccount. *U kunt ook rechtstreeks naar het opslagaccount Azure Portal gaan en naar de blade Verbindingen **met privé-eindpunt gaan.***
1. Tik op het privé-eindpunt dat u in studio hebt gemaakt en selecteer **Goedkeuren.**
1. Voeg een beschrijving toe en selecteer **Ja**
1. Terug u Synapse Studio in onder de sectie **Beheerde virtuele** netwerken van het **tabblad** Beheren.
1. Het duurt ongeveer 1 minuut om de goedkeuring voor uw privé-eindpunt weer te geven.

## <a name="check-the-connection-works"></a>Controleer of de verbinding werkt
1. Ga naar het **tabblad Beheren** en selecteer de gekoppelde service die u hebt gemaakt.
1. Zorg ervoor dat **Interactief schrijven** actief is.
1. Selecteer **Verbinding testen**. Als het goed is, ziet u dat de verbinding is geslaagd.

U hebt nu een beveiligde en privéverbinding tot stand gebracht tussen Synapse en uw gekoppelde service.

## <a name="next-steps"></a>Volgende stappen


Zie Beheerde privé-eindpunten voor meer informatie over beheerde privé-eindpunten in [Azure Synapse Analytics.](../security/synapse-workspace-managed-private-endpoints.md)


Zie het artikel Gegevens opnemen in Azure Synapse Analytics Data Lake voor meer informatie over gegevensintegratie voor een data [lake.](data-integration-data-lake.md)