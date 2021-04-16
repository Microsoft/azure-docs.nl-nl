---
title: Opslag op BareMetal voor Oracle-workloads
description: Meer informatie over de opslag die wordt aangeboden door de BareMetal-infrastructuur voor Oracle-workloads.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 685f4d401d05d897b91ad12dbd79f7d175db05c6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559014"
---
# <a name="storage-on-baremetal-for-oracle-workloads"></a>Opslag op BareMetal voor Oracle-workloads

In dit artikel geven we een overzicht van de opslag die wordt aangeboden door de BareMetal-infrastructuur voor Oracle-workloads.

BareMetal Infrastructure for Oracle biedt NetApp Network File System -opslag (NFS). Voor NFS-opslag is geen RAC-certificering (Oracle Real Application Clusters) vereist. Zie Oracle [RAC Technologies Matrix for Linux Clusters (Oracle RAC Technologies Matrix voor Linux-clusters) voor meer informatie.](https://www.oracle.com/database/technologies/tech-generic-linux-new.html)

Deze opslagaanbieding omvat laag 3-ondersteuning van een OEM-partner, met behulp van A700-opslagcontrollers of A800-opslagcontrollers.

BareMetal Infrastructure-opslag biedt de volgende premium-opslagmogelijkheden:

- Opslagvolumes voor gegevens/logboek/quorum/FSA aangeboden via het dNFS-protocol.
- Schijf redundantie (*bescherming tegen maximaal twee schijffouten*).
- Schaal uw gegevens uit tot meerdere volumes die zijn beperkt tot 100 TB per volume.
- Schaal uit naar meerdere opslagcontrollers tot maximaal 12 controllers.
- Er is geen beheer op schijfniveau *(schijven toevoegen/verwijderen)* die automatisch worden verzorgd door Infra.
- Er is geen downtime voor het opnieuw distribueren van de bestandsinhoud naar verschillende volumes.
- Mogelijkheid om volumes te laten groeien/verkleinen.
- SnapCenter-integratie voor back-up met behulp van klonen en SnapVault.
- Versleuteling van data-at-rest, met ondersteuning voor FIPS (140-2).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over patchoverwegingen voor BareMetal Infrastructure.

> [!div class="nextstepaction"]
> [Patchoverwegingen voor BareMetal voor Oracle](oracle-baremetal-patching.md)

