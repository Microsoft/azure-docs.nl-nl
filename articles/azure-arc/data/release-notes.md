---
title: Data Services van Azure Arc enabled-opmerkingen bij de release
description: Nieuwste release opmerkingen
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/02/2021
ms.topic: conceptual
ms.openlocfilehash: 6b4d5c1372a8351f1fe5a6608aff38bf232aabd8
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121946"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Release opmerkingen-Azure Arc ingeschakelde Data Services (preview-versie)

In dit artikel worden mogelijkheden, functies en verbeteringen besproken die recent zijn gepubliceerd of verbeterd voor Azure Arc ingeschakelde gegevens Services. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="february-2021"></a>Februari 2021

### <a name="new-capabilities-and-features"></a>Nieuwe mogelijkheden en functies

Azure data CLI ( `azdata` )-versie nummer: 20.3.1. Downloaden op [https://aka.ms/azdata](https://aka.ms/azdata) . U kunt installeren `azdata` vanuit [Azure data cli ( `azdata` ) installeren](/sql/azdata/install/deploy-install-azdata).

Aanvullende updates zijn onder andere:

- SQL Managed Instance met Azure Arc
   - Hoge Beschik baarheid met AlwaysOn-beschikbaarheids groepen

- Azure Arc enabled PostgreSQL grootschalige Azure Data Studio: 
   - Op de pagina overzicht wordt nu de status weer gegeven van de Server groep die per knoop punt is gespecificeerd
   - Er is nu een nieuwe eigenschappen pagina beschikbaar om meer informatie over de Server groep weer te geven
   - Para meters van de post gres-Engine configureren op de pagina met **knooppunt parameters**

Zie voor problemen met betrekking tot deze release [bekende problemen-Azure Arc enabled Data Services (preview-versie)](known-issues.md)

## <a name="january-2021"></a>Januari 2021

### <a name="new-capabilities-and-features"></a>Nieuwe mogelijkheden en functies

Azure data CLI ( `azdata` )-versie nummer: 20.3.0. Downloaden op [https://aka.ms/azdata](https://aka.ms/azdata) . U kunt installeren `azdata` vanuit [Azure data cli ( `azdata` ) installeren](/sql/azdata/install/deploy-install-azdata).

Aanvullende updates zijn onder andere:
- Gelokaliseerde portal beschikbaar voor 17 nieuwe talen
- Kleine wijzigingen in uitvoeren-systeem eigen. YAML-bestanden
- Nieuwe versies van Grafana en Kibana
- Problemen met python-omgevingen bij het gebruik van azdata in notitie blokken in Azure Data Studio opgelost
- De uitbrei ding pg_audit is nu beschikbaar voor PostgreSQL grootschalige
- Er is geen back-upid meer vereist bij het volledig herstellen van een PostgreSQL grootschalige-data base
- De status (status) wordt gerapporteerd voor elk van de PostgreSQL-instanties die een server groep vormen

   In eerdere releases is de status geaggregeerd op het niveau van de Server groep en niet op het niveau van het PostgreSQL-knoop punt.

- PostgreSQL-implementaties voldoen nu aan de volume grootte parameters die in Create-opdrachten zijn aangegeven
- De engine-versie parameters worden nu nageleefd bij het bewerken van een server groep
- De naam Conventie van het peul voor Azure Arc enabled PostgreSQL grootschalige is gewijzigd

    Het is nu in de vorm: `ServergroupName{c, w}-n` . Bijvoorbeeld een server groep met drie knoop punten, een coördinator knooppunt en twee worker-knoop punten wordt weer gegeven als:
   - `Postgres01c-0` (coördinator knooppunt)
   - `Postgres01w-0` (worker-knoop punt)
   - `Postgres01w-1` (worker-knoop punt)

## <a name="december-2020"></a>December 2020

### <a name="new-capabilities--features"></a>Nieuwe mogelijkheden & functies

Azure data CLI ( `azdata` )-versie nummer: 20.2.5. Downloaden op [https://aka.ms/azdata](https://aka.ms/azdata) .

Bekijk eind punten voor SQL Managed instance en PostgreSQL grootschalige met behulp van de Azure data CLI ( `azdata` ) met `azdata arc sql endpoint list` en `azdata arc postgres endpoint list` opdrachten.

Bewerk aanvragen en limieten voor SQL Managed Instance-bronnen (CPU-kernen en geheugen) met behulp van Azure Data Studio.

Azure Arc enabled PostgreSQL grootschalige ondersteunt nu het herstellen van Point-in-time naast volledige back-ups voor versie 11 en 12 van PostgreSQL. Met de functie voor herstel naar een bepaald tijdstip kunt u een specifieke datum en tijd voor de terugzet bewerking opgeven.

De naam Conventie van het peul voor Azure Arc enabled PostgreSQL grootschalige is gewijzigd. Deze bevindt zich nu in de vorm: ServergroupName {r, s}-_n_. Bijvoorbeeld een server groep met drie knoop punten, een coördinator knooppunt en twee worker-knoop punten wordt weer gegeven als:
- `postgres02r-0` (coördinator knooppunt)
- `postgres02s-0` (worker-knoop punt)
- `postgres02s-1` (worker-knoop punt)

### <a name="breaking-change"></a>Laatste wijziging

#### <a name="new-resource-provider"></a>Nieuwe resource provider

Deze release introduceert een bijgewerkte [resource provider](../../azure-resource-manager/management/azure-services-resource-providers.md) met de naam `Microsoft.AzureArcData` . Voordat u deze functie kunt gebruiken, moet u deze resource provider registreren. 

Deze resource provider registreren: 

1. Selecteer in de Azure Portal **abonnementen** 
2. Kies uw abonnement
3. Onder **instellingen** selecteert u **resource providers** 
4. Zoeken `Microsoft.AzureArcData` en selecteren **registreren** 

U kunt gedetailleerde stappen voor [Azure-resource providers en-typen](../../azure-resource-manager/management/resource-providers-and-types.md)bekijken. Met deze wijziging worden ook alle bestaande Azure-resources verwijderd die u hebt geüpload naar het Azure Portal. Als u de resource provider wilt gebruiken, moet u de gegevens controller bijwerken en de nieuwste CLI gebruiken `azdata` .  

### <a name="platform-release-notes"></a>Opmerkingen bij de release van het platform

#### <a name="direct-connectivity-mode"></a>Modus directe connectiviteit

In deze release wordt de modus voor directe connectiviteit geïntroduceerd. Met de modus directe connectiviteit kan de gegevens controller de gebruiks gegevens automatisch uploaden naar Azure. Als onderdeel van het uploaden van het gebruik wordt de bron Arc data controller automatisch gemaakt in de portal, als deze nog niet is gemaakt via `azdata` Upload.  

U kunt een directe verbinding opgeven wanneer u de gegevens controller maakt. In het volgende voor beeld wordt een gegevens controller gemaakt met de `azdata arc dc create` naam `arc` directe connectiviteits modus ( `connectivity-mode direct` ). Voordat u het voor beeld uitvoert, vervangt u door `<subscription id>` uw abonnements-id.

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group my-resource-group --location eastus --connectivity-mode direct
```

### <a name="known-issues"></a>Bekende problemen

- In azure Kubernetes service (AKS), wordt Kubernetes-versie 1.19. x niet ondersteund.
- Op Kubernetes 1,19 `containerd` wordt niet ondersteund.
- De gegevens controller resource in Azure is momenteel een Azure-resource. Updates zoals verwijderen, worden niet door gegeven aan het kubernetes-cluster.
- Exemplaar namen mogen niet langer zijn dan 13 tekens
- Geen in-place upgrade voor de Azure Arc-gegevens controller of-data base-exemplaren.
- Arc-containerinstallatiekopieën met Data Services worden niet ondertekend.  Mogelijk moet u uw Kubernetes-knooppunten configureren zodat niet-ondertekende containerinstallatiekopieën kunnen worden opgehaald.  Als u bijvoorbeeld docker als container runtime gebruikt, kunt u de omgevings variabele DOCKER_CONTENT_TRUST = 0 instellen en opnieuw starten.  Andere container-runtimes hebben vergelijkbare opties zoals in [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Kan geen door Azure Arc ingeschakelde SQL Managed instances of PostgreSQL grootschalige Server-groepen maken van de Azure Portal.
- Als u nu gebruikmaakt van NFS, moet u `allowRunAsRoot` `true` in uw implementatie profiel bestand instellen voordat u de Azure Arc-gegevens controller maakt.
- Alleen SQL-en PostgreSQL-aanmeldings verificatie.  Er wordt geen ondersteuning geboden voor Azure Active Directory of Active Directory.
- Voor het maken van een gegevens controller voor open Shift zijn beperkte beveiligings beperkingen vereist.  Zie documentatie voor details.
- Als u de engine van Azure Kubernetes service (AKS) op Azure Stack hub met Azure Arc data controller-en data base-instanties gebruikt, wordt het upgraden naar een nieuwere versie van Kubernetes niet ondersteund. Verwijder de Azure Arc-gegevens controller en alle data base-exemplaren voordat u het Kubernetes-cluster bijwerkt.
- AKS-clusters die [meerdere beschikbaarheids zones](../../aks/availability-zones.md) omvatten, worden momenteel niet ondersteund voor Azure Arc-gegevens Services. Als u dit probleem wilt voor komen, moet u, wanneer u het AKS-cluster maakt in Azure Portal, als u een regio selecteert waar zones beschikbaar zijn, alle zones verwijderen uit het selectie besturings element. Zie de volgende afbeelding:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Schakel de selectie vakjes voor elke zone uit om geen op te geven.":::

#### <a name="postgresql"></a>PostgreSQL

- Azure Arc enabled PostgreSQL grootschalige retourneert een onjuist fout bericht wanneer het niet kan worden teruggezet naar het relatieve punt in de tijd dat u opgeeft. Als u bijvoorbeeld een punt in de tijd hebt opgegeven om te herstellen dat ouder is dan uw back-ups bevatten, mislukt de herstel bewerking met een fout bericht als: fout: (404). Reden: niet gevonden. Hoofd tekst van HTTP-antwoord: {"code": 404, "internalStatus": "NOT_FOUND", "reden": "kan de back-up niet herstellen voor de server...}
Wanneer dit het geval is, start u de opdracht opnieuw op nadat u een tijdstip hebt aangegeven dat binnen het bereik van de datums valt waarvoor u back-ups hebt. U kunt dit bereik bepalen door uw back-ups weer te geven en te kijken naar de datums waarop deze zijn gemaakt.
- Herstel naar een bepaald tijdstip wordt alleen ondersteund in verschillende Server groepen. De doel server van een herstel bewerking naar een bepaald tijdstip kan geen server zijn van waaruit u de back-up hebt gemaakt. Dit moet een andere server groep zijn. Volledige herstel bewerking wordt echter wel ondersteund voor dezelfde server groep.
- U moet een back-up-id opgeven wanneer u een volledige herstel bewerking uitvoert. Als u geen back-upid aangeeft, wordt standaard de meest recente back-up gebruikt. Dit werkt niet in deze release.

## <a name="october-2020"></a>Oktober 2020 

Azure data CLI ( `azdata` )-versie nummer: 20.2.3. Downloaden op [https://aka.ms/azdata](https://aka.ms/azdata) .

### <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken

In deze release worden de volgende belang rijke wijzigingen geïntroduceerd: 

* In de PostgreSQL-aangepaste resource definitie (CRD) wordt de naam van de term `shards` gewijzigd in `workers` . Deze term ( `workers` ) komt overeen met de naam van de opdracht regel parameter.

* `azdata arc postgres server delete` Hiermee wordt om bevestiging gevraagd voordat een post gres-exemplaar wordt verwijderd.  Gebruik `--force` om de prompt te overs Laan.

### <a name="additional-changes"></a>Aanvullende wijzigingen

* Er is een nieuwe optionele para meter toegevoegd met de `azdata arc postgres server create` naam `--volume-claim mounts` . De waarde is een door komma's gescheiden lijst met volume claim koppelingen. Een volume claim koppeling is een paar volume type en PVC-naam. Het enige volume type dat op dit moment wordt ondersteund, is `backup` .  In PostgreSQL, wanneer het volume type is `backup` , wordt het PVC gekoppeld aan `/mnt/db-backups` .  Hierdoor kunnen back-ups tussen PostgresSQL-instanties worden gedeeld, zodat de back-up van een PostgresSQL-exemplaar in een ander exemplaar kan worden hersteld.

* Nieuwe korte namen voor aangepaste resource definities voor PostgresSQL: 
  * `pg11` 
  * `pg12`
* De telemetrie-upload biedt gebruikers de volgende mogelijkheden:
   * Aantal punten dat naar Azure is geüpload of 
   * Als er geen gegevens zijn geladen in azure, wordt u gevraagd om het opnieuw te proberen.
* `azdata arc dc debug copy-logs` Lees nu ook uit `/var/opt/controller/log` map en verzamelt postgresql engine-Logboeken in Linux.
*   Een werk indicator weer geven tijdens het maken en herstellen van de back-up met PostgreSQL grootschalige.
* `azdata arc postrgres backup list` bevat nu informatie over de grootte van de back-up.
* De eigenschap admin name van SQL Managed instance is toegevoegd aan de rechter kolom van de Blade overzicht in het Azure Portal.
* Azure Data Studio ondersteunt het configureren van het aantal worker-knoop punten, de vCore en de geheugen instellingen voor PostgreSQL grootschalige. 
* Preview ondersteunt Backup/Restore voor post gres-versie 11 en 12.

## <a name="september-2020"></a>September 2020

Data Services van Azure-Arc is beschikbaar voor open bare preview. Met Arc ingeschakelde gegevens Services kunt u overal gegevens Services beheren.

- SQL Managed Instance
- PostgreSQL Hyperscale

Zie [Wat zijn Azure Arc-gegevens Services?](overview.md) voor instructies.

## <a name="next-steps"></a>Volgende stappen

> **Wilt u gewoon iets uitproberen?**  
> Ga snel aan de slag met [Azure Arc](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) op AKS, AWS Elastic Kubernetes service (EKS), Google Cloud Kubernetes Engine (GKE) of in een Azure-VM.

- [Installeer de client-hulpprogramma's](install-client-tools.md)
- [Maak de Azure Arc gegevenscontroller](create-data-controller.md) (hiervoor moeten eerst de client-hulpprogramma's worden geïnstalleerd)
- [Maak een Azure SQL Managed Instance op Azure Arc](create-sql-managed-instance.md) (hiervoor moet u eerst een Azure Arc-gegevenscontroller maken)
- [Maak een Azure Database for PostgreSQL Hyperscale-servergroep op Azure Arc](create-postgresql-hyperscale-server-group.md) (eerst moet een Azure Arc-gegevenscontroller gemaakt worden)
- [Resource providers for Azure services](../../azure-resource-manager/management/azure-services-resource-providers.md) (Resourceproviders voor Azure-services)
