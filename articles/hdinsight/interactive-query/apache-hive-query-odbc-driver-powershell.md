---
title: Query's uitvoeren op Apache Hive met ODBC-stuurprogramma en PowerShell - Azure HDInsight
description: Gebruik het Microsoft Hive ODBC-stuur programma en PowerShell om query's uit te voeren op Apache Hive-clusters in Azure HDInsight.
keywords: hive,hive odbc,powershell
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 34ee7eb25b1d106ea8bb53197d69ca5a9d528773
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871602"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>Zelfstudie: Query's uitvoeren op Apache Hive met ODBC en PowerShell

Microsoft ODBC-stuurprogramma's bieden een flexibele manier om te communiceren met verschillende soorten gegevensbronnen, met inbegrip van Apache Hive. U kunt code schrijven in scripttalen zoals PowerShell die de ODBC-stuurprogramma's gebruiken om een verbinding met uw Hive-cluster te openen, een query van uw keuze door te geven en de resultaten weer te geven.

In deze zelfstudie gaat u de volgende taken uitvoeren:

> [!div class="checklist"]
> * Het Microsoft Hive ODBC-stuurprogramma downloaden en installeren
> * Een Apache Hive ODBC-gegevensbron maken die aan uw cluster is gekoppeld
> * Query's uitvoeren op voorbeeldgegevens uit uw cluster met PowerShell

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelfstudie begint, moet u beschikken over de volgende items:

* Een Interactive Query-cluster in HDInsight. Zie [Aan de slag met Azure HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) om er een te maken. Selecteer **Interactieve query** als het clustertype.

## <a name="install-microsoft-hive-odbc-driver"></a>Microsoft Hive ODBC-stuurprogramma installeren

Download en installeer het [Microsoft Hive ODBC-stuurprogramma](https://www.microsoft.com/download/details.aspx?id=40886).

## <a name="create-apache-hive-odbc-data-source"></a>Apache Hive ODBC-gegevensbron maken

De volgende stappen laten zien hoe u een Apache Hive ODBC-gegevensbron maakt.

1. Ga vanuit Windows naar **Start** > **Windows-beheertools** > **ODBC-gegevensbronnen (32-bits)/(64-bits)** .  Een venster **ODBC-gegevensbronbeheerder** wordt geopend.

    :::image type="content" source="./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png " alt-text="ODBC-gegevensbronbeheerder" border="true":::

1. Op het tabblad **gebruikers-DSN** selecteert u **Toevoegen** om het venster **Nieuwe gegevensbron maken** te openen.

1. Selecteer **Microsoft Hive ODBC-stuurprogramma** en selecteer vervolgens **Voltooien** om het venster **DSN-installatie van Microsoft Hive ODBC-stuurprogramma** te openen.

1. Typ of selecteer de volgende waarden:

   | Eigenschap | Beschrijving |
   | --- | --- |
   |  Naam van de gegevensbron |Geef uw gegevensbron een naam |
   |  Host(s) |Voer `CLUSTERNAME.azurehdinsight.net` in. Bijvoorbeeld: `myHDICluster.azurehdinsight.net` |
   |  Poort |Gebruik **443**.|
   |  Database |Gebruik **standaard**. |
   |  Mechanisme |Selecteer **Windows Azure HDInsight Service** |
   |  Gebruikersnaam |Voer de gebruikersnaam van de HTTP-gebruiker van het HDInsight-cluster in. De standaardgebruikersnaam **admin**. |
   |  Wachtwoord |Voer het gebruikerswachtwoord voor het HDInsight-cluster in. Schakel het selectievakje **Wachtwoord opslaan (versleuteld)** in.|

1. Optioneel: Selecteer **Geavanceerde opties**.  

   | Parameter | Beschrijving |
   | --- | --- |
   |  Systeemeigen query gebruiken |Wanneer het ODBC-stuurprogramma is geselecteerd, probeert het NIET TSQL te converteren naar HiveQL. Gebruik deze optie alleen als u 100% zeker weet dat u zuivere HiveQL-instructies verzendt. Wanneer u verbinding maakt met SQL Server of Azure SQL Database, moet u het selectievakje uitgeschakeld laten. |
   |  Opgehaalde rijen per blok |Bij het ophalen van een groot aantal records is het afstemmen van deze parameter mogelijk vereist om te zorgen voor optimale prestaties. |
   |  Standaardlengte van een tekenreekskolom, binaire kolomlengte, decimale kolomschaal |De lengten en precisie van het gegevenstype kunnen van invloed zijn op hoe gegevens worden geretourneerd. Ze leiden ertoe dat onjuiste gegevens worden geretourneerd vanwege verlies van precisie en afbrekingen. |

    :::image type="content" source="./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png " alt-text="Geavanceerde DSN-configuratieopties" border="true":::

1. Selecteer **Testen** om de gegevensbron te testen. Wanneer de gegevensbron op de juiste wijze is geconfigureerd, geven de testresultaten **GESLAAGD** weer.  

1. Selecteer **OK** om het testvenster te sluiten.  

1. Selecteer **OK** om het venster **DSN-installatie van Microsoft Hive ODBC-stuurprogramma** te sluiten.  

1. Selecteer **OK** om het venster **ODBC-gegevensbronbeheerder** te sluiten.  

## <a name="query-data-with-powershell"></a>Query's uitvoeren op gegevens met PowerShell

Het volgende PowerShell-script is een functie die door ODBC kan worden gebruikt om query's uit te voeren op een Hive-cluster.

```powershell
function Get-ODBC-Data {

   param(
   [string]$query=$(throw 'query is required.'),
   [string]$dsn,  
   [PSCredential] $cred = (Get-Credential)  
   )

   $conn = New-Object System.Data.Odbc.OdbcConnection
   $uname = $cred.UserName

   $pswd = (New-Object System.Net.NetworkCredential -ArgumentList "", $cred.Password).Password
   $conn.ConnectionString = "DSN=$dsn;Uid=$uname;Pwd=$pswd;"
   $conn.open()
   $cmd = New-object System.Data.Odbc.OdbcCommand($query,$conn)

   $ds = New-Object system.Data.DataSet

   (New-Object system.Data.odbc.odbcDataAdapter($cmd)).fill($ds) #| out-null
   $conn.close()
   $ds.Tables
}
```

In het volgende codefragment wordt de bovenstaande functie gebruikt om een query uit te voeren op het Interactive Query-cluster dat u aan het begin van de zelfstudie hebt gemaakt. Vervang `DATASOURCENAME` door de **Gegevensbronnaam** die u hebt opgegeven in het scherm **DSN-installatie van Microsoft Hive ODBC-stuurprogramma**. Wanneer u om referenties wordt gevraagd, voert u de gebruikersnaam en het wachtwoord in die u hebt ingevoerd onder **Gebruikersnaam voor aanmelden bij cluster** en **Wachtwoord voor aanmelden bij cluster** toen u het cluster maakte.

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep, het HDInsight-cluster en het opslagaccount wanneer u deze niet meer nodig hebt. Als u dit wilt doen, selecteert u de resourcegroep waarin het cluster is gemaakt en klikt u op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u het Microsoft Hive ODBC-stuurprogramma en PowerShell kunt gebruiken om gegevens op te halen uit uw Azure HDInsight Interactive Query-cluster.

> [!div class="nextstepaction"]
> [Excel verbinden met Apache Hive met behulp van ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
