---
title: Script acties ontwikkelen voor het aanpassen van Azure HDInsight-clusters
description: Meer informatie over het gebruik van bash-scripts voor het aanpassen van HDInsight-clusters. Met script acties kunt u scripts uitvoeren tijdens of na het maken van een cluster om configuratie-instellingen van het cluster te wijzigen of extra software te installeren.
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/28/2019
ms.openlocfilehash: b6705728fddc9a5a3c9cb8eb2f1811412fb3a290
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945472"
---
# <a name="script-action-development-with-hdinsight"></a>Ontwikkeling van script acties met HDInsight

Meer informatie over het aanpassen van uw HDInsight-cluster met behulp van bash-scripts. Met script acties kunt u HDInsight aanpassen tijdens of na het maken van het cluster.

## <a name="what-are-script-actions"></a>Wat zijn script acties?

Script acties zijn bash-scripts die door Azure worden uitgevoerd op de cluster knooppunten om configuratie wijzigingen aan te brengen of software te installeren. Een script actie wordt uitgevoerd als root en biedt volledige toegangs rechten voor de cluster knooppunten.

Script acties kunnen worden toegepast met behulp van de volgende methoden:

| Gebruik deze methode om een script toe te passen... | Tijdens het maken van het cluster... | Op een actief cluster... |
| --- |:---:|:---:|
| Azure Portal |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| Klassieke versie van Azure-CLI |&nbsp; |✓ |
| HDInsight .NET SDK |✓ |✓ |
| Azure Resource Manager-sjabloon |✓ |&nbsp; |

Zie [HDInsight-clusters aanpassen met script acties](hdinsight-hadoop-customize-cluster-linux.md)voor meer informatie over het gebruik van deze methoden voor het Toep assen van script acties.

## <a name="best-practices-for-script-development"></a><a name="bestPracticeScripting"></a>Aanbevolen procedures voor het ontwikkelen van scripts

Wanneer u een aangepast script voor een HDInsight-cluster ontwikkelt, zijn er verschillende aanbevolen procedures om u te bedenken:

