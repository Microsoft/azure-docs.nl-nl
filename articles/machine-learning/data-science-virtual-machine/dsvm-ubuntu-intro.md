---
title: 'Quickstart: Een Ubuntu Data Science Virtual Machine maken'
titleSuffix: Azure Data Science Virtual Machine
description: Configureer en maak een Data Science Virtual Machine voor Linux (Ubuntu) voor analyse en machine learning.
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: quickstart
ms.date: 03/10/2020
ms.openlocfilehash: 4a414b706dffae76eaa9841ee7b1fe6bcc1ac0d3
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109841"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-linux-ubuntu"></a>Quickstart: De Data Science Virtual Machine instellen voor Linux (Ubuntu)

Aan de slag met de Ubuntu 18.04 Data Science Virtual Machine.

## <a name="prerequisites"></a>Vereisten

Voor het maken van een Ubuntu 18.04 Data Science Virtual Machine moet u over een Azure-abonnement beschikken. [Azure gratis uitproberen](https://azure.com/free).

>[!NOTE]
>Gratis Azure-accounts bieden geen ondersteuning voor SKU’s voor virtuele machines met GPU ingeschakeld.

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Uw Data Science Virtual Machine voor Linux maken

Hier ziet u de stappen die u moet uitvoeren om een exemplaar van een Ubuntu 18.04 Data Science Virtual Machine te maken:

1. Ga naar de [Azure Portal](https://portal.azure.com). Als u nog niet bent aangemeld bij uw Azure-account, wordt u gevraagd om dit te doen.
1. Zoek de vermelding van de virtuele machine door 'data science virtual machine' te typen en Data Science Virtual Machine - Ubuntu 18.04 te selecteren.

1. Selecteer **Maken** in het volgende venster.

1. U wordt omgeleid naar de blade Een virtuele machine maken.
   
1. Voer de volgende gegevens in voor het configureren van elke stap in de wizard:

    1. **Basisprincipes**:
    
       * **Abonnement**: Als u meer dan één abonnement hebt, selecteert u datgene waarop de machine wordt gemaakt en gefactureerd. U hebt privileges voor het maken van resources nodig voor dit abonnement.
       * **Resourcegroep**: Maak een nieuwe groep of gebruik een bestaande.
       * **Naam van virtuele machine**: Voer de naam van de virtuele machine in. Deze naam wordt gebruikt in uw Azure Portal.
       * **Regio**: Selecteer het geschiktste datacenter. Dit is het datacenter waarop de meeste van uw gegevens zijn opgeslagen of dat zich fysiek het dichtst bij u in de buurt bevindt, voor de snelste netwerktoegang. Meer informatie over [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/).
       * **Installatiekopie**: Laat de standaardwaarde staan.
       * **Grootte**: Deze optie moet automatisch worden gevuld met een grootte die geschikt is voor algemene werkbelastingen. VM [Linux VM-grootten in Azure](../../virtual-machines/sizes.md).
       * **Verificatietype**: Selecteer Wachtwoord voor snellere installatie. 
         
         > [!NOTE]
         > Als u JupyterHub wilt gebruiken moet u Wachtwoord selecteren, omdat JupyterHub *niet* is geconfigureerd voor het gebruik van openbare SSH-sleutels.

       * **Gebruikersnaam**: Voer de gebruikersnaam van de beheerder in. Met deze gebruikersnaam meldt u zich aan bij de virtuele machine. Deze gebruikersnaam hoeft niet hetzelfde te zijn als uw Azure-gebruikersnaam. Gebruik *geen* hoofdletters.
         
         > [!IMPORTANT]
         > Als u hoofdletters gebruikt in de gebruikersnaam, werkt JupyterHub niet en krijgt u een foutmelding 500 Interne serverfout.

       * **Wachtwoord**: Voer het wachtwoord in waarmee u zich gaat aanmelden bij de virtuele machine.    
    
   1. Selecteer **Controleren + maken**.
   1. **Beoordelen en maken**
      * Controleer of alle informatie die u hebt ingevoerd, correct is. 
      * Selecteer **Maken**.
    
    Het inrichten duurt ongeveer 5 minuten. De status wordt weergegeven in de Azure Portal.

## <a name="how-to-access-the-ubuntu-data-science-virtual-machine"></a>Toegang tot de Ubuntu Data Science Virtual Machine

U kunt op een van de volgende drie manieren toegang krijgen tot de Ubuntu DSVM:

  * SSH voor terminalsessies
  * X2Go voor grafische sessies
  * JupyterHub en JupyterLab voor Jupyter-notebooks

### <a name="ssh"></a>SSH

Als u uw virtuele machine met SSH-verificatie hebt geconfigureerd, kunt u zich aanmelden met de accountreferenties die u hebt gemaakt in het gedeelte **Basisinstellingen** van stap 3 voor de tekstshell-interface. In Windows kunt u een SSH-clienthulpprogramma zoals [PuTTY](https://www.putty.org) downloaden. Als u liever een grafisch bureaublad (X Window-systeem) gebruikt, kunt u gebruikmaken van X11-doorsturing in PuTTy.

> [!NOTE]
> In tests presteerde de X2Go-client beter dan X11-doorsturing. Voor een grafische desktop adviseren we het gebruik van de X2Go-client.

### <a name="x2go"></a>X2Go

De virtuele Linux-machine is al ingericht met X2Go Server en is klaar om clientverbindingen te accepteren. Om verbinding te maken met het grafische bureaublad van de virtuele Linux-machine, voert u de volgende procedure uit op de client:

1. Download en installeer de X2Go-client voor uw clientplatform van [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Noteer het openbare IP-adres van de virtuele machine, dat u in de Azure Portal kunt vinden door de virtuele machine te openen die u hebt gemaakt.

   ![IP-adres Ubuntu-machine](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

1. Start de X2Go-client. Als het venster Nieuwe sessie niet automatisch wordt weergegeven, gaat u naar Sessie -> Nieuwe sessie.

1. Voer in het configuratievenster dat wordt geopend de volgende configuratieparameters in:
   * **Tabblad sessie**:
     * **Host**: Voer het IP-adres van uw virtuele machine in, dat u eerder hebt genoteerd.
     * **Aanmelden**: Voer de gebruikersnaam in op de virtuele Linux-machine.
     * **SSH-poort**: Behoud de standaardwaarde 22.
     * **Sessietype**: Wijzig de waarde in **XFCE**. Momenteel ondersteunt de virtuele Linux-machine alleen het XFCE-bureaublad.
   * **Tabblad media**: U kunt ondersteuning voor geluid en afdrukken met de client uitschakelen als u die niet nodig hebt.
   * **Gedeelde mappen**: Gebruik dit tabblad om de map op de clientmachine toe te voegen die u wilt koppelen aan de virtuele machine. 

   ![X2go-configuratie](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. Selecteer **OK**.
1. Klik op het vak in het rechterdeelvenster van het X2Go-venster om het aanmeldingsscherm voor uw virtuele machine weer te geven.
1. Voer het wachtwoord voor de virtuele machine in.
1. Selecteer **OK**.
1. Mogelijk moet u X2Go toestemming geven om uw firewall te omzeilen voor het voltooien van de verbinding.
1. De grafische interface van de Ubuntu Data Science Virtual Machine wordt nu weergegeven. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub en Jupyterlab

Op de Ubuntu DSVM wordt [JupyterHub](https://github.com/jupyterhub/jupyterhub) uitgevoerd, een Jupyter-server voor meerdere gebruikers. Voer dan de volgende stappen uit om verbinding te maken:

   1. Noteer het openbare IP-adres van uw virtuele machine door de virtuele machine in de Azure Portal te zoeken en te selecteren.
      ![IP-adres Ubuntu-machine](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

   1. Open op uw lokale computer een webbrowser en ga naar https:\//VM-IP: 8000, waarbij u ' VM-IP ' vervangt door het IP-adres dat u eerder hebt genoteerd.
   1. Waarschijnlijk kunt u de pagina niet rechtstreeks openen in de browser vanwege een certificaatfout. De DSVM is beveiligd met een zelfondertekend certificaat. In de meeste browsers kunt u verdergaan na deze waarschuwing. In veel browsers wordt er gedurende de websessie een visuele waarschuwing over het certificaat getoond.

      >[!NOTE]
      > Als het foutbericht `ERR_EMPTY_RESPONSE` in uw browser wordt weergegeven, controleert u of u toegang hebt tot de computer door expliciet het *HTTPS*-protocol te gebruiken en niet *HTTP* of alleen het webadres. Als u het webadres zonder `https://` in de adresregel typt, gaan de meeste browsers standaard naar `http` en dan ziet u deze fout.

   1. Voer de gebruikersnaam en het wachtwoord in die u hebt gebruikt om de virtuele machine te maken en meld u aan. 

      ![Aanmeldingsgegevens Jupyter invoeren](./media/dsvm-ubuntu-intro/jupyter-login.png)

      >[!NOTE]
      > Als er in deze fase een 500-fout wordt weergegeven, hebt u waarschijnlijk hoofdletters gebruikt in uw gebruikersnaam. Dit is een bekende interactie tussen Jupyter Hub en de PAMAuthenticator die wordt gebruikt. Als u de fout 'Kan deze pagina niet bereiken' ontvangt, moeten uw machtigingen voor de netwerkbeveiligingsgroep waarschijnlijk worden aangepast. Zoek in Azure Portal de netwerkbeveiligingsgroep binnen de resourcegroep. Als u via openbaar internet toegang wilt tot JupyterHub, moet poort 8000 open zijn. (In de afbeelding ziet u dat deze VM is geconfigureerd voor Just-in-Time-toegang. Dit wordt zeer aanbevolen. Zie [Beheerpoorten beveiligen met Just-in-Time-toegang](../../security-center/security-center-just-in-time.md).) ![Configuratie van netwerkbeveiligingsgroep](./media/dsvm-ubuntu-intro/nsg-permissions.png)

   1. Blader door de vele beschikbare voorbeeldnotitieblokken.

Jupyterlab, de volgende generatie Jupyter-notebooks en JupyterHub, is ook beschikbaar. Meld u aan bij JupyterHub en blader vervolgens naar de URL https:\//VM-IP: 8000/gebruiker/uw-gebruikersnaam/Lab, waarbij u ‘uw-gebruikersnaam’ vervangt door de gebruikersnaam die u hebt gekozen bij het configureren van de virtuele machine. Ook nu wordt de toegang tot de site mogelijk geblokkeerd vanwege een certificaatfout.

U kunt Jupyterlab instellen als de standaard notebookserver door deze regel toe te voegen aan `/etc/jupyterhub/jupyterhub_config.py`:

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Volgende stappen

U kunt nu verder leren en verkennen:

* Zie [Datatechnologie op de Linux Data Science Virtual Machine](linux-dsvm-walkthrough.md) voor een overzicht waarin wordt getoond hoe u enkele algemene data-sciencetaken uitvoert met de Linux DSVM die hier is ingericht. 
* U kunt de verschillende hulpprogramma’s voor data science op de DSVM verkennen door de hulpprogramma’s uit te proberen die in dit artikel worden beschreven. U kunt ook `dsvm-more-info` uitvoeren in de shell in de virtuele machine voor een basisintroductie en verwijzingen naar meer informatie over de hulpprogramma’s die op de virtuele machine zijn geïnstalleerd.  
* Meer informatie over het systematisch bouwen van analytische oplossingen met het [Team Data Science Process](../team-data-science-process/index.yml).
* Ga naar de [Azure AI Gallery](https://gallery.azure.ai/) voor voorbeelden van machine learning en gegevensanalyse die gebruikmaken van de Azure AI-services.
* Raadpleeg de bijbehorende [referentiedocumentatie](./reference-ubuntu-vm.md) voor deze virtuele machine.