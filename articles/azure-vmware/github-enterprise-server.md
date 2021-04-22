---
title: GitHub Enterprise Server instellen in uw Azure VMware Solution privécloud
description: Meer informatie over het instellen van GitHub Enterprise Server op uw Azure VMware Solution privécloud.
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 0ff7ab87d7401cd3faaecf149fb1b07be3f8db42
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862935"
---
# <a name="set-up-github-enterprise-server-on-your-azure-vmware-solution-private-cloud"></a>GitHub Enterprise Server instellen in uw Azure VMware Solution privécloud

In dit artikel doorlopen we de stappen voor het instellen van GitHub Enterprise Server, de 'on-premises' versie van [GitHub.com](https://github.com/), op uw Azure VMware Solution privécloud. Het scenario dat we behandelen, is een GitHub Enterprise Server-exemplaar dat maximaal 3000 ontwikkelaars kan bedienen met maximaal 25 taken per minuut op GitHub Actions. Het bevat de installatie van (op het moment van schrijven) *preview-functies,* zoals GitHub Actions. Als u de installatie wilt aanpassen aan uw specifieke behoeften, bekijkt u de vereisten die worden vermeld in [GitHub Enterprise Server installeren op VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations).

## <a name="before-you-begin"></a>Voordat u begint

GitHub Enterprise Server vereist een geldige licentiesleutel. U kunt zich aanmelden voor een [proeflicentie](https://enterprise.github.com/trial). Als u de mogelijkheden van GitHub Enterprise Server via een integratie wilt uitbreiden, komt u mogelijk in aanmerking voor een gratis ontwikkelaarslicentie met vijf plaatsen. Vraag deze licentie aan via [het Partnerprogramma van GitHub.](https://partner.github.com/)

## <a name="installing-github-enterprise-server-on-vmware"></a>GitHub Enterprise Server installeren op VMware

Download [de huidige versie van GitHub Enterprise Server](https://enterprise.github.com/releases/2.19.0/download) voor VMware ESXi/vSphere (OVA) en implementeer de [OVA-sjabloon die](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) u hebt gedownload.

:::image type="content" source="media/github-enterprise-server/github-options.png" alt-text="Kies ervoor om GitHub on-premises of in de cloud uit te voeren.":::  

:::image type="content" source="media/github-enterprise-server/deploy-ova-template.png" alt-text="Implementeer de OVA-sjabloon.":::  

Geef een herkenbare naam op voor uw nieuwe virtuele machine, zoals GitHubEnterpriseServer. U hoeft de releasedetails niet op te nemen in de naam van de VM, omdat deze gegevens verouderd raken wanneer het exemplaar wordt bijgewerkt. Selecteer alle standaardwaarden voor nu (we gaan deze details binnenkort bewerken) en wacht tot de OVA is geïmporteerd.

Pas na het [importeren de hardwareconfiguratie aan](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#creating-the-github-enterprise-server-instance) op basis van uw behoeften. In ons voorbeeldscenario hebben we de volgende configuratie nodig.

| Resource | Standaardinstallatie | Standaard instellen + Bètafuncties (acties) |
| --- | --- | --- |
| vCPUs | 4 | 8 |
| Geheugen | 32 GB | 61 GB |
| Gekoppelde opslag | 250 GB | 300 GB |
| Hoofdopslag | 200 GB | 200 GB |

Uw behoeften kunnen echter variëren. Raadpleeg de richtlijnen voor hardwareoverwegingen in GitHub Enterprise Server installeren [op VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations). Zie ook [Cpu- of geheugenbronnen toevoegen voor VMware om](https://docs.github.com/en/enterprise/admin/enterprise-management/increasing-cpu-or-memory-resources#adding-cpu-or-memory-resources-for-vmware) de hardwareconfiguratie aan te passen op basis van uw situatie.

## <a name="configuring-the-github-enterprise-server-instance"></a>Het GitHub Enterprise Server-exemplaar configureren

:::image type="content" source="media/github-enterprise-server/install-github-enterprise.png" alt-text="Installeer GitHub Enterprise.":::  

Nadat de zojuist inrichtende virtuele machine (VM) is ingeschakeld, configureert [u deze via uw browser](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#configuring-the-github-enterprise-server-instance). U moet uw licentiebestand uploaden en een wachtwoord voor de beheerconsole instellen. Schrijf dit wachtwoord op een veilige plaats op.

:::image type="content" source="media/github-enterprise-server/ssh-access.png" alt-text="Toegang tot de beheershell via SSH.":::    

We raden u aan ten minste de volgende stappen uit te voeren:

1. Upload een openbare SSH-sleutel naar de beheerconsole, zodat u via SSH toegang hebt [tot de beheershell.](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh) 

2. [Configureer TLS op uw exemplaar](https://docs.github.com/en/enterprise/admin/configuration/configuring-tls) zodat u een certificaat kunt gebruiken dat is ondertekend door een vertrouwde certificeringsinstantie.

:::image type="content" source="media/github-enterprise-server/configuring-your-instance.png" alt-text="Uw exemplaar configureren.":::

Pas uw instellingen toe.  Terwijl het exemplaar opnieuw wordt opgestart, kunt u doorgaan met de volgende stap: Blob Storage **configureren GitHub Actions**.

:::image type="content" source="media/github-enterprise-server/create-admin-account.png" alt-text="Maak uw beheerdersaccount.":::

Nadat het exemplaar opnieuw is opgestart, kunt u een nieuw beheerdersaccount maken voor het exemplaar. Noteer ook het wachtwoord van deze gebruiker.

### <a name="other-configuration-steps"></a>Andere configuratiestappen

Als u uw exemplaar wilt harden voor productiegebruik, worden de volgende optionele installatiestappen aanbevolen:

1. Configureer [hoge beschikbaarheid](https://help.github.com/enterprise/admin/guides/installation/configuring-github-enterprise-for-high-availability/) voor beveiliging tegen:

    - Software-crashes (niveau van besturingssysteem of toepassing)
    - Hardwarefouten (opslag, CPU, RAM, e.d.)
    - Virtualisatiehostsysteemfouten
    - Logisch of fysiek ernstig netwerk

2. [Configureer](https://docs.github.com/en/enterprise/admin/configuration/configuring-backups-on-your-appliance) [back-upprogramma's](https://github.com/github/backup-utils)en voorziet in versie-momentopnamen voor herstel na noodgevallen, gehost in beschikbaarheid die gescheiden is van het primaire exemplaar.
3. [Stel subdomeinisolatie in,](https://docs.github.com/en/enterprise/admin/configuration/enabling-subdomain-isolation)met behulp van een geldig TLS-certificaat, om het uitvoeren van scripts op meerdere plaatsen en andere gerelateerde beveiligingsproblemen te beperken.

## <a name="configuring-blob-storage-for-github-actions"></a>Blob-opslag configureren voor GitHub Actions

> [!NOTE]
> GitHub Actions is [momenteel beschikbaar als een beperkte bètaversie op GitHub Enterprise Server release 2.22.](https://docs.github.com/en/enterprise/admin/github-actions)

Externe blobopslag is nodig om de GitHub Actions github enterprise-server (momenteel beschikbaar als bètafunctie). Deze externe blobopslag wordt door Actions gebruikt om artefacten en logboeken op te slaan. Acties op GitHub Enterprise Server [ondersteunen Azure Blob Storage als een opslagprovider](https://docs.github.com/en/enterprise/admin/github-actions/enabling-github-actions-and-configuring-storage#about-external-storage-requirements) (en enkele andere). Daarom gaan we een nieuw Azure-opslagaccount inrichten met het [opslagaccounttype](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts) BlobStorage:

:::image type="content" source="media/github-enterprise-server/storage-account.png" alt-text="Een Azure Blob Storage account inrichten.":::

Zodra de implementatie van de nieuwe BlobStorage-resource is voltooid, kopieert en noteer de connection string (beschikbaar onder Toegangssleutels). We hebben deze tekenreeks zo nodig.

## <a name="setting-up-the-github-actions-runner"></a>De GitHub Actions instellen

> [!NOTE]
> GitHub Actions is [momenteel beschikbaar als een beperkte bètaversie op GitHub Enterprise Server versie 2.22.](https://docs.github.com/en/enterprise/admin/github-actions)

Op dit moment moet u een exemplaar van GitHub Enterprise Server uitvoeren, met een beheerdersaccount gemaakt. U moet ook externe blobopslag hebben die GitHub Actions gebruikt voor persistentie.

We gaan nu ergens een nieuwe GitHub Actions om uit te voeren; opnieuw gebruiken we Azure VMware Solution.

Eerst gaan we een nieuwe VM inrichten in het cluster. We baseren onze VM op [een recente versie van Ubuntu Server](http://releases.ubuntu.com/20.04.1/).

:::image type="content" source="media/github-enterprise-server/provision-new-vm.png" alt-text="Een nieuwe VM inrichten.":::

:::image type="content" source="media/github-enterprise-server/provision-new-vm-2.png" alt-text="Een nieuwe VM inrichten stap 2.":::

Zodra de VM is gemaakt, kunt u deze in- en aansluiten via SSH.

Installeer vervolgens de [actions runner-toepassing,](https://github.com/actions/runner) waarmee een taak wordt uitgevoerd vanuit GitHub Actions werkstroom. Identificeer en download de meest recente Linux x64-release van de Actions-runner, via de [releasepagina](https://github.com/actions/runner/releases) of door het volgende snelle script uit te voeren. Voor dit script moeten zowel curl als [jq](https://stedolan.github.io/jq/) aanwezig zijn op uw VM.

`LATEST\_RELEASE\_ASSET\_URL=$( curl https://api.github.com/repos/actions/runner/releases/latest | \`

`  jq -r '.assets | .[] | select(.name | match("actions-runner-linux-arm64")) | .url' )`

`DOWNLOAD\_URL=$( curl $LATEST\_RELEASE\_ASSET\_URL | \`

`  jq -r '.browser\_download\_url' )`

`curl -OL $DOWNLOAD\_URL`

U hebt nu een lokaal bestand op uw VM, actions-runner-linux-arm64- \* .tar.gz. Extraheerde deze tarball lokaal:

`tar xzf actions-runner-linux-arm64-\*.tar.gz`

Met deze extractie worden een aantal bestanden lokaal uitgepakt, waaronder een script en. Hier komen we later `config.sh` `run.sh` op terug.

## <a name="enabling-github-actions"></a>Het inschakelen GitHub Actions

> [!NOTE]
> GitHub Actions is [momenteel beschikbaar als een beperkte bètaversie op GitHub Enterprise Server versie 2.22.](https://docs.github.com/en/enterprise/admin/github-actions)

Bijna! Laten we deze configureren en inschakelen GitHub Actions het GitHub Enterprise Server-exemplaar. We moeten toegang krijgen tot de beheershell van het [GitHub Enterprise Server-exemplaar via SSH](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh)en vervolgens de volgende opdrachten uitvoeren:

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

Volgende keer uitvoeren:

`ghe-actions-check -s blob`

U ziet uitvoer: 'Blob Storage is in orde'.

Nu GitHub Actions geconfigureerd, kunt u dit inschakelen voor uw gebruikers. Meld u als beheerder aan bij uw GitHub Enterprise Server-exemplaar en selecteer het ![ pictogram Raket.](media/github-enterprise-server/rocket-icon.png) in de rechterbovenhoek van een pagina. Selecteer in de linkerzijbalk **Bedrijfsoverzicht,** vervolgens **Beleid**, **Acties** en selecteer de optie om Acties voor alle organisaties in **teschakelen.**

Configureer vervolgens uw runner op het **tabblad Zelf-hostende hardlopers.** Selecteer **Nieuwe toevoegen** en vervolgens Nieuwe **runner** in de vervolgkeuzekeuze.

Op de volgende pagina ziet u een set opdrachten die moeten worden uitgevoerd.  We hoeven alleen de opdracht te kopiëren om de runner te configureren, bijvoorbeeld:

`./config.sh --url https://10.1.1.26/enterprises/octo-org --token AAAAAA5RHF34QLYBDCHWLJC7L73MA`

Kopieer de `config.sh` opdracht en plak deze in een sessie op uw Actions Runner (eerder gemaakt).

:::image type="content" source="media/github-enterprise-server/actions-runner.png" alt-text="Runner van acties.":::

Gebruik de run.sh om de *runner uit* te voeren:

:::image type="content" source="media/github-enterprise-server/run-runner.png" alt-text="Voer de runner uit.":::

Als u deze runner beschikbaar wilt maken voor organisaties in uw onderneming, bewerkt u de toegang tot de organisatie:

:::image type="content" source="media/github-enterprise-server/edit-runner-access.png" alt-text="Toegang tot runner bewerken.":::

Hier maken we het beschikbaar voor alle organisaties, maar u kunt de toegang beperken tot een subset van organisaties en zelfs tot specifieke opslagplaatsen.

## <a name="optional-configuring-github-connect"></a>(Optioneel) GitHub Connect configureren

Hoewel deze stap optioneel is, raden we deze aan als u van plan bent opensource-acties te gebruiken die beschikbaar zijn op GitHub.com. Hiermee kunt u voortbouwen op het werk van anderen door te verwijzen naar deze herbruikbare acties in uw werkstromen.

Als u GitHub Connect wilt inschakelen, volgt u de stappen in Automatische toegang tot GitHub.com [met behulp van GitHub Connect.](https://docs.github.com/en/enterprise/admin/github-actions/enabling-automatic-access-to-githubcom-actions-using-github-connect)

Zodra GitHub Connect is ingeschakeld, selecteert u de optie Server om acties van GitHub.com **in workflow runs te** gebruiken.

:::image type="content" source="media/github-enterprise-server/enable-using-actions.png" alt-text="Inschakelen met behulp van acties GitHub.com in werkstroom wordt uitgevoerd.":::

## <a name="setting-up-and-running-your-first-workflow"></a>Uw eerste werkstroom instellen en uitvoeren

Nu Actions en GitHub Connect zijn ingesteld, gaan we al dit werk goed gebruiken. Hier is een voorbeeldwerkstroom die verwijst naar de uitstekende [octokit/request-action,](https://github.com/octokit/request-action)zodat we GitHub kunnen 'scripten' via interacties met behulp van de GitHub-API, powered by GitHub Actions.

In deze basiswerkstroom gebruiken we om een probleem op GitHub te openen `octokit/request-action` met behulp van de API.

:::image type="content" source="media/github-enterprise-server/workflow-example.png" alt-text="Voorbeeldwerkstroom.":::

>[!NOTE]
>GitHub.com host de actie, maar wanneer deze wordt uitgevoerd  op GitHub Enterprise Server, wordt automatisch de GitHub Enterprise Server-API gebruikt.

Als u ervoor hebt gekozen om GitHub Connect niet in teschakelen, kunt u de volgende alternatieve werkstroom gebruiken.

:::image type="content" source="media/github-enterprise-server/workflow-example-2.png" alt-text="Alternatieve voorbeeldwerkstroom.":::

Navigeer naar een repo op uw exemplaar en voeg de bovenstaande werkstroom toe als: `.github/workflows/hello-world.yml`

:::image type="content" source="media/github-enterprise-server/workflow-example-3.png" alt-text="Een andere voorbeeldwerkstroom.":::

Wacht op **het** tabblad Acties voor uw repo tot de werkstroom is uitgevoerd.

:::image type="content" source="media/github-enterprise-server/executed-example-workflow.png" alt-text="Voorbeeldwerkstroom uitgevoerd.":::

U kunt ook zien hoe het wordt verwerkt door de runner.

:::image type="content" source="media/github-enterprise-server/workflow-processed-by-runner.png" alt-text="Werkstroom verwerkt door runner.":::

Als alles goed is uitgevoerd, ziet u een nieuw probleem in uw repo met de naam 'Hallo wereld'.

:::image type="content" source="media/github-enterprise-server/example-in-repo.png" alt-text="Voorbeeld in de repo.":::

Gefeliciteerd U hebt zojuist uw eerste actions-werkstroom op GitHub Enterprise Server voltooid, die wordt uitgevoerd in Azure VMware Solution privécloud.

In dit artikel hebben we een nieuw exemplaar van GitHub Enterprise Server, het zelf-hostende equivalent van GitHub.com, ingesteld op uw Azure VMware Solution privécloud. Dit exemplaar biedt ondersteuning voor GitHub Actions en gebruikt Azure Blob Storage voor persistentie van logboeken en artefacten. Maar we maken alleen een scratch van wat u met uw GitHub Actions. Bekijk de lijst met acties op [de Marketplace van GitHub](https://github.com/marketplace)of [maak uw eigen](https://docs.github.com/en/actions/creating-actions).

## <a name="next-steps"></a>Volgende stappen

Nu u het instellen van GitHub Enterprise Server in uw Azure VMware Solution privécloud hebt behandeld, wilt u mogelijk meer informatie over: 

- [Aan de slag met GitHub Actions](https://docs.github.com/en/actions)
- [Deelnemen aan het bètaprogramma](https://resources.github.com/beta-signup/)
- [Beheer van GitHub Enterprise Server](https://githubtraining.github.io/admin-training/#/00_getting_started)