* [De Apache Hadoop versie van het doel](#bPS1)
* [Doel versie van het besturings systeem](#bps10)
* [Stabiele koppelingen naar script bronnen geven](#bPS2)
* [Vooraf gecompileerde resources gebruiken](#bPS4)
* [Zorg ervoor dat het script voor het aanpassen van het cluster idempotent is](#bPS3)
* [Hoge Beschik baarheid van de cluster architectuur garanderen](#bPS5)
* [De aangepaste onderdelen configureren voor het gebruik van Azure Blob-opslag](#bPS6)
* [Gegevens schrijven naar STDOUT en STDERR](#bPS7)
* [Bestanden opslaan als ASCII-regel eindigend op LF](#bps8)
* [Pogings logica gebruiken om te herstellen van tijdelijke fouten](#bps9)

> [!IMPORTANT]  
> Script acties moeten binnen 60 minuten worden voltooid of het proces mislukt. Tijdens het inrichten van het knoop punt wordt het script gelijktijdig uitgevoerd met andere installatie-en configuratie processen. De concurrentie voor bronnen zoals CPU-tijd of netwerk bandbreedte kan ertoe leiden dat het script meer tijd in beslag neemt dan in uw ontwikkel omgeving.

### <a name="target-the-apache-hadoop-version"></a><a name="bPS1"></a>De Apache Hadoop versie van het doel

Voor verschillende versies van HDInsight zijn verschillende versies van Hadoop-Services en onderdelen geïnstalleerd. Als uw script een specifieke versie van een service of onderdeel verwacht, moet u het script alleen gebruiken met de versie van HDInsight die de vereiste onderdelen bevat. U kunt informatie vinden over onderdeel versies die zijn opgenomen in HDInsight met behulp van het document [versie beheer van HDInsight-onderdelen](hdinsight-component-versioning.md) .

### <a name="checking-the-operating-system-version"></a>De versie van het besturings systeem controleren

Verschillende versies van HDInsight zijn afhankelijk van specifieke versies van Ubuntu. Er zijn mogelijk verschillen tussen versies van het besturings systeem die u moet controleren in uw script. Het is bijvoorbeeld mogelijk dat u een binair element moet installeren dat is gekoppeld aan de versie van Ubuntu.

Gebruik om de versie van het besturings systeem te controleren `lsb_release` . Het volgende script laat bijvoorbeeld zien hoe u kunt verwijzen naar een specifiek tar-bestand, afhankelijk van de versie van het besturings systeem:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
```

### <a name="target-the-operating-system-version"></a><a name="bps10"></a> De versie van het besturings systeem als doel instellen

HDInsight is gebaseerd op de Ubuntu Linux-distributie. Verschillende versies van HDInsight zijn afhankelijk van verschillende versies van Ubuntu, die kunnen veranderen hoe uw script zich gedraagt. HDInsight 3,4 en eerder zijn bijvoorbeeld gebaseerd op Ubuntu-versies die gebruikmaken van upstart. Versie 3,5 en hoger zijn gebaseerd op Ubuntu 16,04, dat gebruikmaakt van gesystemed. Systeem-en Upstarts zijn afhankelijk van verschillende opdrachten, zodat uw script moet worden geschreven om te werken met beide.

Een ander belang rijk verschil tussen HDInsight 3,4 en 3,5 is dat `JAVA_HOME` nu naar Java 8 wijst. De volgende code laat zien hoe u kunt bepalen of het script wordt uitgevoerd op Ubuntu 14 of 16:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
...
if [[ $OS_VERSION == 16* ]]; then
    echo "Using systemd configuration"
    systemctl daemon-reload
    systemctl stop webwasb.service    
    systemctl start webwasb.service
else
    echo "Using upstart configuration"
    initctl reload-configuration
    stop webwasb
    start webwasb
fi
...
if [[ $OS_VERSION == 14* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
elif [[ $OS_VERSION == 16* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
fi
```

U kunt het volledige script vinden dat deze fragmenten bevat op https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh .

Voor de versie van Ubuntu die door HDInsight wordt gebruikt, raadpleegt u het versie document van [HDInsight-onderdelen](hdinsight-component-versioning.md) .

Zie voor meer informatie over de verschillen tussen het systeem en het starten [van een systeem voor upstarted users](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="provide-stable-links-to-script-resources"></a><a name="bPS2"></a>Stabiele koppelingen naar script bronnen geven

Het script en de bijbehorende resources moeten beschikbaar blijven gedurende de levens duur van het cluster. Deze resources zijn vereist als er nieuwe knoop punten worden toegevoegd aan het cluster tijdens het schalen.

Het best practice is om alles in een Azure Storage account in uw abonnement te downloaden en te archiveren.

> [!IMPORTANT]  
> Het gebruikte opslag account moet het standaard opslag account voor het cluster of een open bare, alleen-lezen container op een ander opslag account zijn.

De voor beelden die door micro soft worden verschaft, worden bijvoorbeeld opgeslagen in het [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) opslag account. Deze locatie is een open bare, alleen-lezen container die wordt onderhouden door het HDInsight-team.

### <a name="use-pre-compiled-resources"></a><a name="bPS4"></a>Vooraf gecompileerde resources gebruiken

Voorkom de tijd die nodig is om het script uit te voeren. Vermijd bewerkingen die resources van de bron code compileren. U kunt bijvoorbeeld resources vooraf compileren en deze opslaan in een Azure Storage-account-Blob in hetzelfde Data Center als HDInsight.

### <a name="ensure-that-the-cluster-customization-script-is-idempotent"></a><a name="bPS3"></a>Zorg ervoor dat het script voor het aanpassen van het cluster idempotent is

Scripts moeten worden idempotent. Als het script meerdere keren wordt uitgevoerd, moet het cluster elke keer naar dezelfde status worden geretourneerd.

Een script dat bijvoorbeeld configuratie bestanden wijzigt, mag geen dubbele vermeldingen toevoegen als er meerdere keren worden uitgevoerd.

### <a name="ensure-high-availability-of-the-cluster-architecture"></a><a name="bPS5"></a>Hoge Beschik baarheid van de cluster architectuur garanderen

HDInsight-clusters op basis van Linux bieden twee hoofd knooppunten die actief zijn binnen het cluster en script acties worden uitgevoerd op beide knoop punten. Als de onderdelen die u installeert slechts één hoofd knooppunt verwachten, installeert u de onderdelen niet op beide hoofd knooppunten.

> [!IMPORTANT]  
> Services die deel uitmaken van HDInsight, zijn zo ontworpen dat ze naar behoefte failover tussen de twee hoofd knooppunten. Deze functionaliteit wordt niet uitgebreid naar aangepaste onderdelen die zijn geïnstalleerd via script acties. Als u een hoge Beschik baarheid voor aangepaste onderdelen nodig hebt, moet u uw eigen failover-mechanisme implementeren.

### <a name="configure-the-custom-components-to-use-azure-blob-storage"></a><a name="bPS6"></a>De aangepaste onderdelen configureren voor het gebruik van Azure Blob-opslag

Onderdelen die u op het cluster installeert, hebben mogelijk een standaard configuratie die gebruikmaakt van Apache Hadoop Distributed File System (HDFS)-opslag. HDInsight maakt gebruik van Azure Storage of Data Lake Storage als standaard opslag. Beide bieden een met HDFS compatibel bestands systeem dat gegevens persistent maakt, zelfs als het cluster wordt verwijderd. U moet mogelijk onderdelen configureren die u installeert voor gebruik van WASB of ADL in plaats van HDFS.

Voor de meeste bewerkingen hoeft u het bestands systeem niet op te geven. In het volgende voor beeld wordt het bestand Hadoop-common. jar van het lokale bestands systeem gekopieerd naar de cluster opslag:

```bash
hdfs dfs -put /usr/hdp/current/hadoop-client/hadoop-common.jar /example/jars/
```

In dit voor beeld `hdfs` gebruikt de opdracht de standaard cluster opslag op transparante wijze. Voor sommige bewerkingen moet u mogelijk de URI opgeven. Bijvoorbeeld `adl:///example/jars` voor Azure data Lake Storage gen1, `abfs:///example/jars` voor data Lake Storage Gen2 of `wasb:///example/jars` voor Azure Storage.

### <a name="write-information-to-stdout-and-stderr"></a><a name="bPS7"></a>Gegevens schrijven naar STDOUT en STDERR

HDInsight registreert script uitvoer die is geschreven naar STDOUT en STDERR. U kunt deze informatie weer geven met behulp van de Ambari-webgebruikersinterface.

> [!NOTE]  
> Apache Ambari is alleen beschikbaar als het cluster is gemaakt. Als u een script actie gebruikt tijdens het maken van het cluster en het maken mislukt, raadpleegt u [script acties oplossen](./troubleshoot-script-action.md) voor andere manieren om toegang te krijgen tot geregistreerde gegevens.

De meeste hulpprogram ma's en installatie pakketten schrijven al informatie naar STDOUT en STDERR, maar u wilt mogelijk extra logboek registratie toevoegen. Als u tekst wilt verzenden naar STDOUT, gebruikt u `echo` . Bijvoorbeeld:

```bash
echo "Getting ready to install Foo"
```

`echo`De teken reeks wordt standaard verzonden naar stdout. Voeg vóór toe om het naar STDERR te sturen `>&2` `echo` . Bijvoorbeeld:

```bash
>&2 echo "An error occurred installing Foo"
```

Hiermee wordt informatie die naar STDOUT is geschreven, omgeleid naar STDERR (2). Zie voor meer informatie over i/o-omleiding [https://www.tldp.org/LDP/abs/html/io-redirection.html](https://www.tldp.org/LDP/abs/html/io-redirection.html) .

Zie [problemen met script acties oplossen](./troubleshoot-script-action.md)voor meer informatie over het weer geven van gegevens die zijn geregistreerd door script acties.

### <a name="save-files-as-ascii-with-lf-line-endings"></a><a name="bps8"></a> Bestanden opslaan als ASCII-regel eindigend op LF

Bash-scripts moeten worden opgeslagen als ASCII-indeling, waarbij regels worden beëindigd door LF. Bestanden die zijn opgeslagen als UTF-8, of CRLF gebruiken als de regel die eindigt, kan mislukken met de volgende fout:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="use-retry-logic-to-recover-from-transient-errors"></a><a name="bps9"></a> Pogings logica gebruiken om te herstellen van tijdelijke fouten

Bij het downloaden van bestanden, het installeren van pakketten met apt-get of andere acties waarmee gegevens via internet worden verzonden, kan de actie mislukken vanwege tijdelijke netwerk fouten. De externe bron waarmee u communiceert, kan bijvoorbeeld worden overgeschakeld naar een back-upknooppunt.

Als u uw script tot tijdelijke fouten wilt laten overgaan, kunt u de logica voor opnieuw proberen implementeren. De volgende functie laat zien hoe u nieuwe logica implementeert. Er wordt drie keer opnieuw geprobeerd om de bewerking uit te kunnen laten slagen.

```bash
#retry
MAXATTEMPTS=3

retry() {
    local -r CMD="$@"
    local -i ATTMEPTNUM=1
    local -i RETRYINTERVAL=2

    until $CMD
    do
        if (( ATTMEPTNUM == MAXATTEMPTS ))
        then
                echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                return 1
        else
                echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                sleep $(( RETRYINTERVAL ))
                ATTMEPTNUM=$ATTMEPTNUM+1
        fi
    done
}
```

De volgende voor beelden laten zien hoe u deze functie kunt gebruiken.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helper-methods-for-custom-scripts"></a><a name="helpermethods"></a>Hulp methoden voor aangepaste scripts

Hulp methoden voor script acties zijn hulpprogram ma's die u kunt gebruiken bij het schrijven van aangepaste scripts. Deze methoden zijn opgenomen in het [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) script. Gebruik het volgende om ze te downloaden en te gebruiken als onderdeel van het script:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

De volgende helpers die beschikbaar zijn voor gebruik in uw script:

| Help-gebruik | Description |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Hiermee wordt een bestand gedownload van de bron-URI naar het opgegeven bestandspad. Standaard wordt een bestaand bestand niet overschreven. |
| `untar_file TARFILE DESTDIR` |Hiermee wordt een tar-bestand (met) uitgepakt `-xf` naar de doelmap. |
| `test_is_headnode` |Als het script is uitgevoerd op een hoofd knooppunt van het cluster, retour neren 1; in andere gevallen, 0. |
| `test_is_datanode` |Als het huidige knoop punt een Data (worker)-knoop punt is, retourneert een 1; in andere gevallen, 0. |
| `test_is_first_datanode` |Als het huidige knoop punt het eerste gegevens knooppunt (worker) (met de naam workernode0) is, retourneert een 1; in andere gevallen, 0. |
| `get_headnodes` |Retourneert de Fully Qualified Domain Name van de hoofd knooppunten in het cluster. Namen worden gescheiden door komma's. Een lege teken reeks wordt geretourneerd als fout. |
| `get_primary_headnode` |Hiermee wordt de Fully Qualified Domain Name van de primaire hoofd knooppunt opgehaald. Een lege teken reeks wordt geretourneerd als fout. |
| `get_secondary_headnode` |Hiermee wordt de Fully Qualified Domain Name van de secundaire hoofd knooppunt opgehaald. Een lege teken reeks wordt geretourneerd als fout. |
| `get_primary_headnode_number` |Hiermee wordt het numerieke achtervoegsel van de primaire hoofd knooppunt opgehaald. Een lege teken reeks wordt geretourneerd als fout. |
| `get_secondary_headnode_number` |Hiermee wordt het numerieke achtervoegsel van de secundaire hoofd knooppunt opgehaald. Een lege teken reeks wordt geretourneerd als fout. |

## <a name="common-usage-patterns"></a><a name="commonusage"></a>Algemene gebruiks patronen

Deze sectie bevat richt lijnen voor het implementeren van een aantal algemene gebruiks patronen die u kunt uitvoeren terwijl u uw eigen aangepaste script schrijft.

### <a name="passing-parameters-to-a-script"></a>Para meters door geven aan een script

In sommige gevallen kan het script para meters nodig hebben. Het is bijvoorbeeld mogelijk dat u het beheerders wachtwoord voor het cluster nodig hebt wanneer u de Ambari-REST API gebruikt.

Para meters die aan het script worden door gegeven, worden ook wel *positionele para meters* genoemd en worden toegewezen aan `$1` voor de eerste para meter, `$2` voor de tweede, enzovoort. `$0` bevat de naam van het script zelf.

Waarden die worden door gegeven aan het script als para meters moeten tussen enkele aanhalings tekens (') worden geplaatst. Dit zorgt ervoor dat de door gegeven waarde wordt beschouwd als een letterlijke teken reeks.

### <a name="setting-environment-variables"></a>Omgevings variabelen instellen

Het instellen van een omgevings variabele wordt uitgevoerd met de volgende instructie:

```bash
VARIABLENAME=value
```

In het voor gaande voor beeld `VARIABLENAME` is de naam van de variabele. Gebruik om toegang te krijgen tot de variabele `$VARIABLENAME` . Als u bijvoorbeeld een waarde wilt toewijzen die wordt opgegeven door een positionele para meter als een omgevings variabele met de naam wacht woord, gebruikt u de volgende instructie:

```bash
PASSWORD=$1
```

Volgende toegang tot de informatie kan vervolgens worden gebruikt `$PASSWORD` .

Omgevings variabelen die in het script zijn ingesteld, bestaan alleen binnen het bereik van het script. In sommige gevallen moet u mogelijk omgevings variabelen voor het hele systeem toevoegen die blijven bestaan nadat het script is voltooid. Als u omgevings variabelen voor het hele systeem wilt toevoegen, voegt u de variabele toe aan `/etc/environment` . De volgende instructie voegt bijvoorbeeld `HADOOP_CONF_DIR` :

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Toegang tot locaties waar de aangepaste scripts zijn opgeslagen

Scripts die worden gebruikt voor het aanpassen van een cluster, moeten worden opgeslagen op een van de volgende locaties:

* Een __Azure Storage-account__ dat aan het cluster is gekoppeld.

* Een __extra opslag account__ dat aan het cluster is gekoppeld.

* Een __openbaar Lees bare URI__. Bijvoorbeeld een URL naar gegevens die zijn opgeslagen in OneDrive, Dropbox of een andere service voor het hosten van bestanden.

* Een __Azure data Lake Storage-account__ dat is gekoppeld aan het HDInsight-cluster. Zie [Quick Start: clusters instellen in hdinsight](./hdinsight-hadoop-provision-linux-clusters.md)voor meer informatie over het gebruik van Azure data Lake Storage met hdinsight.

    > [!NOTE]  
    > De Service-Principal HDInsight gebruikt om toegang te krijgen tot Data Lake Storage moet lees toegang hebben tot het script.

Resources die worden gebruikt door het script, moeten ook openbaar beschikbaar zijn.

Het opslaan van bestanden in een Azure Storage account of Azure Data Lake Storage biedt snelle toegang, zowel binnen het Azure-netwerk.

> [!NOTE]  
> De URI-indeling die wordt gebruikt om te verwijzen naar het script verschilt, afhankelijk van de service die wordt gebruikt. Gebruik of voor opslag accounts die zijn gekoppeld aan het HDInsight-cluster `wasb://` `wasbs://` . Gebruik of voor openbaar Lees bare `http://` uri's `https://` . Gebruik voor Data Lake Storage `adl://` .

## <a name="checklist-for-deploying-a-script-action"></a><a name="deployScript"></a>Controle lijst voor het implementeren van een script actie

Dit zijn de stappen die worden uitgevoerd bij het voorbereiden van het implementeren van een script:

* Plaats de bestanden die de aangepaste scripts bevatten op een locatie die tijdens de implementatie toegankelijk is voor de cluster knooppunten. Bijvoorbeeld de standaard opslag voor het cluster. Bestanden kunnen ook worden opgeslagen in openbaar Lees bare hosting services.
* Controleer of het script idempotent is. Hierdoor kan het script meerdere keren op hetzelfde knoop punt worden uitgevoerd.
* Gebruik een tijdelijke map/tmp om de gedownloade bestanden die worden gebruikt door de scripts te blijven gebruiken en deze vervolgens op te schonen nadat de scripts zijn uitgevoerd.
* Als de instellingen op het niveau van het besturings systeem of de configuratie bestanden van de Hadoop-service worden gewijzigd, kunt u HDInsight-services opnieuw starten.

## <a name="how-to-run-a-script-action"></a><a name="runScriptAction"></a>Een script actie uitvoeren

U kunt script acties gebruiken om HDInsight-clusters aan te passen met behulp van de volgende methoden:

* Azure Portal
* Azure PowerShell
* Azure Resource Manager-sjablonen
* De HDInsight .NET SDK.

Zie [How to use script Action](hdinsight-hadoop-customize-cluster-linux.md)(Engelstalig) voor meer informatie over het gebruik van elke methode.

## <a name="custom-script-samples"></a><a name="sampleScripts"></a>Voor beelden van aangepaste scripts

Micro soft biedt voorbeeld scripts voor het installeren van onderdelen in een HDInsight-cluster. Zie [kleur Toon op HDInsight-clusters installeren en gebruiken](hdinsight-hadoop-hue-linux.md) als een voorbeeld script actie.

## <a name="troubleshooting"></a>Problemen oplossen

Hieronder vindt u fouten die kunnen optreden bij het gebruik van scripts die u hebt ontwikkeld:

**Fout**: `$'\r': command not found` . Soms gevolgd door `syntax error: unexpected end of file` .

*Oorzaak*: deze fout wordt veroorzaakt wanneer de regels in een script EINDIGEN met CRLF. UNIX-systemen verwachten alleen LF als de regel eindigt.

Dit probleem treedt meestal op wanneer het script wordt gemaakt in een Windows-omgeving, aangezien CRLF een gemeen schappelijke lijn eindigt voor veel tekst editors in Windows.

*Oplossing*: als het een optie in de tekst editor is, selecteert u UNIX-indeling of LF voor de regel die eindigt. U kunt ook de volgende opdrachten op een UNIX-systeem gebruiken om de CRLF te wijzigen in een LF:

> [!NOTE]  
> De volgende opdrachten zijn ongeveer gelijk aan die moeten worden gewijzigd de CRLF-regel eindigt op LF. Selecteer een op basis van de hulpprogram ma's die beschikbaar zijn op uw systeem.

| Opdracht | Opmerkingen |
| --- | --- |
| `unix2dos -b INFILE` |Er wordt een back-up gemaakt van het oorspronkelijke bestand met een. BAK-extensie |
| `tr -d '\r' < INFILE > OUTFILE` |Out-bestand bevat een versie met alleen LF-einden |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Hiermee wordt het bestand rechtstreeks gewijzigd |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |Out-bestand bevat een versie met alleen LF-einden. |

**Fout**: `line 1: #!/usr/bin/env: No such file or directory` .

*Oorzaak*: deze fout treedt op wanneer het script is opgeslagen als UTF-8 met een byte order Mark (bom).

*Oplossing*: Sla het bestand op als ASCII of als UTF-8 zonder een stuk lijst. U kunt ook de volgende opdracht op een Linux-of UNIX-systeem gebruiken om een bestand te maken zonder de stuk lijst:

```bash
awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE
```

Vervang door `INFILE` het bestand dat de stuk lijst bevat. `OUTFILE` moet een nieuwe bestands naam zijn die het script bevat zonder de stuk lijst.

## <a name="next-steps"></a><a name="seeAlso"></a>Volgende stappen

* Meer informatie over het [aanpassen van HDInsight-clusters met behulp van script acties](hdinsight-hadoop-customize-cluster-linux.md)
* Gebruik de [referentie voor hdinsight .NET SDK](/dotnet/api/overview/azure/hdinsight) voor meer informatie over het maken van .NET-toepassingen voor het beheren van HDInsight
* Gebruik het [HDInsight-rest API](/rest/api/hdinsight/) om te leren hoe u rest kunt gebruiken om beheer acties uit te voeren op HDInsight-clusters.