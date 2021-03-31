---
title: Continue integratie en implementatie voor Azure Stream Analytics
description: Dit artikel geeft een overzicht van een continue integratie-en implementatie pijplijn (CI/CD) voor Azure Stream Analytics.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: 940813f12d542715db47781731144a75e854a98e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019564"
---
# <a name="continuous-integration-and-deployment-cicd-for-azure-stream-analytics"></a>Continue integratie en implementatie (CI/CD) voor Azure Stream Analytics

U kunt uw Azure Stream Analytics taak continu implementeren met behulp van broncode beheer integratie. Met broncode beheer integratie kunt u een werk stroom maken waarin een code-update een resource-implementatie activeert naar Azure. In dit artikel vindt u een overzicht van de basis stappen voor het maken van een continue integratie-en implementatie pijplijn (CI/CD).

Als u nog geen ervaring hebt met Azure Stream Analytics, kunt u aan de slag met de [Azure stream Analytics Snelstartgids](stream-analytics-quick-create-portal.md).

## <a name="create-a-cicd-pipeline"></a>Een CI/CD-pijplijn maken

Volg de stappen in deze hand leiding voor het maken van een CI/CD-pijp lijn voor Stream Analytics.

1. Ontwikkel een Azure Stream Analytics-query.

   Gebruik Azure Stream Analytics-hulpprogram ma's voor [Visual Studio code](./quick-create-visual-studio-code.md) of [Visual Studio](stream-analytics-quick-create-vs.md) om [query's lokaal te ontwikkelen en te testen](develop-locally.md). U kunt ook [een bestaande taak exporteren](visual-studio-code-explore-jobs.md#export-a-job-to-a-local-project) naar een lokaal project.

2. Uw Azure Stream Analytics-projecten door voeren in uw broncode beheersysteem, zoals een Git-opslag plaats.

3. Gebruik [Azure stream Analytics CI/cd-hulpprogram ma's](cicd-tools.md) om de projecten te bouwen en Azure-resource beheer sjablonen te genereren voor de implementatie.

4. [Automatische script tests](cicd-tools.md#automated-test) uitvoeren voor kwaliteits regressie.

5. [De taak automatisch implementeren](cicd-tools.md#deploy-to-azure) in Azure.

## <a name="auto-build-test-and-deploy"></a>Automatisch bouwen, testen en implementeren

U kunt de opdracht regel en [Azure stream Analytics CI/cd-hulpprogram ma's](cicd-tools.md) gebruiken om automatisch te bouwen, te testen en te implementeren. U kunt ook een CI/CD-pijp lijn instellen in [Azure-pijp lijnen](set-up-cicd-pipeline.md). Azure-pijp lijnen om meer geavanceerde mogelijkheden te bieden, zoals pijplijn beheer, visualisatie en triggers.

## <a name="next-steps"></a>Volgende stappen

* [Builds, testen en implementaties van een Azure Stream Analytics-taak automatiseren met CI/CD-hulpprogram ma's](cicd-tools.md)
* [Een CI/CD-pijp lijn voor Stream Analytics taak instellen met behulp van Azure-pijp lijnen](set-up-cicd-pipeline.md)