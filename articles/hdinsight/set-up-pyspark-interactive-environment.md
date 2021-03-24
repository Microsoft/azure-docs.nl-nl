---
title: PySpark Interactive Environment met Azure HDInsight-Hulpprogram Ma's
description: Meer informatie over het gebruik van de Azure HDInsight-Hulpprogram Ma's voor Visual Studio code voor het maken en verzenden van query's en scripts.
keywords: VScode, Azure HDInsight tools, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, interactieve query
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-python
ms.date: 04/23/2020
ms.openlocfilehash: a6247116cdf579691e48883231f57712da36c4ad
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864564"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>De PySpark Interactive Environment instellen voor Visual Studio code

De volgende stappen laten zien hoe u de PySpark Interactive Environment instelt in VSCode. Deze stap is alleen voor niet-Windows-gebruikers.

We gebruiken **python/pip-** opdracht voor het maken van een virtuele omgeving in uw start traject. Als u een andere versie wilt gebruiken, moet u de standaard versie van de opdracht **python/pip** hand matig wijzigen. Zie [update-alternatieven](https://linux.die.net/man/8/update-alternatives)voor meer informatie.

1. Installeer [python](https://www.python.org/downloads/) en [PIP](https://pip.pypa.io/en/stable/installing/).

   * Installeer python vanuit [https://www.python.org/downloads/](https://www.python.org/downloads/) . 
   * PIP installeren vanaf [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) (als deze niet is geïnstalleerd vanuit de python-installatie).
   * U kunt eventueel controleren of python en PIP zijn geïnstalleerd met behulp van de opdrachten `python --version` , `pip --version` respectievelijk. 

     > [!NOTE]
     > Het is raadzaam python hand matig te installeren in plaats van de standaard versie macOS te gebruiken.

2. Installeer **virtualenv** door de onderstaande opdracht uit te voeren.

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>Andere pakketten

Als u in Linux over het onderstaande fout bericht komt, installeert u de vereiste pakketten door de volgende twee opdrachten uit te voeren.

   :::image type="content" source="./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png" alt-text="Het libkrb5-pakket voor python installeren" border="true":::

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

Start VSCode opnieuw op, ga terug naar de VSCode-editor en voer **Spark: PySPark Interactive** Command uit.

## <a name="next-steps"></a>Volgende stappen

### <a name="demo"></a>Demo

* HDInsight voor VS code: [video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Tools en uitbreidingen

* [Azure HDInsight-hulp programma voor Visual Studio code gebruiken](hdinsight-for-vscode.md)
* [Azure-toolkit voor IntelliJ gebruiken om Apache Spark scala-toepassingen te maken en in te dienen](spark/apache-spark-intellij-tool-plugin.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](spark/apache-spark-jupyter-notebook-install-locally.md)
