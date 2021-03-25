---
title: Aan de slag met Azure Service Fabric-CLI
description: Lees hier alles over het gebruik van de CLI van Azure Service Fabric. Informatie over verbinding maken met een cluster en het beheren van toepassingen.
author: jeffj6123
ms.topic: conceptual
ms.date: 5/19/2020
ms.author: jejarry
ms.openlocfilehash: ff322cb3633616e9004bc8fa25b048b6f1263008
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046331"
---
# <a name="azure-service-fabric-cli"></a>Azure Service Fabric-CLI

De opdrachtregelinterface (CLI) van Azure Service Fabric is een opdrachtregelprogramma voor interactie met en het beheren van Service Fabric-entiteiten. De Service Fabric-CLI kan worden gebruikt met Windows- of Linux-clusters. De Service Fabric-CLI kan worden uitgevoerd op elk platform dat ondersteuning biedt voor Python.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="prerequisites"></a>Vereisten

Voorafgaand aan de installatie controleert u of zowel Python als pip is geïnstalleerd in uw omgeving. Zie de [Quick Start-documentatie voor pip](https://pip.pypa.io/en/latest/quickstart/) en de officiële [installatiedocumentatie voor python](https://wiki.python.org/moin/BeginnersGuide/Download) voor meer informatie.

De CLI ondersteunt python-versies 2,7 en 3.6 +, met python 3. x aanbevolen.

### <a name="service-fabric-target-runtime"></a>Service Fabric-doelruntime

De Service Fabric-CLI is bedoeld ter ondersteuning van de nieuwste runtimeversie van de Service Fabric-SDK. Gebruik de volgende tabel om te bepalen welke CLI-versie u moet installeren:

| CLI-versie   | ondersteunde runtimeversie |
|---------------|---------------------------|
| Nieuwste (~ = 10) | Nieuwste (~ = 7,1)            |
| 9.0.0         | 7.1                       |
| 8.0.0         | 6.5                       |
| 7.1.0         | 6.4                       |
| 6.0.0         | 6.3                       |
| 5.0.0         | 6,2                       |
| 4.0.0         | 6.1                       |
| 3.0.0         | 6.0                       |
| 1.1.0         | 5.6, 5.7                  |

U kunt optioneel een doelversie van de CLI opgeven om te installeren, door het achtervoegsel `==<version>` achter de opdracht `pip install` te plaatsen. Bijvoorbeeld, voor versie 1.1.0 is de syntaxis:

```shell
pip install -I sfctl==1.1.0
```

Vervang de volgende `pip install`-opdracht indien nodig door de eerder genoemde opdracht.

Zie de [GitHub-documentatie](https://github.com/Azure/service-fabric-cli/releases) voor meer informatie over Service Fabric CLI-versies.

## <a name="install-pip-python-and-the-service-fabric-cli"></a>pip, Python en de Service Fabric-CLI installeren

Er zijn diverse manieren waarop u pip en Python op uw platform kunt installeren. Hier volgen enkele stappen waarmee u Python 3 en pip snel op de bekendste besturingssystemen kunt installeren.

### <a name="windows"></a>Windows

Voor Windows 10, Windows Server 2016 en Windows Server 2012 R2 gebruikt u de officiële standaardinstallatie-instructies. Tijdens de installatie van python wordt pip ook standaard geïnstalleerd.

1. Ga naar de officiële [python-pagina met Down loads](https://www.python.org/downloads/)en down load de meest recente versie van python 3. x.

2. Start het installatieprogramma.

3. Selecteer aan de onderkant van de prompt de optie **python 3. x toevoegen aan pad**.

4. Selecteer **Install Now** en voltooi de installatie.

U kunt nu een nieuw opdrachtvenster openen en de versie van Python en pip opvragen.

```shell
python --version
pip --version
```

Voer vervolgens de volgende opdracht uit om de Azure Service Fabric CLI (sfctl) te installeren en de CLI-Help-pagina weer te geven:

```shell
pip install sfctl
sfctl -h
```

### <a name="ubuntu-and-windows-subsystem-for-linux"></a>Ubuntu en Windows-subsysteem voor Linux

Voer de volgende opdrachten uit om de Service Fabric-CLI te installeren:

```bash
sudo apt-get install python3
sudo apt-get install python3-pip
pip3 install sfctl
```

Daarna kunt u de installatie testen met:

```bash
sfctl -h
```

Als u de fout Opdracht niet gevonden krijgt, zoals:

`sfctl: command not found`

Controleer dan of `~/.local/bin` toegankelijk is vanuit het `$PATH`:

```bash
export PATH=$PATH:~/.local/bin
echo "export PATH=$PATH:~/.local/bin" >> .shellrc
```

Als de installatie op Windows-subsysteem voor Linux mislukt vanwege onjuiste mapmachtigingen, kunt u het opnieuw proberen met verhoogde machtigingen:

```bash
sudo pip3 install sfctl
```

### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (ondersteuning voor Service Fabric-preview)

Voer de volgende opdrachten uit om Service Fabric CLI te installeren op Red Hat:

```bash
sudo yum install -y python38
sudo yum install python38-setuptools
sudo easy_install-3.4 pip
sudo pip3 install sfctl
```

Voor het testen van de installatie kunt u verwijzen naar de stappen die worden beschreven in de sectie **Ubuntu en Windows-subsysteem voor Linux**

<a name = "cli-mac"></a>
### <a name="macos"></a>MacOS

Voor Mac OS is het raadzaam om [het pakketbeheerprogramma HomeBrew](https://brew.sh) te gebruiken. Installeer HomeBrew als dat nog niet is gebeurd door de volgende opdracht uit te voeren:

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Installeer vervolgens vanaf de Terminal de meest recente python 3. x, PIP en de Service Fabric CLI door de volgende opdrachten uit te voeren:

```bash
brew install python3
pip3 install sfctl
sfctl -h
```

## <a name="cli-syntax"></a>De syntaxis van de CLI

Opdrachten worden altijd voorafgegaan door `sfctl`. Voor algemene informatie over alle opdrachten die u kunt gebruiken, gebruikt u `sfctl -h`. Gebruik `sfctl <command> -h` voor hulp bij één opdracht.

Opdrachten volgen een herhaalbare structuur, met het doel van de opdracht vóór de bewerking of actie.

```shell
sfctl <object> <action>
```

In dit voorbeeld is `<object>` het doel voor `<action>`.

## <a name="select-a-cluster"></a>Een cluster selecteren

U kunt pas bewerkingen uitvoeren nadat u een cluster hebt geselecteerd waarmee u verbinding wilt maken. Als u bijvoorbeeld het cluster met de naam `testcluster.com` wilt selecteren en er verbinding mee wilt maken, voert u de volgende opdracht uit:

> [!WARNING]
> Gebruik geen niet-beveiligde Service Fabric-clusters in een productieomgeving.

```shell
sfctl cluster select --endpoint http://testcluster.com:19080
```

Het clustereindpunt moet worden voorafgegaan door `http` of `https`. Het moet de poort voor de HTTP-gateway bevatten. De poort en het adres komen overeen met de Service Fabric Explorer-URL.

Voor clusters die zijn beveiligd met een certificaat, kunt u een met PEM gecodeerd certificaat opgeven. Het certificaat kan worden opgegeven als een enkel bestand of als een certificaat-sleutelpaar. Als het een zelfondertekend certificaat is dat niet is ondertekend door een CA, kunt u de optie `--no-verify` kiezen om CA-verificatie over te slaan.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Zie [Verbinding maken met een beveiligd Azure Service Fabric-cluster](service-fabric-connect-to-secure-cluster.md) voor meer informatie.

## <a name="basic-operations"></a>Basisbewerkingen

De gegevens van een clusterverbinding blijven behouden tussen meerdere Service Fabric-CLI-sessies. Nadat u een Service Fabric-cluster hebt geselecteerd, kunt u elke Service Fabric-opdracht in het cluster uitvoeren.

Als u bijvoorbeeld de status van het Service Fabric-cluster wilt weten, voert u de volgende opdracht uit:

```shell
sfctl cluster health
```

De opdracht resulteert in de volgende uitvoer:

```json
{
  "aggregatedHealthState": "Ok",
  "applicationHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "name": "fabric:/System"
    }
  ],
  "healthEvents": [],
  "nodeHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "id": {
        "id": "66aa824a642124089ee474b398d06a57"
      },
      "name": "_Test_0"
    }
  ],
  "unhealthyEvaluations": []
}
```

## <a name="tips-and-troubleshooting"></a>Tips en problemen oplossen

Hier volgen enkele suggesties en tips voor het oplossen van veelvoorkomende problemen.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Een certificaat converteren van PFX- naar PEM-indeling

De CLI van Service Fabric ondersteunt clientcertificaten als PEM-bestanden (extensie .pem). Als u PFX-bestanden van Windows gebruikt, moet u deze certificaten converteren naar PEM-indeling. Gebruik de volgende opdracht om een PFX-bestand te converteren naar een PEM-bestand:

```shell
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Op een vergelijkbare manier kunt u de volgende opdracht gebruiken (hier wordt geen wachtwoord verstrekt) om een PEM-bestand te converteren naar een PFX-bestand:

```shell
openssl  pkcs12 -export -out Certificates.pfx -inkey Certificates.pem -in Certificates.pem -passout pass:'' 
```

Voor meer informatie raadpleegt u de [OpenSSL-documentatie](https://www.openssl.org/docs/).

### <a name="connection-problems"></a>Verbindingsproblemen

Bepaalde bewerkingen genereren mogelijk het volgende bericht:

`Failed to establish a new connection`

Controleer of het opgegeven clustereindpunt beschikbaar is en luistert. Controleer ook of de gebruikersinterface van Service Fabric Explorer beschikbaar is op die host en poort. Gebruik `sfctl cluster select` om het eindpunt bij te werken.

### <a name="detailed-logs"></a>Gedetailleerde logboeken

Gedetailleerde logboeken zijn vaak nuttig zijn wanneer u fouten opspoort of een probleem meldt. `--debug`Met de vlag neemt de uitgebreidheid van de uitvoer toe.

### <a name="command-help-and-syntax"></a>Syntaxis van Help-opdracht

Als u hulp nodig hebt met een specifieke opdracht of een groep opdrachten, gebruikt u de `-h`-vlag.

```shell
sfctl application -h
```

Hier volgt nog een voorbeeld:

```shell
sfctl application create -h
```

## <a name="updating-the-service-fabric-cli"></a>De Service Fabric-CLI bijwerken 

Voer de volgende opdrachten uit om de Service Fabric-CLI bij te werken (vervang `pip` door `pip3`, afhankelijk van wat u tijdens de oorspronkelijke installatie hebt gekozen):

```shell
pip uninstall sfctl
pip install sfctl
```

## <a name="next-steps"></a>Volgende stappen

* [Een toepassing implementeren met de Azure Service Fabric-CLI](service-fabric-application-lifecycle-sfctl.md)
* [Aan de slag met Service Fabric in Linux](service-fabric-get-started-linux.md)
