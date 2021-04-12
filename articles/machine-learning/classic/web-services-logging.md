---
title: 'ML Studio (klassiek): logboek registratie van webservice inschakelen-Azure'
description: Meer informatie over het inschakelen van logboek registratie voor Machine Learning Studio (klassieke) webservices. Logboek registratie biedt aanvullende informatie over het oplossen van de Api's.
services: machine-learning
author: likebupt
ms.author: keli19
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
ms.date: 06/15/2017
ms.openlocfilehash: c9ce9b7b0d739301e76abd43b265fe28195ed302
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100518280"
---
# <a name="enable-logging-for-azure-machine-learning-studio-classic-web-services"></a>Logboek registratie inschakelen voor Azure Machine Learning Studio (klassieke) webservices

**VAN TOEPASSING OP:**  ![Van toepassing op.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek) ![Niet van toepassing op.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Dit document bevat informatie over de logboek registratie van Machine Learning Studio (klassieke) webservices. Logboek registratie biedt aanvullende informatie, behalve een fout nummer en een bericht, waarmee u uw aanroepen naar de Machine Learning Studio (klassieke) Api's kunt oplossen.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Logboek registratie inschakelen voor een webservice

U schakelt logboek registratie in via de [Web Services-portal van Azure machine learning Studio (klassiek)](https://services.azureml.net) . 

1. Meld u aan bij de portal voor webservices van Azure Machine Learning Studio (klassiek) op [https://services.azureml.net](https://services.azureml.net) . Voor een klassieke webservice kunt u ook naar de portal gaan door te klikken op **nieuwe Web Services-ervaring** op de pagina machine learning Studio (klassiek) Web Services in Studio (klassiek).

   ![Koppeling nieuwe Web Services-ervaring](./media/web-services-logging/new-web-services-experience-link.png)

2. Klik in de bovenste menu balk op **webservices** voor een nieuwe webservice, of klik op **klassieke** webservices voor een klassieke webservice.

   ![Nieuwe of klassieke webservices selecteren](./media/web-services-logging/select-web-service.png)

3. Voor een nieuwe webservice, klikt u op de naam van de webservice. Voor een klassieke webservice klikt u op de naam van de webservice en klikt u op de volgende pagina op het juiste eind punt.

4. Klik op de bovenste menu balk op **configureren**.

5. Stel de optie **logboek registratie inschakelen** in op *fouten (alleen* logboeken vastleggen) of *alle* (voor volledige logboek registratie).

   ![Logboek registratie niveau selecteren](./media/web-services-logging/enable-logging.png)

6. Klik op **Opslaan**.

7. Voor klassieke webservices maakt u de container **met ml-diagnose** .

   Alle logboeken van de webservice worden bewaard in een BLOB-container met de naam **ml-diagnostische gegevens** in het opslag account dat is gekoppeld aan de webservice. Voor nieuwe webservices wordt deze container gemaakt wanneer u de eerste keer toegang tot de webservice. Voor klassieke webservices moet u de container maken als deze nog niet bestaat. 

   1. Ga in het [Azure Portal](https://portal.azure.com)naar het opslag account dat is gekoppeld aan de webservice.

   2. Klik onder **Blob Service** op **Containers**.

   3. Als de container **ml-diagnostische gegevens** niet aanwezig is, klikt u op **+ container**, geeft u de container de naam ' ml-Diagnostics ' en selecteert u het **toegangs type** als ' BLOB '. Klik op **OK**.

      ![Een nieuwe container maken om uw Diagnostische logboeken op te slaan](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Voor een klassieke webservice heeft het dash board webservices in Machine Learning Studio (klassiek) ook een schakel optie om logboek registratie in te scha kelen. Omdat logboek registratie nu wordt beheerd via de Web Services-portal, moet u logboek registratie inschakelen via de portal, zoals beschreven in dit artikel. Als u logboek registratie al hebt ingeschakeld in Studio (klassiek), schakelt u in de portal voor webservices logboek registratie uit en schakelt u het opnieuw in.


## <a name="the-effects-of-enabling-logging"></a>De gevolgen van het inschakelen van logboek registratie
Wanneer logboek registratie is ingeschakeld, worden de diagnostische gegevens en fouten van het eind punt van de webservice vastgelegd in de BLOB-container voor **milliliters diagnostiek** in het Azure Storage account dat is gekoppeld aan de werk ruimte van de gebruiker. Deze container bevat alle diagnostische gegevens voor alle webservice-eind punten voor alle werk ruimten die zijn gekoppeld aan dit opslag account.

De logboeken kunnen worden weer gegeven met een van de verschillende hulpprogram ma's die beschikbaar zijn om een Azure Storage account te verkennen. Het eenvoudigste kan zijn om naar het opslag account in de Azure Portal te gaan, op **containers** te klikken en vervolgens op de container **ml-diagnostische gegevens** te klikken.  

## <a name="log-blob-detail-information"></a>Informatie over logboek-blobs
Elke Blob in de container bevat de diagnostische gegevens voor precies één van de volgende acties:

* Een uitvoering van de Batch-Execution methode  
* Een uitvoering van de Request-Response methode  
* Initialisatie van een Request-Response-container

De naam van elke BLOB heeft een voor voegsel van de volgende vorm: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Waarbij _logboek type_ een van de volgende waarden is:  

* batch  
* Score/aanvragen  
* Score/init