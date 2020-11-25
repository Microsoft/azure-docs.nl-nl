---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: rogarana
ms.openlocfilehash: 1387933dc82c07e73b7715d6593238ea8c993e93
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005309"
---
Wijzigingen die zijn aangebracht in de Azure-bestands share met behulp van de Azure Portal of SMB worden niet onmiddellijk gedetecteerd en gerepliceerd zoals wijzigingen in het server eindpunt. Azure Files hebt nog geen wijzigings meldingen of Logboeken, dus is er geen manier om automatisch een synchronisatie sessie te initiëren wanneer bestanden worden gewijzigd. Op Windows Server maakt Azure File Sync gebruik van [Windows USN-logboeken](/windows/win32/fileio/change-journals) om automatisch een synchronisatie sessie te initiëren wanneer bestanden worden gewijzigd.

Om wijzigingen in de Azure-bestands share te detecteren, heeft Azure File Sync een geplande taak die een *wijzigings detectie taak* wordt genoemd. Een wijzigings detectie taak inventariseert elk bestand in de bestands share en vergelijkt deze met de synchronisatie versie voor dat bestand. Wanneer de wijzigings detectie taak bepaalt dat bestanden zijn gewijzigd, Azure File Sync initieert een synchronisatie sessie. De wijzigings detectie taak wordt elke 24 uur geïnitieerd. Omdat de wijzigings detectie taak werkt door elk bestand in de Azure-bestands share te inventariseren, neemt de detectie van wijzigingen langer deel uit van grotere naam ruimten dan in kleinere naam ruimten. Voor grote naam ruimten kan het langer dan eenmaal per 24 uur duren om te bepalen welke bestanden zijn gewijzigd.

Om bestanden direct te synchroniseren die in de Azure-bestands share zijn gewijzigd, kan de Power shell **-cmdlet invoke-AzStorageSyncChangeDetection** worden gebruikt om de detectie van wijzigingen in de Azure-bestands share hand matig te initiëren. Deze cmdlet is bedoeld voor scenario's waarbij een type geautomatiseerd proces wijzigingen aanbrengt in de Azure-bestands share of de wijzigingen worden uitgevoerd door een beheerder (zoals het verplaatsen van bestanden en mappen naar de share). Voor wijzigingen van de eind gebruiker is het aanbeveling om de Azure File Sync-agent te installeren in een IaaS-VM en eind gebruikers hebben toegang tot de bestands share via de IaaS-VM. Op deze manier worden alle wijzigingen snel gesynchroniseerd met andere agents zonder dat u de Invoke-AzStorageSyncChangeDetection-cmdlet hoeft te gebruiken. Zie de documentatie voor [invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) voor meer informatie.

>[!NOTE]
>Wijzigingen die zijn aangebracht in een Azure-bestands share met REST, werken de SMB laatst gewijzigd tijd niet bij en worden niet gezien als een wijziging door synchronisatie.

We gaan de detectie van wijzigingen voor een Azure-bestands share toevoegen vergelijkbaar met de USN voor volumes op Windows Server. Help ons de prioriteit van deze functie voor toekomstige ontwikkeling aan te geven op [Azure files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).