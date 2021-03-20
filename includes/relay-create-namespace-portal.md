---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 500db9f6d299ea81b1f1b1be864df5d91ba1eae7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76021108"
---
1. Meld u aan bij de [Azure-portal][Azure portal].
1. Selecteer **Een resource maken**. Selecteer vervolgens **Integratie** > **Relay**. Als u **Relay** niet in de lijst ziet staan, selecteert u rechtsboven **Alles weergeven**.
1. Selecteer **Maken** en voer een naam voor de naamruimte in het veld **Naam** in. Er wordt in de Azure-portal gecontroleerd of de naam beschikbaar is.
1. Kies een Azure-abonnement waarin u de naamruimte wilt maken.
1. Kies bij [Resourcegroep](../articles/azure-resource-manager/management/manage-resource-groups-portal.md) een bestaande resourcegroep waarin de naamruimte moet worden geplaatst of maak een nieuwe resourcegroep.  
1. Kies het land of de regio waarin uw naamruimte moet worden gehost.

    ![Een naamruimte maken][create-namespace]

1. Selecteer **Maken**. Uw naamruimte wordt in de Azure-portal gemaakt en ingeschakeld. Na enkele minuten stelt het systeem resources ter beschikking voor uw account.

### <a name="get-management-credentials"></a>Beheerreferenties ophalen

1. Selecteer **Alle resources** en kies vervolgens de zojuist gemaakte naam voor de naamruimte.
1. Selecteer **Beleid voor gedeelde toegang**.  
1. Selecteer onder **Beleid voor gedeelde toegang** de optie **RootManageSharedAccessKey**.
1. Onder **SAS-beleid: RootManageSharedAccessKey** selecteert u de knop **Kopiëren** naast **Primaire verbindingsreeks**. Met deze actie kopieert u de verbindingsreeks naar het Klembord voor later gebruik. Plak deze waarde in Kladblok of een andere tijdelijke locatie.
1. Herhaal de vorige stap om de waarde voor de **Primaire sleutel** te kopiëren en plakken naar een tijdelijke locatie zodat u deze later kunt gebruiken.  

    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
