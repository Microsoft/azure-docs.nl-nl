---
title: Gegevens services die beschikbaar zijn voor Azure Arc-bekende problemen
description: Meest recente bekende problemen
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/02/2021
ms.topic: conceptual
ms.openlocfilehash: ee652047a33d73ece2d7648905fa590d90b1fb2f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029502"
---
# <a name="known-issues---azure-arc-enabled-data-services-preview"></a>Bekende problemen: Azure Arc enabled Data Services (preview-versie)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="march-2021"></a>2021 maart

### <a name="data-controller"></a>Gegevens controller

- U kunt een gegevens controller maken in de modus Direct Connect met de Azure Portal. Implementatie met andere hulpprogram ma's van Azure Arc ingeschakelde gegevens services worden niet ondersteund. U kunt met name geen gegevens controller implementeren in de modus Direct Connect met een van de volgende hulpprogram ma's tijdens deze release.
   - Azure Data Studio
   - Azure Data CLI (`azdata`)
   - Kubernetes systeem eigen hulpprogram ma's

   [Azure Arc data controller implementeren | In de modus direct verbinden](deploy-data-controller-direct-mode.md) wordt uitgelegd hoe u de gegevens controller maakt in de portal. 

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc enabled PostgreSQL grootschalige

- Het is niet mogelijk om een post gres grootschalige-Server groep voor Azure-Arc te implementeren in een Arc-gegevens controller ingeschakeld voor de modus direct verbinden.
- Door een ongeldige waarde door te geven aan de `--extensions` para meter bij het bewerken van de configuratie van een server groep om extra extensies in te scha kelen, wordt de lijst met ingeschakelde uitbrei dingen onjuist teruggezet op de manier waarop de Server groep is gemaakt en wordt voor komen dat gebruikers extra uitbrei dingen kunnen maken. De enige tijdelijke oplossing die beschikbaar is wanneer dat gebeurt, is om de Server groep te verwijderen en opnieuw te implementeren.

## <a name="february-2021"></a>Februari 2021

### <a name="data-controller"></a>Gegevens controller

- De cluster modus Direct Connect is uitgeschakeld

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc enabled PostgreSQL grootschalige

- Herstellen naar een bepaald tijdstip wordt niet ondersteund voor nu op NFS-opslag.
- Het is niet mogelijk om de pg_cron-extensie tegelijkertijd in te scha kelen en te configureren. U moet hiervoor twee opdrachten gebruiken. Eén opdracht om deze in te scha kelen en één opdracht om deze te configureren. 

   Bijvoorbeeld:
   ```console
   § azdata arc postgres server edit -n myservergroup --extensions pg_cron 
   § azdata arc postgres server edit -n myservergroup --engine-settings cron.database_name='postgres'
   ```

   Voor de eerste opdracht moet de Server groep opnieuw worden opgestart. Voordat u de tweede opdracht uitvoert, moet u ervoor zorgen dat de status van de Server groep is overgezet van bijwerken naar gereed. Als u de tweede opdracht uitvoert voordat de computer opnieuw wordt opgestart, mislukt de bewerking. Als dat het geval is, wacht u gewoon even en voert u de tweede opdracht opnieuw uit.

## <a name="introduced-prior-to-february-2021"></a>Vóór februari 2021

### <a name="data-controller"></a>Gegevens controller

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


## <a name="next-steps"></a>Volgende stappen

> **Wilt u gewoon iets uitproberen?**  
> Ga snel aan de slag met [Azure Arc](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) op AKS, AWS Elastic Kubernetes service (EKS), Google Cloud Kubernetes Engine (GKE) of in een Azure-VM.

- [Installeer de client-hulpprogramma's](install-client-tools.md)
- [Maak de Azure Arc gegevenscontroller](create-data-controller.md) (hiervoor moeten eerst de client-hulpprogramma's worden geïnstalleerd)
- [Maak een Azure SQL Managed Instance op Azure Arc](create-sql-managed-instance.md) (hiervoor moet u eerst een Azure Arc-gegevenscontroller maken)
- [Maak een Azure Database for PostgreSQL Hyperscale-servergroep op Azure Arc](create-postgresql-hyperscale-server-group.md) (eerst moet een Azure Arc-gegevenscontroller gemaakt worden)
- [Resource providers for Azure services](../../azure-resource-manager/management/azure-services-resource-providers.md) (Resourceproviders voor Azure-services)
- [Release opmerkingen-Azure Arc ingeschakelde Data Services (preview-versie)](release-notes.md)
