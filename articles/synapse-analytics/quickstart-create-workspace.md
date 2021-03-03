---
title: 'Quickstart: een Synapse-werkruimte maken'
description: Maak een Synapse-werkruimte door de stappen in deze handleiding uit te voeren.
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 09/03/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: d2f2ed99af288b72a4c53f07d3cd7ba8b46974c0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693625"
---
# <a name="quickstart-create-a-synapse-workspace"></a>Quickstart: Een Synapse-werkruimte maken
In deze quickstart wordt stapsgewijs beschreven hoe u een Azure Synapse-werkruimte maakt met Azure Portal.

## <a name="create-a-synapse-workspace"></a>Een Synapse-werkruimte maken

1. Open de [Azure-portal](https://portal.azure.com) en zoek bovenin naar **Synapse**.
1. Selecteer in de zoekresultaten onder **Services** de optie **Azure Synapse Analytics**.
1. Selecteer **Toevoegen** om een werkruimte te maken.
1. Geef op het tabblad **Basisinformatie** een unieke naam voor de werkruimte op. In dit document gebruiken we **myworkspace**
1. U hebt een ADLSGEN2-account nodig om een werkruimte te maken. Het meest eenvoudige is om een nieuw account te maken. Als u een bestaand account opnieuw wilt gebruiken, moet u een aantal extra configuratiestappen uitvoeren. 
1. OPTIE 1 Een nieuw ADLSGEN2-account maken 
    1. Klik bij **Data Lake Storage Gen 2 selecteren** op **Nieuwe maken** en noem dit **contosolake**.
    1. Klik bij **Data Lake Storage Gen 2 selecteren** op **Bestandssysteem** en noem dit **users**.
1. OPTIE 2 Raadpleeg de instructies in **Een opslagaccount voorbereiden** onderaan dit document.
1. De Azure Synapse-werkruimte gebruikt dit opslagaccount als primair opslagaccount en de container als opslag voor werkruimtegegevens. In de werkruimte worden gegevens opgeslagen in Apache Spark-tabellen. Het slaat Apache Spark-toepassingslogboeken op in een map met de naam **/synapse/workspacename**.
1. Selecteer **Beoordelen en maken** > **Maken**. Uw werkruimte is binnen een paar minuten klaar.

> [!NOTE]
> Nadat u de Azure Synapse-werkruimte hebt gemaakt, kunt u de werkruimte niet verplaatsen naar een andere Azure Active Directory-tenant. Als u dit doet via een migratie van abonnementen of een andere actie, verliest u mogelijk de toegang tot de artefacten in de werkruimte.
> Daarnaast kunt u momenteel geen Synapse Analytics-werk ruimte maken in een abonnement voor [Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/csp-overview) .

## <a name="open-synapse-studio"></a>Synapse Studio openen

Wanneer uw Azure Synapse-werkruimte is gemaakt, kunt u Synapse Studio op twee manieren openen:

* Open de Synapse-werkruimte in de [Azure-portal](https://portal.azure.com). Selecteer bovenaan de sectie **Overzicht** de optie **Synapse Studio starten**.
* Ga naar `https://web.azuresynapse.net` en meld u aan bij uw werkruimte.

## <a name="prepare-an-existing-storage-account-for-use-with-azure-synapse-analytics"></a>Een bestaand opslagaccount voorbereiden voor gebruik met Azure Synapse Analytics

1. Open [Azure Portal](https://portal.azure.com).
1. Ga naar een bestaand ADLSGEN2-opslagaccount
1. Selecteer **Toegangsbeheer (IAM)** in het linkerdeelvenster. Wijs vervolgens de volgende rollen toe of zorg ervoor dat deze al zijn toegewezen:
    * Wijs uzelf toe aan de rol **Eigenaar**.
    * Wijs uzelf toe aan de rol **Eigenaar van opslagblobgegevens**.
1. Selecteer in het navigatievenster links de optie **Containers** en maak een container.
1. U kunt de container een willekeurige naam geven. In dit document noemen we de container **users**.
1. Accepteer de standaardinstelling **Openbaar toegangsniveau** en selecteer vervolgens **Maken**.

### <a name="configure-access-to-the-storage-account-from-your-workspace"></a>De toegang tot het opslagaccount vanuit uw werkruimte configureren

Beheerde identiteiten voor uw Azure Synapse-werkruimte hebben mogelijk al toegang tot het opslagaccount. Voer de volgende stappen uit om dat te controleren:

1. Open de [Azure-portal](https://portal.azure.com) en open het primaire opslagaccount dat u hebt gekozen voor uw werkruimte.
1. Selecteer **Toegangsbeheer (IAM)** in het linkerdeelvenster.
1. Wijs vervolgens de volgende rollen toe of controleer of ze al zijn toegewezen. We gebruiken dezelfde naam voor de werkruimte-id en de werkruimtenaam.
    * Wijs voor de rol **Bijdrager voor opslagblobgegevens** in het opslagaccount **myworkspace** toe als werkruimte-id.
    * Wijs **myworkspace** als werkruimtenaam toe.

1. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

* [Een toegewezen SQL-pool maken](quickstart-create-sql-pool-studio.md) 
* [Een serverloze Apache Spark-pool maken](quickstart-create-apache-spark-pool-portal.md)
* [Serverloze SQL-pools gebruiken](quickstart-sql-on-demand.md)
