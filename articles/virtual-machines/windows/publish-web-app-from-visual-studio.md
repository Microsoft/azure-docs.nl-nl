---
title: Een web-app publiceren naar een Azure VM vanuit Visual Studio
description: Een ASP.NET-webtoepassing publiceren naar een virtuele machine van Azure vanuit Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: 7cf2474c9ca005d85cea28f2b8fa1e23836b191f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487555"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Een ASP.NET-Web-app publiceren naar een Azure VM vanuit Visual Studio

In dit document wordt beschreven hoe u een ASP.NET-webtoepassing publiceert naar een virtuele Azure-machine (VM) met behulp van de functie voor het publiceren van **Microsoft Azure virtual machines** in Visual Studio 2019.  

## <a name="prerequisites"></a>Vereisten
Als u Visual Studio wilt gebruiken om een ASP.NET-project te publiceren op een virtuele machine van Azure, moet de virtuele machine correct zijn ingesteld.

- De machine moet worden geconfigureerd voor het uitvoeren van een ASP.NET-webtoepassing en moet webdeploy hebben geïnstalleerd. Zie [een ASP.net-VM maken met Webdeploy](https://github.com/aspnet/Tooling/blob/AspNetVMs/docs/create-asp-net-vm-with-webdeploy.md)voor meer informatie.

- Voor de virtuele machine moet een DNS-naam zijn geconfigureerd. Zie [een Fully Qualified Domain name maken in de Azure portal voor een Windows-VM](../create-fqdn.md)voor meer informatie.

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Uw ASP.NET-Web-app publiceren naar de Azure-VM met behulp van Visual Studio
In de volgende sectie wordt beschreven hoe u een bestaande ASP.NET-webtoepassing publiceert naar een virtuele machine van Azure.

1. Open uw web-app-oplossing in Visual Studio 2019.
2. Klik met de rechter muisknop op het project in Solution Explorer en kies **publiceren...**
3. Gebruik de pijl aan de rechter kant van de pagina om door de publicatie opties te schuiven totdat u **Microsoft Azure virtual machines** hebt gevonden.  

   ![Pagina publiceren-pijl-rechts]

4. Selecteer het **Microsoft Azure virtual machines** pictogram en selecteer **publiceren**.

   ![Het pictogram pagina publiceren-Microsoft Azure virtuele machine]

5. Kies het juiste account (met een Azure-abonnement dat is verbonden met uw virtuele machine).  
   - Als u bent aangemeld bij Visual Studio, wordt de lijst met accounts gevuld met al uw geverifieerde accounts.  
   - Als u niet bent aangemeld of als het account dat u nodig hebt niet wordt weer gegeven, kiest u een account toevoegen... en volg de aanwijzingen om u aan te melden.  
   ![Azure-account kiezer]  

6. Selecteer de juiste VM in de lijst met bestaande Virtual Machines.

   > [!Note]
   > Het invullen van deze lijst kan enige tijd duren.

   ![VM-selector van Azure]

7. Klik op OK om te beginnen met publiceren.

8. Wanneer u om referenties wordt gevraagd, geeft u de gebruikers naam en het wacht woord op van een gebruikers account op de doel-VM die is geconfigureerd met publicatie rechten. Deze referenties zijn doorgaans de gebruikers naam en het wacht woord voor beheerders die worden gebruikt bij het maken van de virtuele machine.  

   ![Aanmelding bij webdeploy]

9. Accepteer het beveiligings certificaat.

   ![Certificaat fout]

10. Bekijk het uitvoer venster om de voortgang van de publicatie bewerking te controleren.

    ![Uitvoervenster]

11. Als het publiceren is gelukt, wordt een browser gestart om de URL van de zojuist gepubliceerde site te openen.

**Gelukt!**

U hebt nu uw web-app gepubliceerd naar een virtuele machine van Azure.

## <a name="publish-page-options"></a>Pagina opties publiceren

Nadat de wizard Publiceren is voltooid, wordt de pagina publiceren in het document goed geopend met het nieuwe publicatie profiel geselecteerd.

### <a name="re-publish"></a>Opnieuw publiceren

Als u updates voor uw webtoepassing wilt publiceren, selecteert u de knop **publiceren** op de pagina publiceren.  
- Voer de gebruikers naam en het wacht woord in als u hierom wordt gevraagd.  
- Publiceren begint onmiddellijk.

![Pagina publiceren-knop publiceren]

### <a name="modify-publish-profile-settings"></a>Instellingen van het publicatie profiel wijzigen

Als u de instellingen voor het publicatie profiel wilt weer geven en wijzigen, selecteert u **instellingen...**.  

![Pagina publiceren-instellingen knop]

De instellingen moeten er ongeveer als volgt uitzien:  

![Publicatie-instellingen-pagina verbinding]

#### <a name="save-user-name-and-password"></a>Gebruikers naam en wacht woord opslaan
- Vermijd elke keer dat u een verificatie gegevens opgeeft. Als u dit wilt doen, vult u de velden **gebruikers naam** en **wacht woord** in en selecteert u het vak **wacht woord opslaan** .
- Gebruik de knop **verbinding valideren** om te bevestigen dat u de juiste gegevens hebt ingevoerd.

#### <a name="deploy-to-clean-web-server"></a>Implementeren voor schone webserver

- Als u ervoor wilt zorgen dat de webserver een schone kopie van de webtoepassing heeft na elke upload en dat er geen andere bestanden van een vorige implementatie zijn, schakelt u het selectie vakje **extra bestanden in doel verwijderen** in op het tabblad **instellingen** .

- Waarschuwing: als u met deze instelling publiceert, worden alle bestanden verwijderd die bestaan op de webserver (wwwroot Directory). Zorg ervoor dat u de status van de computer kent voordat u deze optie inschakelt. 

![Publicatie-instellingen-pagina instellingen]

## <a name="next-steps"></a>Volgende stappen

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>CI/CD instellen voor automatische implementatie naar Azure VM

Zie [implementeren op een virtuele Windows-machine](/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups)om een continue leverings pijplijn met Azure-pijp lijnen in te stellen.

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Pagina publiceren-pijl-rechts]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Het pictogram pagina publiceren-Microsoft Azure virtuele machine]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Azure-account kiezer]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[VM-selector van Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[Aanmelding bij webdeploy]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Certificaat fout]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Uitvoervenster]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Pagina publiceren-knop publiceren]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Pagina publiceren-instellingen knop]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Publicatie-instellingen-pagina verbinding]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Publicatie-instellingen-pagina instellingen]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png