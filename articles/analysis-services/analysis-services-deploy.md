---
title: Een model implementeren naar Azure Analysis Services met behulp van Visual Studio | Microsoft Docs
description: Meer informatie over het implementeren van een tabellair model op een Azure Analysis Services-server met behulp van Visual Studio.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9df10760164dcd0d207663c14107f72c46b76d25
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501240"
---
# <a name="deploy-a-model-from-visual-studio"></a>Een model implementeren vanuit Visual Studio

Wanneer u een server in uw Azure-abonnement hebt gemaakt, kunt u een tabellaire modeldatabase op de server implementeren. U kunt met behulp van Visual Studio met Analysis Services projecten een tabellair model project maken en implementeren waarmee u aan de slag gaat. 

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* **Analysis Services-server** in Azure. Zie [Create an Azure Analysis Services server in Azure portal](analysis-services-create-server.md) (Een Azure Analysis Services-server maken in Azure Portal) voor meer informatie.
* **Project tabellair model** in Visual Studio of een bestaand tabellair model op het compatibiliteits niveau 1200 of hoger. Nog nooit zo'n project gemaakt? Raadpleeg de [Adventure Works Internet sales tabular modeling tutorial](/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial) (Zelfstudie voor tabellaire modellen bij Adventure Works-internetverkoop).
* **On-premises gateway**: als een of meer gegevensbronnen on-premises zijn opgeslagen in het netwerk van uw organisatie, moet u een [on-premises gegevensgateway](analysis-services-gateway.md) installeren. De gateway is nodig om uw server in de cloud verbinding te laten maken met uw on-premises gegevensbronnen om gegevens in het model te verwerken en te vernieuwen.

> [!TIP]
> Voer de implementatie pas uit als u weet dat u de gegevens in de tabellen kunt verwerken. Klik in Visual Studio op **model**  >  **proces**  >  **proces alle**. Als de verwerking mislukt, kunt u niet implementeren.
> 
> 

## <a name="get-the-server-name"></a>De server naam ophalen

In **Azure Portal** > server > **Overview** > **Servernaam**,kopieer de servernaam.
   
![Servernaam bepalen in Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-visual-studio"></a>Implementeren vanuit Visual Studio

1. Klik in Visual Studio > **Solution Explorer** met de rechter muisknop op de > **Eigenschappen** van het project. Plak vervolgens in **implementatie**  >  **Server** de naam van de server.   
   
    ![Servernaam plakken in de eigenschap Deployment Server](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. Klik in **Solution Explorer** met de rechtermuisknop op **Properties** en klik vervolgens op **Deploy**. U wordt mogelijk gevraagd u aan te melden bij Azure.
   
    ![Implementeren op server](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    De status van de implementatie wordt weergegeven in het uitvoervenster en in het venster Deploy.
   
    ![Implementatiestatus](./media/analysis-services-deploy/aas-deploy-status.png)

Dat is alles!


## <a name="troubleshooting"></a>Problemen oplossen

Als de implementatie mislukt bij het implementeren van meta gegevens, is het waarschijnlijk dat Visual Studio geen verbinding kan maken met uw server. Zorg ervoor dat u verbinding kunt maken met uw server met behulp van SQL Server Management Studio (SSMS). Controleer vervolgens of de eigenschap Deployment Server voor het project juist is ingesteld.

Als de implementatie mislukt bij een tabel, komt dat waarschijnlijk omdat de server geen verbinding kan maken met een gegevensbron. Als uw gegevensbron on-premises in het netwerk van uw organisatie is opgeslagen, moet u een [on-premises gegevensgateway](analysis-services-gateway.md) installeren.

## <a name="next-steps"></a>Volgende stappen

Uw tabellaire model is nu geïmplementeerd op de server en dus kunt u verbinding gaan maken met het model. U kunt er [verbinding mee maken met SQL Server Management Studio (SSMS)](analysis-services-manage.md) om deze te beheren. [Maak verbinding met een clienthulpprogramma](analysis-services-connect.md) zoals Power BI, Power BI Desktop of Excel om rapporten te gaan maken.   

Zie voor meer informatie over geavanceerde implementatie methoden [tabellaire model oplossingen implementeren](/analysis-services/deployment/tabular-model-solution-deployment?view=azure-analysis-services-current&preserve-view=true).