---
title: Overzicht van diagnostische gegevens over Azure Kubernetes service (AKS)
description: Meer informatie over zelf diagnose van clusters in azure Kubernetes service.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: yunjchoi
ms.openlocfilehash: ee11221e5484a796b8dbbcb10a323288d3e74756
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011555"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Overzicht van de diagnostische gegevens voor Azure Kubernetes-service (preview)

Problemen met Azure Kubernetes service (AKS) oplossen speelt een belang rijke rol bij het onderhouden van uw cluster, met name als uw cluster bedrijfskritische workloads uitvoert. AKS Diagnostics is een intelligente, zelf diagnose-ervaring die:
* Helpt u bij het identificeren en oplossen van problemen in uw cluster. 
* Is Cloud-native.
* Vereist geen extra configuratie-of facturerings kosten.

Deze functie is nu beschikbaar als open bare preview. 

## <a name="open-aks-diagnostics"></a>Diagnostische gegevens van AKS openen

Voor toegang tot AKS Diagnostics:

1. Navigeer naar uw Kubernetes-cluster in de [Azure Portal](https://portal.azure.com).
1. Klik op **problemen opsporen en oplossen** in het navigatie venster aan de linkerkant, waarmee AKS Diagnostics wordt geopend.
1. Kies een categorie die het probleem van uw cluster het beste beschrijft, zoals problemen met het _cluster knooppunt_:
    * De tref woorden in de tegel start pagina gebruiken.
    * Typ een tref woord dat uw probleem het beste beschrijft in de zoek balk.

![Startpagina](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Een diagnostisch rapport weer geven

Nadat u op een categorie hebt geklikt, kunt u een diagnostisch rapport bekijken dat specifiek is voor uw cluster. Diagnostische rapporten kunnen problemen in uw cluster op intelligente wijze aanroepen met status pictogrammen. U kunt inzoomen op elk onderwerp door te klikken op **meer info** om een gedetailleerde beschrijving weer te geven van:
* Problemen
* Aanbevolen acties
* Koppelingen naar nuttige docs
* Gerelateerde metrische gegevens
* Het vastleggen van gegevens in een logboek 

Diagnostische rapporten genereren op basis van de huidige status van uw cluster nadat verschillende controles zijn uitgevoerd. Ze kunnen handig zijn om het probleem van uw cluster te lokaliseren en de volgende stappen te volgen om het probleem op te lossen.

![Diagnostisch rapport](./media/concepts-diagnostics/diagnostic-report.png)

![Uitgebreid diagnostisch rapport](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Cluster inzichten

De volgende diagnostische controles zijn beschikbaar in **cluster Insights**.

### <a name="cluster-node-issues"></a>Problemen met het cluster knooppunt

Problemen met het cluster knooppunt controleren op problemen met betrekking tot het knoop punt, waardoor het cluster onverwacht gedraagt.

- Problemen met de gereedheid van knoop punten
- Knooppunt fouten
- Onvoldoende resources
- IP-configuratie van knoop punt ontbreekt
- Storingen in knooppunt CNI
- Knoop punt niet gevonden
- Knoop punt uitschakelen
- Fout bij knooppunt verificatie
- Knoop punt uitvoeren-proxy verouderd

### <a name="create-read-update--delete-crud-operations"></a>Het maken, lezen, bijwerken & verwijderen (ruwe) bewerkingen

RUWE bewerkingen controleren op ruwe bewerkingen die problemen in uw cluster veroorzaken.

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

Identiteits-en beveiligings beheer detecteert verificatie-en autorisatie fouten die de communicatie met uw cluster verhinderen.

- Knooppunt autorisatie fouten
- 401 fouten
- 403-fouten

## <a name="next-steps"></a>Volgende stappen

* Verzamel Logboeken om u te helpen bij het oplossen van problemen met uw cluster met behulp van [AKS Peri Scope](https://aka.ms/aksperiscope).

* Lees het [gedeelte sorteren practices](/azure/architecture/operator-guides/aks/aks-triage-practices) van de AKS-hand leiding voor de dag-2.

* Post uw vragen of feedback op [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) door ' [diag] ' toe te voegen in de titel.