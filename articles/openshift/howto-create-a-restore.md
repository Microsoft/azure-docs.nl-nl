---
title: Een Azure Red Hat open Shift 4-cluster toepassing herstellen met behulp van velero
description: Meer informatie over het maken van een herstel bewerking van uw Azure Red Hat open Shift-cluster toepassingen met velero
ms.service: container-service
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: Aro, open Shift, AZ Aro, Red Hat, cli
ms.custom: mvc
ms.openlocfilehash: a4b22036612aac3d2b67e299881ba11b896979e5
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492400"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-restore"></a>Een Azure Red Hat open Shift 4-cluster toepassing herstellen

In dit artikel maakt u een voor bereiding van uw omgeving voor het maken van een Azure Red Hat open Shift 4-cluster toepassing herstellen. U leert het volgende:

> [!div class="checklist"]
> * De vereisten instellen en de benodigde hulpprogram ma's installeren
> * Een Azure Red Hat open Shift 4-toepassing herstellen

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.6.0 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli?view=azure-cli-latest) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="before-you-begin"></a>Voordat u begint

### <a name="create-an-azure-red-hat-openshift-4-application-backup"></a>Een Azure Red Hat open Shift 4-toepassing maken

Zie [een Azure Red Hat open Shift 4-back-up maken](./howto-create-a-backup.md) om een Azure Red Hat open Shift 4-toepassing te maken

## <a name="restore-an-azure-red-hat-openshift-4-application"></a>Een Azure Red Hat open Shift 4-toepassing herstellen

Met deze stappen kunt u een toepassing herstellen waarvoor eerder een back-up is gemaakt met Velero.
U kunt de lijst met back-ups die momenteel door het cluster worden herkend, controleren om te zien welke back-ups beschikbaar zijn voor herstel.  Voor deze stap moet u de volgende opdracht uitvoeren:

_(In deze stap wordt ervan uitgegaan dat u Velero hebt geïnstalleerd in een project met de naam ' Velero ')_

```bash
oc get backups -n velero
```

Zodra u de back-up hebt die u wilt herstellen, moet u de herstel bewerking uitvoeren met de volgende opdracht:

```bash
velero restore create <name of restore> --from-backup <name of backup from above output list>
```

Met deze stap maakt u de Kubernetes-objecten waarvan een back-up is gemaakt van de vorige stap bij het maken van een back-up.

Voer de volgende stap uit om de status van de herstel bewerking te bekijken:

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
Wanneer de fase aangeeft `Completed` , moet uw Azure Red Hat 4-toepassing worden hersteld.

## <a name="restore-an-azure-red-hat-openshift-4-application-with-included-snapshots"></a>Een Azure Red Hat open Shift 4-toepassing met opgenomen moment opnamen herstellen


Voor het maken van een herstel bewerking van een Azure Red Hat open Shift 4-toepassing met permanente volumes met behulp van velero, moet u de herstel bewerking uitvoeren met de volgende opdracht:

```bash
velero restore create <name of the restore> --from-backup <name of backup from above output list> --exclude-resources="nodes,events,events.events.k8s.io,backups.ark.heptio.com,backups.velero.io,restores.ark.heptio.com,restores.velero.io"
```
Met deze stap maakt u de Kubernetes-objecten waarvan een back-up is gemaakt van de vorige stap bij het maken van een back-up.

Voer de volgende stap uit om de status van de herstel bewerking te bekijken:

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
Wanneer de fase aangeeft `Completed` , moet uw Azure Red Hat 4-toepassing worden hersteld.

Voor meer informatie over het maken van back-ups en herstel bewerkingen met Velero raadpleegt u [de systeem eigen manier van back-ups](https://www.openshift.com/blog/backup-openshift-resources-the-native-way) .

## <a name="next-steps"></a>Volgende stappen

In dit artikel is een Azure Red Hat open Shift 4-cluster toepassing teruggezet. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een open Shift v4 cluster toepassing Restore maken met behulp van velero
> * Een open Shift v4-cluster toepassing herstellen met moment opnamen met behulp van velero


Ga naar het volgende artikel voor meer informatie over de ondersteunde bronnen van Azure Red Hat open Shift 4.

* [Ondersteunde bronnen voor Azure Red Hat open Shift v4](supported-resources.md)