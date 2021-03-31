---
title: Azure Data Studio gebruiken om uw PostgreSQL-exemplaar te beheren
description: Azure Data Studio gebruiken om uw PostgreSQL-exemplaar te beheren
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7dcc0f916a15598060e034dcf62536ee13e2672e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92320240"
---
# <a name="use-azure-data-studio-to-manage-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Data Studio gebruiken voor het beheren van uw PostgreSQL grootschalige-Server groep voor Azure-Arc


In dit artikel wordt beschreven hoe u:
- uw PostgreSQL-instanties beheren met Dashboard weergaven als overzicht, verbindings reeksen, eigenschappen Resource Health...
- werken met uw gegevens en schema

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Vereisten

- [Azdata, Azure Data Studio en Azure CLI installeren](install-client-tools.md)
- Installeren in Azure Data Studio de- **[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]** en **Azure Arc** -en **postgresql** -extensies
- De [Azure Arc-gegevens controller](create-data-controller-using-azdata.md) maken
- Azure Data Studio starten

## <a name="connect-to-the-azure-arc-data-controller"></a>Verbinding maken met de Azure-Arc-gegevens controller

Vouw in Azure Data Studio het knoop punt **Azure-Arc-controllers** uit en selecteer de knop **controller verbinden** :

Voer de verbindings gegevens in voor uw Azure data-controller:

- **Controller-URL:**

    De URL waarmee verbinding moet worden gemaakt met uw controller in Kubernetes. U hebt het volgende ingevoerd in de vorm van `https://<IP_address_of_the_controller>:<Kubernetes_port.` bijvoorbeeld:

    ```console
    https://12.345.67.890:30080
    ```
- **Gebruikersnaam:**

    De naam van het gebruikers account dat u gebruikt om verbinding te maken met de controller. Gebruik de naam die u doorgaans gebruikt wanneer u uitvoert `azdata login` . Het is niet de naam van de PostgreSQL-gebruiker die u gebruikt om verbinding te maken met de PostgreSQL-data base-engine, doorgaans van psql.
- **Wacht woord:** Het wacht woord van het gebruikers account dat u gebruikt om verbinding te maken met de controller


In azure Data Studio wordt uw Arc-gegevens controller weer gegeven. Uitvouwen en de lijst met PostgreSQL-instanties die worden beheerd wordt weer gegeven.

## <a name="manage-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Uw Azure-PostgreSQL grootschalige-Server groepen beheren

Klik met de rechter muisknop op het PostgreSQL-exemplaar dat u wilt beheren en selecteer [beheren]

De PostgreSQL-dashboard weergave:

Deze bevat een aantal Dash boards die aan de linkerkant van het deel venster worden weer gegeven:

- **Overzicht:** Geeft samenvattings informatie weer over uw exemplaar zoals naam, PostgreSQL admin-gebruikers naam, ID van het Azure-abonnement, configuratie, versie van de data base-engine, eind punten voor Grafana en Kibana...
- **Verbindings reeksen:** Geeft verschillende verbindings reeksen weer die u mogelijk nodig hebt om verbinding te maken met uw PostgreSQL-instantie zoals psql, Node.js, PHP, Ruby...
- **Problemen vaststellen en oplossen:** Is de landings pagina waar u verschillende bronnen vindt die u kunnen helpen bij het oplossen van uw exemplaar bij het uitpakken van de notitie blokken voor probleem oplossing
- **Nieuwe ondersteunings aanvraag:** Is de landings pagina van waaruit u hulp kunt aanvragen via onze ondersteunings services die open bare preview-aankondiging starten.

## <a name="work-with-your-data-and-schema"></a>Werken met uw gegevens en schema

Vouw de knooppunt **servers** aan de linkerkant van het Azure Data Studio-venster uit:

En selecteer [verbinding toevoegen] en vul de verbindings gegevens in voor uw PostgreSQL-instantie:
- **Verbindings type:** PostgreSQL
- **Server naam:** Voer de naam in van uw postgresql-exemplaar. Bijvoorbeeld: postgres01
- **Verificatie type:** Wacht woord
- **Gebruikers naam:** u kunt bijvoorbeeld de gebruikers naam standaard/standaard postgresql-beheerder gebruiken. Opmerking: dit veld is hoofdletter gevoelig.
- **Wacht woord:** u vindt het wacht woord van de postgresql-gebruikers naam in de psql-Connection String in de uitvoer van de `azdata postgres server endpoint -n postgres01` opdracht
- **Database naam:** Stel de naam in van de data base waarmee u verbinding wilt maken. U kunt dit instellen op __standaard__
- **Server groep:** u kunt deze instellen op __standaard__
- **Naam (optioneel):** u kunt dit veld leeg laten
- **Gevanceerde**
    - **IP-adres van host:** is het open bare IP-adres van het Kubernetes-cluster
    - **Poort:** is de poort waarop uw postgresql-exemplaar luistert. U vindt deze poort aan het einde van de psql-connection string in de uitvoer van de `azdata postgres server endpoint -n postgres01` opdracht. Niet poort 30080 waarop Kubernetes luistert en die u hebt ingevoerd bij het maken van verbinding met de Azure-gegevens controller in Azure Data Studio.
    - **Andere para meters:** Ze moeten Self-Explicit zijn, u kunt live met de standaard/lege waarden die worden weer gegeven met.

Selecteer **[OK] en [verbinding]** om verbinding te maken met uw server.

Zodra de verbinding is gemaakt, zijn verschillende ervaringen beschikbaar:
- **Nieuwe query**
- **Nieuw notitie blok**
- **De weer gave van uw server uitbreiden en bladeren/werken met objecten in uw data base**
- **...**

## <a name="next-step"></a>Volgende stap
[Uw server groep controleren](monitor-grafana-kibana.md)
