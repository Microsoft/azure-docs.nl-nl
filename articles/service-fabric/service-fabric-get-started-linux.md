---
title: Uw ontwikkel omgeving instellen in Linux
description: Installeer de runtime en SDK en maak een lokaal ontwikkelcluster in Linux. Zodra u dit hebt gedaan, kunt u toepassingen bouwen.
ms.topic: conceptual
ms.date: 10/16/2020
ms.custom: devx-track-js
ms.openlocfilehash: 14b8a278605a908b4182c724831b2e42de54a753
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93086887"
---
# <a name="prepare-your-development-environment-on-linux"></a>Uw ontwikkelomgeving voorbereiden in Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [Mac OS X](service-fabric-get-started-mac.md)

Als u [Azure Service Fabric-toepassingen](service-fabric-application-model.md) op uw Linux-ontwikkelmachine wilt implementeren en uitvoeren, moet u de runtime en algemene SDK installeren. U kunt ook optionele SDK's voor Java en .NET Core-ontwikkeling installeren. 

Bij de stappen in dit artikel wordt ervan uitgegaan dat u systeem eigen op Linux installeert of dat u de [service Fabric Onebox-container installatie kopie](https://hub.docker.com/_/microsoft-service-fabric-onebox)gebruikt, dat wil zeggen `mcr.microsoft.com/service-fabric/onebox:u18` .

U kunt Service Fabric entiteiten die worden gehost in de Cloud of on-premises beheren met de Azure Service Fabric opdracht regel interface (CLI). Zie [De Service Fabric-CLI instellen](./service-fabric-cli.md) voor meer informatie over het installeren van de CLI.


## <a name="prerequisites"></a>Vereisten

Deze besturingssysteemversies worden ondersteund voor ontwikkeling.

* Ubuntu 16,04 ( `Xenial Xerus` ), 18,04 ( `Bionic Beaver` )

    Zorg ervoor dat het pakket `apt-transport-https` is geïnstalleerd.
         
    ```bash
    sudo apt-get install apt-transport-https
    ```
* Red Hat Enterprise Linux 7.4 (ondersteuning voor Service Fabric-preview)


## <a name="installation-methods"></a>Installatiemethoden

<!-- markdownlint-disable MD025 -->
<!-- markdownlint-disable MD024 -->

# <a name="ubuntu"></a>[Ubuntu](#tab/sdksetupubuntu)

## <a name="update-your-apt-sources"></a>Uw APT-bronnen bijwerken
Voor het installeren van de SDK en het bijbehorende runtimepakket via het apt-get-opdrachtregelprogramma, moet u eerst uw APT-bronnen (Advanced Packaging Tool) bijwerken.

## <a name="script-installation"></a>Installatie van script

Voor het gemak wordt een script gegeven om de Service Fabric runtime en de Service Fabric algemene SDK samen met de [ **sfctl** cli](service-fabric-cli.md)te installeren. Als u het script uitvoert, wordt ervan uitgegaan dat u akkoord gaat met de licenties voor alle software die wordt geïnstalleerd. U kunt ook de [hand matige installatie](#manual-installation) stappen in de volgende sectie uitvoeren, zodat hieraan gekoppelde licenties worden gepresenteerd, evenals de onderdelen die worden geïnstalleerd.

Nadat het script is uitgevoerd, kunt u verdergaan met [Een lokaal cluster instellen](#set-up-a-local-cluster).

```bash
sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
```

## <a name="manual-installation"></a>Handmatige installatie
Volg de rest van deze handleiding voor handmatige installatie van de Service Fabric-runtime en de algemene SDK.

1. Open een terminal.

2. Voeg de `dotnet` opslag plaats toe aan de lijst met bronnen die overeenkomt met uw distributie.

    ```bash
    wget -q https://packages.microsoft.com/config/ubuntu/$(lsb_release -rs)/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    ```

3. Voeg de nieuwe MS open tech GNU Privacy Guard-sleutel (GnuPG of GPG) toe aan uw APT-sleutel hanger.

    ```bash
    sudo curl -fsSL https://packages.microsoft.com/keys/msopentech.asc | sudo apt-key add -
    ```

4. Voeg de officiële GPG-sleutel van Docker toe aan uw APT-sleutelring.

    ```bash
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

5. Stel de Docker-opslagplaats in.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

6. Voeg Azul JDK-sleutel toe aan uw APT sleutel hanger en stel de opslag plaats in.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9
    sudo apt-add-repository "deb http://repos.azul.com/azure-only/zulu/apt stable main"
    ```

7. Vernieuw uw pakketlijsten op basis van de net toegevoegde opslagplaatsen.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-a-local-cluster"></a>De Service Fabric SDK installeren en instellen voor een lokaal cluster

Wanneer u uw bronnen hebt bijgewerkt, kunt u de SDK installeren. Installeer het Service Fabric SDK-pakket, bevestig de installatie en accepteer de gebruiksrechtovereenkomst.

### <a name="ubuntu"></a>Ubuntu

```bash
sudo apt-get install servicefabricsdkcommon
```

> [!TIP]
>   Met de volgende opdrachten kunt u het accepteren van de licentie voor Service Fabric-pakketten automatiseren:
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-ga select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-ga select true" | sudo debconf-set-selections
>   ```

# <a name="red-hat-enterprise-linux-74"></a>[Red Hat Enterprise Linux 7.4](#tab/sdksetuprhel74)

## <a name="update-your-yum-repositories"></a>Uw yum-opslag plaatsen bijwerken
Als u de SDK en het bijbehorende runtime pakket via het yum-opdracht regel programma wilt installeren, moet u eerst uw pakket bronnen bijwerken.

## <a name="manual-installation-rhel"></a>Hand matige installatie (RHEL)
Volg de rest van deze handleiding voor handmatige installatie van de Service Fabric-runtime en de algemene SDK.

1. Open een terminal.
2. Download en installeer extra pakketten voor Enterprise Linux (EPEL).

    ```bash
    wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    sudo yum install epel-release-latest-7.noarch.rpm
    ```
3. Voeg de EfficiOS RHEL7-pakketopslagplaats toe aan het systeem.

    ```bash
    sudo wget -P /etc/yum.repos.d/ https://packages.efficios.com/repo.files/EfficiOS-RHEL7-x86-64.repo
    ```

4. Importeer de aanmeldingssleutel voor het EfficiOS-pakket naar de lokale GPG-sleutelhanger.

    ```bash
    sudo rpmkeys --import https://packages.efficios.com/rhel/repo.key
    ```

5. Voeg de Microsoft RHEL-opslagplaats toe aan het systeem.

    ```bash
    curl https://packages.microsoft.com/config/rhel/7.4/prod.repo > ./microsoft-prod.repo
    sudo cp ./microsoft-prod.repo /etc/yum.repos.d/
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-a-local-cluster-rhel"></a>De Service Fabric SDK installeren en instellen voor een lokaal cluster (RHEL)

Wanneer u uw bronnen hebt bijgewerkt, kunt u de SDK installeren. Installeer het Service Fabric SDK-pakket, bevestig de installatie en accepteer de gebruiksrechtovereenkomst.

```bash
sudo yum install servicefabricsdkcommon
```

---

## <a name="included-packages"></a>Inbegrepen pakketten
De Service Fabric-runtime die wordt geleverd met de installatie omvat de pakketten in de volgende tabel. 

 | | DotNetCore | Java | Python | Node.js | 
--- | --- | --- | --- |---
**Ubuntu** | 2.0.7 | AzulJDK 1,8 | Implicit van npm | meest recente |
**RHEL** | - | OpenJDK 1.8 | Implicit van npm | meest recente |

## <a name="set-up-a-local-cluster"></a>Een lokaal cluster instellen
1. Een lokaal Service Fabric cluster starten voor ontwikkeling.

# <a name="container-based-local-cluster"></a>[Lokaal cluster op basis van containers](#tab/localclusteroneboxcontainer)

Start een op een container gebaseerd [service Fabric Onebox](https://hub.docker.com/_/microsoft-service-fabric-onebox) -cluster.

1. Installeer Moby om docker-containers te kunnen implementeren.
    ```bash
    sudo apt-get install moby-engine moby-cli -y
    ```
2. Werk de configuratie van de docker-daemon op uw host bij met de volgende instellingen en start de docker-daemon opnieuw. Details: [IPv6-ondersteuning inschakelen](https://docs.docker.com/config/daemon/ipv6/)

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```

3. Start het cluster.<br/>
    <b>Ubuntu 18,04 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16,04 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > Standaard wordt hierdoor de installatiekopie met de nieuwste versie van Service Fabric opgehaald. Ga naar de [Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/)-pagina als u een bepaalde revisie wilt ophalen.

# <a name="local-cluster"></a>[Lokaal cluster](#tab/localcluster)

Nadat u de SDK hebt geïnstalleerd met behulp van de bovenstaande stappen, start u een lokaal cluster.

1. Voer het installatiescript van het cluster uit.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

---

2. Open een webbrowser en ga naar **service Fabric Explorer** ( `http://localhost:19080/Explorer` ). Wanneer het cluster is gestart, ziet u het Service Fabric Explorer-dashboard. Het kan enkele minuten duren voordat het cluster volledig is ingesteld. Als het openen van de browser is mislukt of als in Service Fabric Explorer niet wordt aangegeven dat het systeem gereed is, wacht u enkele minuten en probeert u het opnieuw.

    ![Service Fabric Explorer in Linux][sfx-linux]

    U kunt nu vooraf samengestelde Service Fabric-toepassingspakketten of nieuwe implementeren op basis van gastcontainers of uitvoerbare gastbestanden. Als u nieuwe services wilt maken met behulp van de Java of .NET Core SDK's, voert u de optionele installatiestappen uit die in de volgende secties worden uitgelegd.


> [!NOTE]
> Zelfstandige clusters worden niet ondersteund in Linux.


> [!TIP]
> Als u een SSD-schijf beschikbaar hebt, is het raadzaam om met behulp van `--clusterdataroot` met devclustersetup.sh een SSD-mappad door te geven voor hogere prestaties.

## <a name="set-up-the-service-fabric-cli"></a>De Service Fabric-CLI instellen

De [Service Fabric-CLI](service-fabric-cli.md) bevat opdrachten voor interactie met Service Fabric-entiteiten, inclusief clusters en toepassingen. Volg de instructies in [Service Fabric-CLI](service-fabric-cli.md) voor het installeren van de CLI.


## <a name="set-up-yeoman-generators-for-containers-and-guest-executables"></a>Yeoman-generatoren instellen voor containers en uitvoerbare gastbestanden
Service Fabric biedt hulpprogramma's waarmee u vanuit een terminal Service Fabric-toepassingen kunt maken met behulp van Yeoman-sjabloongeneratoren. Volg deze stappen om de Service Fabric Yeoman-sjabloongeneratoren in te stellen:

1. Installeer nodejs en NPM op uw computer.

    ```bash
    sudo add-apt-repository "deb https://deb.nodesource.com/node_8.x $(lsb_release -s -c) main"
    sudo apt-get update
    sudo apt-get install nodejs
    ```
2. Installeer de [Yeoman](https://yeoman.io/)-sjabloongenerator op uw computer vanuit NPM.

    ```bash
    sudo npm install -g yo
    ```
3. Installeer de Yeo-containergenerator voor Service Fabric en de generator voor uitvoerbare gastbestanden vanuit NPM.

    ```bash
    sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
    sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
    ```

Nadat u de generatoren hebt geïnstalleerd, kunt u uitvoerbare gastbestanden of containerservices maken door respectievelijk `yo azuresfguest` of `yo azuresfcontainer` uit te voeren.

## <a name="set-up-net-core-31-development"></a>.NET Core 3,1-ontwikkeling instellen

Installeer de [.net Core 3,1 SDK voor Ubuntu](https://www.microsoft.com/net/core#linuxubuntu) om [C# service Fabric-toepassingen te maken](service-fabric-create-your-first-linux-application-with-csharp.md). Pakketten voor .NET Core Service Fabric-toepassingen worden gehost op NuGet.org.

## <a name="set-up-java-development"></a>Java-ontwikkeling instellen

Als u Service Fabric Services wilt maken met behulp van Java, installeert u Gradle om bouw taken uit te voeren. Voer de onderstaande opdracht uit om Gradle te installeren. De Service Fabric-Java-bibliotheken worden opgehaald uit Maven.


* Ubuntu

    ```bash
    curl -s https://get.sdkman.io | bash
    sdk install gradle 5.1
    ```

* Red Hat Enterprise Linux 7.4 (ondersteuning voor Service Fabric-preview)

  ```bash
  sudo yum install java-1.8.0-openjdk-devel
  curl -s https://get.sdkman.io | bash
  sdk install gradle
  ```

U moet ook de Yeo-containergenerator voor Service Fabric installeren voor uitvoerbare Java-bestanden. Zorg ervoor dat u [Yeoman hebt geïnstalleerd](#set-up-yeoman-generators-for-containers-and-guest-executables) en voer de volgende opdracht uit:

  ```bash
  npm install -g generator-azuresfjava
  ```
 
## <a name="install-the-eclipse-plug-in-optional"></a>De Eclipse-invoegtoepassing installeren (optioneel)

U kunt de Eclipse-invoegtoepassing voor Service Fabric installeren vanuit de Eclipse IDE voor Java-ontwikkelaars of Java EE-ontwikkelaars. Met Eclipse kunt u behalve Service Fabric Java-toepassingen ook uitvoerbare Fabric Service-gasttoepassingen en containertoepassingen maken.

> [!IMPORTANT]
> Voor de Service Fabric-invoegtoepassing is Eclipse Neon of een hogere versie vereist. Zie de instructies na deze opmerking voor het controleren van uw Eclipse-versie. Als er een oudere versie van Eclipse is geïnstalleerd, kunt u een nieuwere versie downloaden van de [site van Eclipse](https://www.eclipse.org). We raden u af een oudere versie van Eclipse te overschrijven met een nieuwere versie. Verwijder de oudere versie voordat u het installatieprogramma uitvoert of installeer de nieuwe versie in een andere map.
> 
> Voor Ubuntu wordt u aangeraden de installatie rechtstreeks vanaf de site van Eclipse uit te voeren en niet door middel van een installatieprogramma voor pakketten (`apt` of `apt-get`). Daardoor weet u zeker dat u de meest recente versie van Eclipse hebt. U kunt de Eclipse IDE installeren voor Java-ontwikkelaars of voor Java EE-ontwikkelaars.

1. Controleer in Eclipse of u Eclipse Neon of later en Buildship versie 2.2.1 of later hebt geïnstalleerd. Controleer de versies van geïnstalleerde onderdelen door **Help**  >  **over** de  >  **installatie Details** van de eclips te selecteren. U kunt Buildship bijwerken met behulp van de instructies in [Eclipse Buildship: Eclipse-invoegtoepassingen voor Gradle][buildship-update].

2. Als u de service Fabric-invoeg toepassing wilt installeren, selecteert u **Help**  >  **nieuwe software installeren**.

3. Voer in het vak **werken met** **https: \/ /DL.Microsoft.com/Eclipse** in.

4. Selecteer **Toevoegen**.

    ![Pagina met beschikbare software][sf-eclipse-plugin]

5. Selecteer de **Service Fabric**-invoegtoepassing en klik op **Next**.

6. Voer de installatiestappen uit. Ga vervolgens akkoord met de gebruiksrechtovereenkomst.

Als u de Eclipse-invoegtoepassing voor Service Fabric al hebt geïnstalleerd, controleert u of u de meest recente versie gebruikt. Controleer door **Help**  >  **over** de  >  **installatie Details** van de eclips te selecteren. Zoek vervolgens naar Service Fabric in de lijst met geïnstalleerde invoeg toepassingen. Selecteer **bijwerken** als er een nieuwere versie beschikbaar is.

Zie [Service Fabric-invoegtoepassing voor de ontwikkeling van Eclipse Java-toepassingen](service-fabric-get-started-eclipse.md) voor meer informatie.

## <a name="update-the-sdk-and-runtime"></a>De SDK en runtime bijwerken

Als u wilt bijwerken naar de nieuwste versie van de SDK en runtime, voert u de volgende opdrachten uit.

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon
```
Als u de binaire bestanden uit de Java SDK wilt bijwerken vanaf Maven, moet u de versiegegevens van het bijbehorende binaire bestand in het bestand ``build.gradle`` aanpassen, zodat deze verwijzen naar de nieuwste versie. Als u precies wilt weten waar u de versie moet bijwerken, kunt u kijken naar een ``build.gradle``-bestand in de [voorbeelden om snel aan de slag te gaan met Service Fabric](https://github.com/Azure-Samples/service-fabric-java-getting-started).

> [!NOTE]
> Als de pakketten worden bijgewerkt, kan dit ertoe leiden dat uw lokale ontwikkelcluster wordt stopgezet. Start uw lokale cluster opnieuw op na een upgrade door de instructies in dit artikel te volgen.

## <a name="remove-the-sdk"></a>De SDK verwijderen
Voer de volgende opdrachten uit om de Service Fabric SDK's te verwijderen.

* Ubuntu

    ```bash
    sudo apt-get remove servicefabric servicefabicsdkcommon
    npm uninstall -g generator-azuresfcontainer
    npm uninstall -g generator-azuresfguest
    sudo apt-get install -f
    ```


* Red Hat Enterprise Linux 7.4 (ondersteuning voor Service Fabric-preview)

    ```bash
    sudo yum remove servicefabric servicefabicsdkcommon
    npm uninstall -g generator-azuresfcontainer
    npm uninstall -g generator-azuresfguest
    ```

## <a name="next-steps"></a>Volgende stappen

* [Uw eerste Service Fabric Java-toepassing in Linux maken en implementeren met behulp van Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Uw eerste Service Fabric Java-toepassing in Linux maken en implementeren met behulp van de Service Fabric-invoegtoepassing voor Eclipse](service-fabric-get-started-eclipse.md)
* [Uw eerste CSharp-toepassing in Linux maken](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Uw ontwikkelomgeving voorbereiden in OSX](service-fabric-get-started-mac.md)
* [Een Linux-ontwikkelomgeving voorbereiden in Windows](service-fabric-local-linux-cluster-windows.md)
* [Uw toepassingen beheren met behulp van de Service Fabric-CLI](service-fabric-application-lifecycle-sfctl.md)
* [Verschillen tussen Service Fabric in Windows en Linux](service-fabric-linux-windows-differences.md)
* [Aan de slag met Service Fabric-CLI](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
