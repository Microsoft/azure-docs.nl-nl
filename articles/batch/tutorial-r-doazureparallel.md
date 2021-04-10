---
title: 'Zelfstudie: Parallelle R-simulatie uitvoeren met Azure Batch'
description: 'Zelfstudie: Stapsgewijze instructies voor het uitvoeren van een financiële Monte Carlo-simulatie in Azure Batch met het R-pakket doAzureParallel'
ms.devlang: r
ms.topic: tutorial
ms.date: 10/08/2020
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 7e74a3b02be35b40e1a92bef269a3aed0b2313e4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103491772"
---
# <a name="tutorial-run-a-parallel-r-simulation-with-azure-batch"></a>Zelfstudie: Een parallelle R-simulatie uitvoeren met Azure Batch

Voer uw parallelle R-workloads op schaal uit met behulp van [doAzureParallel](https://www.github.com/Azure/doAzureParallel), een lichtgewicht R-pakket dat het mogelijk maakt Azure Batch rechtstreeks vanuit uw R-sessie te gebruiken. Het pakket doAzureParallel is gebouwd op het populaire R-pakket [foreach](https://cran.r-project.org/web/packages/foreach/index.html). doAzureParallel haalt elke herhaling van de foreach-lus op en verzendt deze als een Azure Batch-taak.

In deze zelfstudie wordt uitgelegd hoe u een Batch-pool implementeert en een parallelle R-taak in Azure Batch rechtstreeks uitvoert in RStudio. In deze zelfstudie leert u procedures om het volgende te doen:


> [!div class="checklist"]
> * doAzureParallel installeren en configureren voor toegang tot uw Batch- en Storage-accounts
> * Een Batch-pool maken als een parallelle back-end voor uw R-sessie
> * Een voorbeeld van een parallelle simulatie uitvoeren in de pool

## <a name="prerequisites"></a>Vereisten

* Een geïnstalleerde [R](https://www.r-project.org/)-distributie, zoals [Microsoft R Open](https://mran.microsoft.com/open). Gebruik R versie 3.3.1 of hoger.

* [RStudio](https://www.rstudio.com/), de commerciële versie of de open-source versie [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop).

* Een Azure Batch-account en een Azure Storage-account. Raadpleeg de Batch-quickstarts met behulp van [Azure Portal](quick-create-portal.md) of [Azure CLI](quick-create-cli.md) voor instructies voor het maken van deze accounts.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]
## <a name="install-doazureparallel"></a>doAzureParallel installeren

Installeer het [GitHub-pakket doAzureParallel](https://www.github.com/Azure/doAzureParallel) in de RStudio-console. De volgende opdrachten downloaden en installeren het pakket en de bijbehorende afhankelijkheden in uw huidige R-sessie:

```R
# Install the devtools package
install.packages("devtools")

# Install rAzureBatch package
devtools::install_github("Azure/rAzureBatch")

# Install the doAzureParallel package
devtools::install_github("Azure/doAzureParallel")

# Load the doAzureParallel library
library(doAzureParallel)
```
De installatie kan enkele minuten duren.

Voor de configuratie van doAzureParallel met de accountreferenties die u eerder hebt opgehaald, genereert u een configuratiebestand genaamd *credentials.json* in uw werkmap:

```R
generateCredentialsConfig("credentials.json")
```

Vul dit bestand met de namen en sleutels van uw Batch- en opslagaccount. Laat de instelling `githubAuthenticationToken` ongewijzigd.

Na voltooiing ziet het referentiebestand er ongeveer als volgt uit:

```json
{
  "batchAccount": {
    "name": "mybatchaccount",
    "key": "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==",
    "url": "https://mybatchaccount.mybatchregion.batch.azure.com"
  },
  "storageAccount": {
    "name": "mystorageaccount",
    "key": "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=="
  },
  "githubAuthenticationToken": ""
}
```

Sla het bestand op. Voer de volgende opdracht uit om de referenties voor uw huidige R-sessie in te stellen:

```R
setCredentials("credentials.json")
```

## <a name="create-a-batch-pool"></a>Batch-pool maken

doAzureParallel bevat een functie die een Azure Batch-pool (cluster) genereert voor het uitvoeren van parallelle R-taken. Op de knooppunten draait een [virtuele machine voor datatechnologie van Azure](../machine-learning/data-science-virtual-machine/overview.md) op basis van Ubuntu. Microsoft R Open en populaire R-pakketten zijn vooraf geïnstalleerd in deze installatiekopie. U kunt bepaalde clusterinstellingen weergeven of aanpassen, zoals het aantal en de grootte van de knooppunten.

Een JSON-bestand voor clusterconfiguratie in uw werkmap genereren:

```R
generateClusterConfig("cluster.json")
```

Open het bestand om de standaardconfiguratie weer te geven, waaronder 3 toegewezen knooppunten en 3 knooppunten met een [lage prioriteit](batch-low-pri-vms.md). Deze instellingen zijn slechts voorbeelden waarmee u kunt experimenteren en die u kunt wijzigen. Toegewezen rekenknooppunten zijn gereserveerd voor uw pool. Knooppunten met een lage prioriteit worden aangeboden tegen een lagere prijs en worden gehaald uit het overschot van de VM-capaciteit in Azure. Knooppunten met een lage prioriteit zijn niet beschikbaar als Azure onvoldoende capaciteit heeft.

Voor deze zelfstudie wijzigt u de configuratie als volgt:

* Verhoog `taskSlotsPerNode` naar *2* om gebruik te maken van beide kernen op elk knooppunt
* Stel `dedicatedNodes` in op *0*, zodat u de VM's met lage prioriteit die beschikbaar zijn voor Batch kunt proberen. Stel `min` van `lowPriorityNodes` in op *5*. Stel `max` in op *10* of kies desgewenste lagere waarden.

Laat de overige instellingen op de standaardwaarden staan en sla het bestand op. Het moet er ongeveer als volgt uitzien:

```json
{
  "name": "myPoolName",
  "vmSize": "Standard_D2_v2",
  "taskSlotsPerNode": 2,
  "poolSize": {
    "dedicatedNodes": {
      "min": 0,
      "max": 0
    },
    "lowPriorityNodes": {
      "min": 5,
      "max": 10
    },
    "autoscaleFormula": "QUEUE"
  },
  "containerImage": "rocker/tidyverse:latest",
  "rPackages": {
    "cran": [],
    "github": [],
    "bioconductor": []
  },
  "commandLine": []
}
```

U gaat nu het cluster maken. De pool wordt onmiddellijk gemaakt in Batch, maar het duurt enkele minuten voordat de rekenknooppunten zijn toegewezen en gestart. Als het cluster eenmaal beschikbaar is, registreert u dit als parallelle back-end voor uw R-sessie.

```R
# Create your cluster if it does not exist; this takes a few minutes
cluster <- makeCluster("cluster.json")

# Register your parallel backend
registerDoAzureParallel(cluster)

# Check that the nodes are running
getDoParWorkers()
```

De uitvoer toont het aantal 'uitvoeringswerkrollen' voor doAzureParallel. Dit aantal is het aantal knooppunten vermenigvuldigd met de waarde van `taskSlotsPerNode`. Als u de clusterconfiguratie hebt gewijzigd zoals eerder beschreven, is de waarde *10*.

## <a name="run-a-parallel-simulation"></a>Een parallelle simulatie uitvoeren

Nu uw cluster is gemaakt, bent u klaar om uw foreach-lus uit te voeren met uw geregistreerde parallelle back-end (Azure Batch-pool). Als voorbeeld voert u een financiële Monte Carlo-simulatie uit, eerst lokaal via een standaard foreach-lus, en daarna voert u foreach uit met Batch. Dit voorbeeld is een vereenvoudigde versie van het voorspellen van een aandelenkoers door een groot aantal verschillende uitkomsten na 5 jaar te simuleren.

Stel dat het aandeel van Contoso Corporation gemiddeld elke dag 1,001 keer de openingskoers stijgt, maar een volatiliteit (standaarddeviatie) van 0,01 heeft. Met een beginkoers van $100 gebruikt u een Monte Carlo-koerssimulatie om de aandelenkoers van Contoso na 5 jaar te bepalen.

Parameters voor de Monte Carlo-simulatie:

```R
mean_change = 1.001
volatility = 0.01
opening_price = 100
```

Als u slotkoersen wilt simuleren, definieert u de volgende functie:

```R
getClosingPrice <- function() {
  days <- 1825 # ~ 5 years
  movement <- rnorm(days, mean=mean_change, sd=volatility)
  path <- cumprod(c(opening_price, movement))
  closingPrice <- path[days]
  return(closingPrice)
}
```

Voer eerst 10.000 simulaties lokaal uit via een standaard foreach-lus met het sleutelwoord `%do%`:

```R
start_s <- Sys.time()
# Run 10,000 simulations in series
closingPrices_s <- foreach(i = 1:10, .combine='c') %do% {
  replicate(1000, getClosingPrice())
}
end_s <- Sys.time()
```


Zet de slotkoersen uit in een histogram om de distributie van de resultaten weer te geven:

```R
hist(closingPrices_s)
```

De uitvoer lijkt op het volgende:

![Schermopname toont een histogram met slotprijzen](media/tutorial-r-doazureparallel/closing-prices-local.png)
  
Een lokale simulatie wordt in enkele seconden of minder uitgevoerd:

```R
difftime(end_s, start_s)
```

De geschatte uitvoertijd voor 10 miljoen uitkomsten lokaal met behulp van een lineaire benadering is ongeveer 30 minuten:

```R
1000 * difftime(end_s, start_s, unit = "min")
```


Voer de code nu uit met behulp van `foreach` met het sleutelwoord `%dopar%` om te vergelijken hoe lang het duurt om 10 miljoen simulaties uit te voeren in Azure. Als u de simulatie parallel wilt uitvoeren met Batch, voert u 100 herhalingen van 100.000 simulaties uit:

```R
# Optimize runtime. Chunking allows running multiple iterations on a single R instance.
opt <- list(chunkSize = 10)
start_p <- Sys.time()
closingPrices_p <- foreach(i = 1:100, .combine='c', .options.azure = opt) %dopar% {
  replicate(100000, getClosingPrice())
}
end_p <- Sys.time()
```

De simulatie distribueert taken naar de knooppunten in de Batch-pool. U ziet de activiteit in de heatmap voor de pool in de Azure Portal. Ga naar **Batch-accounts** > *myBatchAccount*. Klik op **Pools** > *myPoolName*.

![Heatmap van pool waarin parallelle R-taken worden uitgevoerd](media/tutorial-r-doazureparallel/pool.png)

Na enkele minuten is de simulatie voltooid. Het pakket voegt de resultaten automatisch samen en haalt ze op uit de knooppunten. Vervolgens kunt u de resultaten gaan gebruiken in uw R-sessie.

```R
hist(closingPrices_p)
```

De uitvoer lijkt op het volgende:

![Distributie van slotkoersen](media/tutorial-r-doazureparallel/closing-prices.png)

Hoe lange duurde de parallelle simulatie?

```R
difftime(end_p, start_p, unit = "min")
```

U ziet dat het uitvoeren van de simulatie in de Batch-pool een aanzienlijke toename in de prestaties oplevert ten opzichte van de tijd die het kost om de simulatie lokaal uit te voeren.

## <a name="clean-up-resources"></a>Resources opschonen

De taak wordt automatisch verwijderd nadat deze is voltooid. Wanneer het cluster niet meer nodig is, roept u de functie `stopCluster` in het pakket doAzureParallel aan om het cluster te verwijderen:

```R
stopCluster(cluster)
```

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> doAzureParallel installeren en configureren voor toegang tot uw Batch- en Storage-accounts
> * Een Batch-pool maken als een parallelle back-end voor uw R-sessie
> * Een voorbeeld van een parallelle simulatie uitvoeren in de pool


Raadpleeg de documentatie en voorbeelden op GitHub voor meer informatie over doAzureParallel.

> [!div class="nextstepaction"]
> [doAzureParallel-pakket](https://github.com/Azure/doAzureParallel/)
