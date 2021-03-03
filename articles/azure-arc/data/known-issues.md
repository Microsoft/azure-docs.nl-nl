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
ms.openlocfilehash: d4667e8fa3a5624dddc3cb0dd792fc73ea812332
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693034"
---
# <a name="known-issues---azure-arc-enabled-data-services-preview"></a>Bekende problemen: Azure Arc enabled Data Services (preview-versie)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="february-2021"></a>Februari 2021


- De modus verbonden cluster is uitgeschakeld
- Azure Arc enabled PostgreSQL grootschalige retourneert een onjuist fout bericht wanneer het niet kan worden teruggezet naar het relatieve punt in de tijd dat u opgeeft. Als u bijvoorbeeld een punt in de tijd hebt opgegeven om te herstellen dat ouder is dan uw back-ups bevatten, mislukt de herstel bewerking met een fout bericht als: `ERROR: (404). Reason: Not found. HTTP response body: {"code":404, "internalStatus":"NOT_FOUND", "reason":"Failed to restore backup for server...}` . Wanneer dit het geval is, start u de opdracht opnieuw op nadat u een tijdstip hebt aangegeven dat binnen het bereik van de datums valt waarvoor u back-ups hebt. Als u dit bereik wilt bepalen, vermeldt u uw back-ups en bekijkt u de datums waarop deze zijn gemaakt.
- U moet een back-up-id opgeven wanneer u een volledige herstel bewerking uitvoert. Als u geen back-upid aangeeft, wordt standaard de meest recente back-up gebruikt. Dit werkt niet in deze release.

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

### <a name="postgresql"></a>PostgreSQL

- Azure Arc enabled PostgreSQL grootschalige retourneert een onjuist fout bericht wanneer het niet kan worden teruggezet naar het relatieve punt in de tijd dat u opgeeft. Als u bijvoorbeeld een punt in de tijd hebt opgegeven om te herstellen dat ouder is dan uw back-ups bevatten, mislukt de herstel bewerking met een fout bericht als: `ERROR: (404). Reason: Not found. HTTP response body: {"code":404, "internalStatus":"NOT_FOUND", "reason":"Failed to restore backup for server...}`
Wanneer dit het geval is, start u de opdracht opnieuw op nadat u een tijdstip hebt aangegeven dat binnen het bereik van de datums valt waarvoor u back-ups hebt. U kunt dit bereik bepalen door uw back-ups weer te geven en te kijken naar de datums waarop deze zijn gemaakt.
- Herstel naar een bepaald tijdstip wordt alleen ondersteund in verschillende Server groepen. De doel server van een herstel bewerking naar een bepaald tijdstip kan geen server zijn van waaruit u de back-up hebt gemaakt. Dit moet een andere server groep zijn. Volledige herstel bewerking wordt echter wel ondersteund voor dezelfde server groep.
- U moet een back-up-id opgeven wanneer u een volledige herstel bewerking uitvoert. Als u geen back-upid aangeeft, wordt standaard de meest recente back-up gebruikt. Dit werkt niet in deze release.

## <a name="next-steps"></a>Volgende stappen

> **Wilt u gewoon iets uitproberen?**  
> Ga snel aan de slag met [Azure Arc](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) op AKS, AWS Elastic Kubernetes service (EKS), Google Cloud Kubernetes Engine (GKE) of in een Azure-VM.

- [Installeer de client-hulpprogramma's](install-client-tools.md)
- [Maak de Azure Arc gegevenscontroller](create-data-controller.md) (hiervoor moeten eerst de client-hulpprogramma's worden geïnstalleerd)
- [Maak een Azure SQL Managed Instance op Azure Arc](create-sql-managed-instance.md) (hiervoor moet u eerst een Azure Arc-gegevenscontroller maken)
- [Maak een Azure Database for PostgreSQL Hyperscale-servergroep op Azure Arc](create-postgresql-hyperscale-server-group.md) (eerst moet een Azure Arc-gegevenscontroller gemaakt worden)
- [Resource providers for Azure services](../../azure-resource-manager/management/azure-services-resource-providers.md) (Resourceproviders voor Azure-services)
- [Release opmerkingen-Azure Arc ingeschakelde Data Services (preview-versie)](release-notes.md)
