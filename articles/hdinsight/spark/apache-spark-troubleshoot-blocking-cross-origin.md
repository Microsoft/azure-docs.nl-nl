---
title: 'Jupyter 404-fout: de cross-Origin-API wordt geblokkeerd-Azure HDInsight'
description: Jupyter-server 404 "niet gevonden" vanwege "het blok keren van de API voor cross-origin" in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 27cd3aff859fd46679679ac12d3acc03fa6da158
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98929445"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>Scenario: Jupyter server 404 "niet gevonden" vanwege "het blok keren van de API voor cross-origin" in azure HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van Apache Spark-onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Wanneer u de Jupyter-service op HDInsight opent, ziet u een fout venster met de melding ' niet gevonden '. Als u de Jupyter-logboeken controleert, ziet u er ongeveer als volgt uit:

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

Mogelijk ziet u ook een IP-adres in het veld oorsprong in het Jupyter-logboek.

## <a name="cause"></a>Oorzaak

Deze fout kan worden veroorzaakt door een paar dingen:

- Als u NSG-regels (netwerk beveiligings groep) hebt geconfigureerd, kunt u de toegang tot het cluster beperken. Als u de toegang met NSG-regels beperkt, kunt u nog steeds rechtstreeks toegang krijgen tot Apache Ambari en andere services met behulp van het IP-adres in plaats van de cluster naam. Wanneer u echter toegang zoekt tot Jupyter, ziet u de fout 404 ' niet gevonden '.

- Als u uw HDInsight-gateway een aangepaste DNS-naam hebt opgegeven dan de standaard `xxx.azurehdinsight.net` .

## <a name="resolution"></a>Oplossing

1. Wijzig de jupyter.py-bestanden op deze twee locaties:

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. Zoek de regel met de volgende tekst: `NotebookApp.allow_origin='\"https://{2}.{3}\"'` en wijzig deze in: `NotebookApp.allow_origin='\"*\"'` .

1. Start de Jupyter-service van Ambari opnieuw.

1. `ps aux | grep jupyter`Wanneer u op de opdracht prompt typt, ziet u dat er een URL kan worden gemaakt om er verbinding mee te maken.

Dit is minder veilig dan de instelling die we al hebben geïmplementeerd. Maar er wordt van uitgegaan dat de toegang tot het cluster beperkt is en dat een van de buiten kant is toegestaan om verbinding te maken met het cluster als er NSG aanwezig zijn.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]