---
title: Meer informatie over het gebruik van een Apache Hadoop sandbox, emulator-Azure HDInsight
description: 'Als u meer wilt weten over het gebruik van het Apache Hadoop ecosysteem, kunt u een Hadoop-sandbox instellen vanuit Hortonworks op een virtuele Azure-machine. '
keywords: Hadoop-emulator, Hadoop-sandbox
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: how-to
ms.date: 05/29/2019
ms.openlocfilehash: 60f4e4998e1077ca373f47a0cdf35c9d7a064605
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866672"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Aan de slag met een Apache Hadoop sandbox, een emulator op een virtuele machine

Meer informatie over het installeren van de Apache Hadoop sandbox van Hortonworks op een virtuele machine voor meer informatie over het Hadoop-ecosysteem. De sandbox biedt een lokale ontwikkel omgeving voor meer informatie over Hadoop, Hadoop Distributed File System (HDFS) en het verzenden van taken. Wanneer u bekend bent met Hadoop, kunt u Hadoop op Azure gaan gebruiken door een HDInsight-cluster te maken. Zie aan de slag [met Hadoop op HDInsight](apache-hadoop-linux-tutorial-get-started.md)voor meer informatie over hoe u aan de slag kunt gaan.

## <a name="prerequisites"></a>Vereisten

* [Oracle-VirtualBox](https://www.virtualbox.org/). Down load en installeer deze [hier](https://www.virtualbox.org/wiki/Downloads).

## <a name="download-and-install-the-virtual-machine"></a>De virtuele machine downloaden en installeren

1. Blader naar de [Cloudera-down loads](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html).

1. Klik op **VIRTUALBOX** onder **installatie type kiezen** om de nieuwste Hortonworks sandbox op een virtuele machine te downloaden. Meld u aan of vul het formulier voor de rente van het product in.

1. Klik op de knop **HDP SANDBOX (meest recent)** om het downloaden te starten.

Zie [sandbox-implementatie en installatie handleiding](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/)voor instructies voor het instellen van de sandbox.

Als u een oudere sandbox van de HDP-versie wilt downloaden, raadpleegt u de koppelingen onder **oudere versies**.

## <a name="start-the-virtual-machine"></a>De virtuele machine starten

1. Open Oracle VM VirtualBox.
1. Klik in het menu **bestand** op **apparaat importeren** en geef vervolgens de Hortonworks sandbox-installatie kopie op.
1. Selecteer de sandbox Hortonworks, klik op **Start** en vervolgens op **normaal starten**. Zodra de virtuele machine het opstart proces heeft voltooid, worden aanmeldings instructies weer gegeven.

    :::image type="content" source="./media/apache-hadoop-emulator-get-started/virtualbox-normal-start.png" alt-text="normale start van VirtualBox Manager" border="true":::

1. Open een webbrowser en navigeer naar de weer gegeven URL (meestal `http://127.0.0.1:8888` ).

## <a name="set-sandbox-passwords"></a>Sandbox-wacht woorden instellen

1. Selecteer in de stap **aan de slag** van de sandbox Hortonworks-pagina **Geavanceerde opties weer geven**. Gebruik de informatie op deze pagina om u aan te melden bij de sandbox met SSH. Gebruik de opgegeven naam en het wacht woord.

   > [!NOTE]
   > Als er geen SSH-client is geïnstalleerd, kunt u de op het web gebaseerde SSH gebruiken die is verschaft door de virtuele machine op **http://localhost:4200/** .

    De eerste keer dat u verbinding maakt via SSH, wordt u gevraagd het wacht woord voor het hoofd account te wijzigen. Voer een nieuw wacht woord in dat u gebruikt wanneer u zich aanmeldt met SSH.

2. Wanneer u bent aangemeld, voert u de volgende opdracht in:

    ```bash
    ambari-admin-password-reset
    ```

    Wanneer u hierom wordt gevraagd, geeft u een wacht woord op voor het Ambari-beheerders account. Dit wordt gebruikt wanneer u toegang hebt tot de Ambari-webgebruikersinterface.

## <a name="use-hive-commands"></a>Hive-opdrachten gebruiken

1. Gebruik vanuit een SSH-verbinding met de sandbox de volgende opdracht om de Hive-shell te starten:

    ```bash
    hive
    ```

2. Zodra de shell is gestart, gebruikt u het volgende om de tabellen weer te geven die bij de sandbox worden meegeleverd:

    ```hiveql
    show tables;
    ```

3. Gebruik het volgende om 10 rijen uit de tabel op te halen `sample_07` :

    ```hiveql
    select * from sample_07 limit 10;
    ```

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het gebruik van Visual Studio met de Hortonworks sandbox](./apache-hadoop-visual-studio-tools-get-started.md)

* [De kabels van de Hortonworks-sandbox leren](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)

* [Hadoop-zelf studie: aan de slag met HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)