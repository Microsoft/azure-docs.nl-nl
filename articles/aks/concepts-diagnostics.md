---
title: Overzicht van diagnostische gegevens over Azure Kubernetes service (AKS)
description: Meer informatie over zelf diagnose van clusters in azure Kubernetes service.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: e8921152177d3e4534ca9fb48cf209aed6e1b27c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96183359"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Overzicht van de diagnostische gegevens voor Azure Kubernetes-service (preview)

Het oplossen van problemen met Azure Kubernetes service (AKS) is een belang rijk onderdeel van het onderhoud van uw cluster, met name als uw cluster essentiële werk belastingen uitvoert. AKS Diagnostics is een intelligente, zelf diagnose-ervaring die u helpt bij het identificeren en oplossen van problemen in uw cluster. AKS Diagnostics is Cloud-native en u kunt het gebruiken zonder extra configuratie-of facturerings kosten.

Deze functie is nu beschikbaar als open bare preview.

## <a name="open-aks-diagnostics"></a>Diagnostische gegevens van AKS openen

Voor toegang tot AKS Diagnostics:

- Navigeer naar uw Kubernetes-cluster in de [Azure Portal](https://portal.azure.com).
- Klik op **problemen opsporen en oplossen** in het navigatie venster aan de linkerkant, waarmee AKS Diagnostics wordt geopend.
- Kies een categorie die het beste het probleem van het cluster beschrijft met behulp van de tref woorden in de tegel start pagina of typ een tref woord dat uw probleem het beste beschrijft in de zoek balk, bijvoorbeeld problemen met het _cluster knooppunt_.

![Startpagina](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Een diagnostisch rapport weer geven

Nadat u op een categorie hebt geklikt, kunt u een diagnostisch rapport bekijken dat specifiek is voor uw cluster. Diagnostisch rapport wordt intelligent aangeroepen als er een probleem is met de status pictogrammen in uw cluster. U kunt inzoomen op elk onderwerp door te klikken op **meer info** om een gedetailleerde beschrijving van het probleem, aanbevolen acties, koppelingen naar nuttige docs, gerelateerde metrieken en logboek registratie gegevens weer te geven. Diagnostische rapporten worden op een slimme manier gegenereerd op basis van de huidige status van het cluster nadat er verschillende controles zijn uitgevoerd. Diagnostische rapporten kunnen een handig hulp middel zijn voor het herkennen van het probleem van het cluster en het vinden van de volgende stappen om het probleem op te lossen.

![Diagnostisch rapport](./media/concepts-diagnostics/diagnostic-report.png)

![Uitgebreid diagnostisch rapport](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Cluster inzichten

De volgende diagnostische controles zijn beschikbaar in **cluster Insights**.

### <a name="cluster-node-issues"></a>Problemen met het cluster knooppunt

Problemen met het cluster knooppunt controleren op problemen met betrekking tot het knoop punt, waardoor uw cluster onverwacht kan werken.

- Problemen met de gereedheid van knoop punten
- Knooppunt fouten
- Onvoldoende resources
- IP-configuratie van knoop punt ontbreekt
- Storingen in knooppunt CNI
- Knoop punt niet gevonden
- Knoop punt uitschakelen
- Fout bij knooppunt verificatie
- Knoop punt uitvoeren-proxy verouderd

### <a name="create-read-update--delete-operations"></a>Bewerkingen voor het verwijderen van & maken, lezen, bijwerken

RUWE bewerkingen controleren op ruwe bewerkingen die problemen in uw cluster kunnen veroorzaken.

- Fout bij het verwijderen van het subnet in gebruik
- Fout bij verwijderen van netwerk beveiligings groep
- Fout bij het verwijderen van de route tabel in gebruik
- Fout bij het inrichten van resource waarnaar wordt verwezen
- Fout bij verwijderen van openbaar IP-adres
- Implementatie fout vanwege implementatie quotum
- Bewerkings fout vanwege het organisatie beleid
- Registratie van abonnement ontbreekt
- Fout bij inrichten van VM-extensie
- Capaciteit van subnet
- Fout met overschreden quotum

### <a name="identity-and-security-management"></a>Identiteits- en beveiligingsbeheer

Identiteits-en beveiligings beheer detecteert verificatie-en autorisatie fouten die de communicatie met uw cluster mogelijk verhinderen.

- Knooppunt autorisatie fouten
- 401 fouten
- 403-fouten

## <a name="next-steps"></a>Volgende stappen

Verzamel Logboeken om u te helpen bij het oplossen van problemen met uw cluster met behulp van [AKS Peri Scope](https://aka.ms/aksperiscope).

Lees het [gedeelte sorteren practices](/azure/architecture/operator-guides/aks/aks-triage-practices) van de AKS-hand leiding voor de dag-2.

Post uw vragen of feedback op [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) door ' [diag] ' toe te voegen in de titel.