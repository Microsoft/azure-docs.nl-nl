---
title: GitHub Enter prise server instellen op uw persoonlijke cloud van Azure VMware-oplossing
description: Meer informatie over het instellen van GitHub Enter prise server op uw Azure VMware-oplossing privécloud.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 00b3acf721dd7f7a1a15bcd0d24eccf3ca27ff58
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326910"
---
# <a name="set-up-github-enterprise-server-on-your-azure-vmware-solution-private-cloud"></a>GitHub Enter prise server instellen op uw persoonlijke cloud van Azure VMware-oplossing

In dit artikel worden de stappen beschreven voor het instellen van GitHub Enter prise server, de ' on-premises ' versie van [github.com](https://github.com/), op uw persoonlijke cloud van Azure VMware-oplossing. Het scenario dat in deze procedure wordt behandeld, is bedoeld voor een GitHub Enter prise Server-exemplaar dat Maxi maal 3.000 ontwikkel aars kan ondersteunen met Maxi maal 25 taken per minuut op GitHub-acties. Het bevat de instellingen van (op het moment van schrijven) *Preview* -functies, zoals github-acties. Als u de installatie voor uw specifieke behoeften wilt aanpassen, raadpleegt u de vereisten die worden vermeld in [github Enter prise server installeren op VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations).

## <a name="before-you-begin"></a>Voordat u begint

GitHub Enter prise server vereist een geldige licentie sleutel. U kunt zich aanmelden voor een [proef licentie](https://enterprise.github.com/trial). Als u de mogelijkheden van GitHub Enter prise server wilt uitbreiden via een integratie, kunt u in aanmerking komen voor een gratis licentie voor de ontwikkel aars van vijf seat. Gelden voor deze licentie via [het partner programma van github](https://partner.github.com/).

## <a name="installing-github-enterprise-server-on-vmware"></a>GitHub Enter prise server installeren op VMware

Down load [de huidige versie van github Enter prise server](https://enterprise.github.com/releases/2.19.0/download) voor VMware ESXi/VSPHERE (eicellen) en [Implementeer de eicellen-sjabloon](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) die u hebt gedownload.

:::image type="content" source="media/github-enterprise-server/github-options.png" alt-text="Kies voor het uitvoeren van GitHub on-premises of in de Cloud.":::  

:::image type="content" source="media/github-enterprise-server/deploy-ova-template.png" alt-text="De eicellen-sjabloon implementeren.":::  

Geef een herken bare naam op voor de nieuwe virtuele machine, zoals GitHubEnterpriseServer. U hoeft de release details niet op te geven in de naam van de virtuele machine, aangezien deze details verouderd worden wanneer het exemplaar wordt geüpgraded. Selecteer alle standaard waarden voor nu (deze gegevens worden binnenkort gewijzigd) en wacht totdat de eicellen zijn geïmporteerd.

Nadat de configuratie is geïmporteerd, [past u de hardwareconfiguratie](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#creating-the-github-enterprise-server-instance) aan op basis van uw behoeften. In het voorbeeld scenario hebt u de volgende configuratie nodig.

| Resource | Standaard instellingen | Standaard instellingen + ' bèta functies ' (acties) |
| --- | --- | --- |
| vCPUs | 4 | 8 |
| Geheugen | 32 GB | 61 GB |
| Gekoppelde opslag | 250 GB | 300 GB |
| Hoofd opslag | 200 GB | 200 GB |

Uw behoeften kunnen echter verschillen. Raadpleeg de richt lijnen over hardwareproblemen in [github Enter prise server installeren op VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations)voor meer informatie. Zie ook [CPU of geheugen resources toevoegen voor VMware](https://docs.github.com/en/enterprise/admin/enterprise-management/increasing-cpu-or-memory-resources#adding-cpu-or-memory-resources-for-vmware) om de hardwareconfiguratie aan te passen op basis van uw situatie.

## <a name="configuring-the-github-enterprise-server-instance"></a>Het GitHub Enter prise Server-exemplaar configureren

:::image type="content" source="media/github-enterprise-server/install-github-enterprise.png" alt-text="Installeer GitHub Enter prise.":::  

Nadat de zojuist ingerichte virtuele machine (VM) is ingeschakeld, configureert u [deze via uw browser](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#configuring-the-github-enterprise-server-instance). U moet uw licentie bestand uploaden en een wacht woord voor de beheer console instellen. Zorg ervoor dat u dit wacht woord ergens anders schrijft.

:::image type="content" source="media/github-enterprise-server/ssh-access.png" alt-text="Open de beheer shell via SSH.":::    

We raden u aan om ten minste de volgende stappen uit te voeren:

1. Upload een open bare SSH-sleutel naar de beheer console, zodat u [via SSH toegang kunt krijgen tot de beheer shell](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh). 

2. [CONFIGUREER TLS op uw exemplaar](https://docs.github.com/en/enterprise/admin/configuration/configuring-tls) zodat u een certificaat kunt gebruiken dat is ondertekend door een vertrouwde certificerings instantie.

:::image type="content" source="media/github-enterprise-server/configuring-your-instance.png" alt-text="Uw exemplaar configureren.":::

Pas uw instellingen toe.  Terwijl het exemplaar opnieuw wordt gestart, kunt u door gaan met de volgende stap en **Blob Storage configureren voor github-acties**.

:::image type="content" source="media/github-enterprise-server/create-admin-account.png" alt-text="Maak uw beheerders account.":::

Wanneer het exemplaar opnieuw is opgestart, maakt u een nieuw beheerders account op het exemplaar. Zorg er ook voor dat u het wacht woord van deze gebruiker noteert.

### <a name="additional-configuration-steps"></a>Aanvullende configuratie stappen

Als u uw exemplaar wilt beveiligen voor gebruik in de productie omgeving, kunt u het beste de volgende optionele installatie stappen uitvoeren:

1. [Hoge Beschik baarheid](https://help.github.com/enterprise/admin/guides/installation/configuring-github-enterprise-for-high-availability/) voor beveiliging configureren voor:

    - Software crashes (OS-of toepassings niveau)
    - Hardwarestoringen (opslag, CPU, RAM, enzovoort)
    - Storingen in virtualisatie hostsysteem
    - Logisch of fysiek ernstig netwerk

2. [Configure](https://docs.github.com/en/enterprise/admin/configuration/configuring-backups-on-your-appliance) [Back-ups configureren-hulpprogram ma's](https://github.com/github/backup-utils), voorzien in versie-moment opnamen voor herstel na nood gevallen, gehost in Beschik baarheid die gescheiden is van het primaire exemplaar.
3. De [isolatie van subdomeinen instellen](https://docs.github.com/en/enterprise/admin/configuration/enabling-subdomain-isolation)met behulp van een geldig TLS-certificaat om cross-site scripting en andere gerelateerde beveiligings problemen te beperken.

## <a name="configuring-blob-storage-for-github-actions"></a>Blob Storage configureren voor GitHub-acties

> [!NOTE]
> GitHub-acties zijn [momenteel beschikbaar als beperkte bèta op github Enter prise Server versie 2,22](https://docs.github.com/en/enterprise/admin/github-actions).

Externe Blob-opslag is nodig voor het inschakelen van GitHub-acties op GitHub Enter prise server (momenteel beschikbaar als een ' bèta-functie). Deze externe Blob-opslag wordt gebruikt door acties om artefacten en logboeken op te slaan. Acties op GitHub Enter prise server [ondersteunen Azure Blob Storage als een opslag provider](https://docs.github.com/en/enterprise/admin/github-actions/enabling-github-actions-and-configuring-storage#about-external-storage-requirements) (en andere). Daarom richten we een nieuw Azure-opslag account in met het [type opslag account](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts) van BlobStorage:

:::image type="content" source="media/github-enterprise-server/storage-account.png" alt-text="Blob Storage-account van Azure inrichten.":::

Nadat de implementatie van de nieuwe BlobStorage-resource is voltooid, kopieert u de connection string (beschikbaar onder toegangs sleutels) en noteert u deze. Deze teken reeks is binnenkort vereist.

## <a name="setting-up-the-github-actions-runner"></a>De acties voor het uitvoeren van GitHub instellen

> [!NOTE]
> GitHub-acties zijn [momenteel beschikbaar als beperkte bèta op github Enter prise Server versie 2,22](https://docs.github.com/en/enterprise/admin/github-actions).

Op dit moment moet er een exemplaar van GitHub Enter prise server worden uitgevoerd, waarbij een beheerders account is gemaakt. U moet ook externe Blob Storage hebben die GitHub-acties voor persistentie gebruiken.

We gaan nu ergens maken voor het uitvoeren van GitHub-acties. de Azure VMware-oplossing wordt opnieuw gebruikt.

Eerst gaan we een nieuwe VM op het cluster inrichten. We baseren onze VM op [een recente release van Ubuntu Server](http://releases.ubuntu.com/20.04.1/).

:::image type="content" source="media/github-enterprise-server/provision-new-vm.png" alt-text="Richt een nieuwe VM in.":::

:::image type="content" source="media/github-enterprise-server/provision-new-vm-2.png" alt-text="Richt u op een nieuwe VM-stap 2.":::

Zodra de VM is gemaakt, kunt u deze inschakelen via SSH en er verbinding mee maken.

Installeer vervolgens de toepassing voor het uitvoeren van [acties](https://github.com/actions/runner) , waarmee een taak wordt uitgevoerd vanuit een werk stroom met github-acties. Identificeer en down load de meest recente Linux x64-versie van de acties loper, hetzij via [de pagina releases](https://github.com/actions/runner/releases) of door het volgende snelle script uit te voeren. Dit script vereist dat krul en [JQ](https://stedolan.github.io/jq/) aanwezig zijn op uw virtuele machine.

`LATEST\_RELEASE\_ASSET\_URL=$( curl https://api.github.com/repos/actions/runner/releases/latest | \`

`  jq -r '.assets | .[] | select(.name | match("actions-runner-linux-arm64")) | .url' )`

`DOWNLOAD\_URL=$( curl $LATEST\_RELEASE\_ASSET\_URL | \`

`  jq -r '.browser\_download\_url' )`

`curl -OL $DOWNLOAD\_URL`

Nu moet u een bestand lokaal op uw virtuele machine hebben, acties uitvoeren-Linux-arm64- \* . tar. gz. Deze tarball lokaal extra heren:

`tar xzf actions-runner-linux-arm64-\*.tar.gz`

Met deze extractie worden een aantal bestanden lokaal uitgepakt, inclusief een `config.sh` en `run.sh` -script, die we binnenkort teruggaan.

## <a name="enabling-github-actions"></a>GitHub-acties inschakelen

> [!NOTE]
> GitHub-acties zijn [momenteel beschikbaar als beperkte bèta op github Enter prise Server versie 2,22](https://docs.github.com/en/enterprise/admin/github-actions).

Bijna klaar. Laten we GitHub-acties configureren en inschakelen op het GitHub Enter prise Server-exemplaar. U moet toegang hebben [tot de beheer shell van de GitHub Enter prise Server-instantie via SSH](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh)en vervolgens de volgende opdrachten uitvoeren:

`# set an environment variable containing your Blob storage connection string`

`export CONNECTION\_STRING="<your connection string from the blob storage step>"`

`# configure actions storage`

`ghe-config secrets.actions.storage.blob-provider azure`

`ghe-config secrets.actions.storage.azure.connection-string "$CONNECTION\_STRING"`

`# apply these settings`

`ghe-config-apply`

`# execute a precheck, this install additional software required by Actions on GitHub Enterprise Server`

`ghe-actions-precheck -p azure -cs "$CONNECTION\_STRING"`

`# enable actions, and re-apply the config`

`ghe-config app.actions.enabled true`

`ghe-config-apply`

Volgende uitvoering:

`ghe-actions-check -s blob`

U ziet de uitvoer: "Blob Storage is in orde".

Nu de GitHub-acties zijn geconfigureerd, schakelt u deze voor uw gebruikers in. Meld u aan bij uw GitHub Enter prise Server-exemplaar als beheerder en selecteer het ![ pictogram raket.](media/github-enterprise-server/rocket-icon.png) in de rechter bovenhoek van elke pagina. In de zijbalk links selecteert u **Enter prise Overview**, vervolgens **beleids regels**, **acties** en selecteert u de optie om **acties voor alle organisaties in te scha kelen**.

Configureer vervolgens uw loper van het tabblad **zelf-hostende lopers** . Selecteer **nieuwe toevoegen** en vervolgens **nieuwe loper** in de vervolg keuzelijst.

Op de volgende pagina ziet u een reeks opdrachten die moeten worden uitgevoerd. u hoeft alleen de opdracht te kopiëren om de loper te **configureren** , bijvoorbeeld:

`./config.sh --url https://10.1.1.26/enterprises/octo-org --token AAAAAA5RHF34QLYBDCHWLJC7L73MA`

Kopieer de `config.sh` opdracht en plak deze in een sessie van uw acties loper (eerder gemaakt).

:::image type="content" source="media/github-enterprise-server/actions-runner.png" alt-text="Acties uitvoeren.":::

Gebruik de opdracht run.sh om de loper *uit te voeren* :

:::image type="content" source="media/github-enterprise-server/run-runner.png" alt-text="Voer de loper uit.":::

Als u deze loper beschikbaar wilt maken voor organisaties in uw onderneming, bewerkt u de toegang tot de organisatie:

:::image type="content" source="media/github-enterprise-server/edit-runner-access.png" alt-text="Toegang voor lopers bewerken.":::

Hier gaan we de app beschikbaar maken voor alle organisaties, maar u kunt ook de toegang tot een subset van organisaties beperken, zelfs voor specifieke opslag plaatsen.

## <a name="optional-configuring-github-connect"></a>Beschrijving GitHub Connect configureren

Hoewel deze stap optioneel is, raden we u aan deze te gebruiken als u open-source acties wilt verbruiken die beschikbaar zijn op GitHub.com. Op die manier kunt u op het werk van anderen samen werken door te verwijzen naar deze herbruikbare acties in uw werk stromen.

Als u GitHub Connect wilt inschakelen, volgt u de stappen in [automatische toegang tot github.com-acties inschakelen met behulp van github Connect](https://docs.github.com/en/enterprise/admin/github-actions/enabling-automatic-access-to-githubcom-actions-using-github-connect).

Zodra GitHub Connect is ingeschakeld, selecteert u de optie **Server voor het gebruik van acties van github.com in werk stroom uitvoeringen** .

:::image type="content" source="media/github-enterprise-server/enable-using-actions.png" alt-text="Inschakelen met behulp van acties vanuit GitHub.com in werk stroom uitvoeringen.":::

## <a name="setting-up-and-running-your-first-workflow"></a>Uw eerste werk stroom instellen en uitvoeren

Nu het maken van acties en GitHub-verbinding is ingesteld, gaan we al deze werkzaamheden voor een goed gebruik. Hier volgt een voor beeld van een werk stroom die verwijst naar de uitstekende [octokit/Request-Action](https://github.com/octokit/request-action), waardoor het ' script ' github via interacties kan worden uitgevoerd met behulp van de GITHUB-API, mogelijk gemaakt door github-acties.

In deze basis werk stroom gebruiken we `octokit/request-action` voor het openen van een probleem op github met behulp van de API.

:::image type="content" source="media/github-enterprise-server/workflow-example.png" alt-text="Voorbeeld werk stroom.":::

>[!NOTE]
>GitHub.com fungeert als host voor de actie, maar wanneer deze wordt uitgevoerd op GitHub Enter prise-server, wordt *automatisch* de GitHub Enter prise Server-API gebruikt.

Als u ervoor hebt gekozen om GitHub Connect niet in te scha kelen, kunt u de volgende alternatieve werk stroom gebruiken.

:::image type="content" source="media/github-enterprise-server/workflow-example-2.png" alt-text="Alternatieve voorbeeld werk stroom.":::

Navigeer naar een opslag plaats in uw exemplaar en voeg de bovenstaande werk stroom toe als: `.github/workflows/hello-world.yml`

:::image type="content" source="media/github-enterprise-server/workflow-example-3.png" alt-text="Een andere voorbeeld werk stroom.":::

Wacht tot de werk stroom is uitgevoerd op het tabblad **acties** van uw opslag plaats.

:::image type="content" source="media/github-enterprise-server/executed-example-workflow.png" alt-text="De voorbeeld werk stroom is uitgevoerd.":::

U kunt ook bekijken dat het wordt verwerkt door de loper.

:::image type="content" source="media/github-enterprise-server/workflow-processed-by-runner.png" alt-text="Werk stroom verwerkt door loper.":::

Als alles goed is uitgevoerd, ziet u een nieuw probleem in uw opslag plaats, getiteld "Hallo wereld".

:::image type="content" source="media/github-enterprise-server/example-in-repo.png" alt-text="Voor beeld in opslag plaats.":::

Gefeliciteerd. U hebt zojuist uw eerste werk stroom voor acties voltooid op GitHub Enter prise server, die wordt uitgevoerd op uw persoonlijke cloud van Azure VMware-oplossing.

We gaan gewoon het Opper vlak van wat u kunt doen met GitHub-acties. Voor meer inspiratie kunt u de lijst met acties op [Marketplace van github](https://github.com/marketplace)afhandelen of [uw eigen actie maken](https://docs.github.com/en/actions/creating-actions).

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebben we een nieuw exemplaar van GitHub Enter prise server ingesteld, het zelf-hostende equivalent van GitHub.com, bovenop uw persoonlijke cloud van Azure VMware-oplossingen. Dit exemplaar bevat ondersteuning voor GitHub-acties en maakt gebruik van Azure Blob Storage voor persistentie van Logboeken en artefacten. Dit is een fantastische combi natie van een moderne, samen werkende en veilige ervaring op het gebied van software ontwikkeling. Het is gebaseerd op een solide basis van de Azure VMware-oplossing, zodat u cloud resources in een vertrouwde instelling kunt gebruiken.

Raadpleeg de volgende bronnen voor meer informatie:

- [Aan de slag met GitHub-acties](https://docs.github.com/en/actions)
- [Word lid van het bèta programma](https://resources.github.com/beta-signup/)
- [Meer informatie over het beheer van GitHub Enter prise server](https://githubtraining.github.io/admin-training/#/00_getting_started)