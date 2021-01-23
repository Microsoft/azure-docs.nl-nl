---
title: Gebruik Visual Studio om Extern bureaublad in te scha kelen voor een rol (Azure Cloud Services Classic)
description: Uw Azure Cloud service-toepassing configureren om extern bureau blad-verbindingen toe te staan
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ad95cefbdf839c28b0979b051e217a1dfec76eea
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743233"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic-using-visual-studio"></a>Verbinding met extern bureaublad inschakelen voor een rol in azure Cloud Services (klassiek) met behulp van Visual Studio

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

> [!div class="op_single_selector"]
> * [Azure-portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Met Extern bureaublad kunt u toegang krijgen tot het bureau blad van een rol die wordt uitgevoerd in Azure. U kunt een Extern bureaublad verbinding gebruiken om problemen met uw toepassing op te lossen en te onderzoeken terwijl deze wordt uitgevoerd.

De wizard publiceren die Visual Studio voor Cloud Services biedt, bevat een optie om Extern bureaublad in te scha kelen tijdens het publicatie proces, met behulp van de referenties die u opgeeft. Het gebruik van deze optie is geschikt voor het gebruik van Visual Studio 2017 versie 15,4 en eerdere versies.

Met Visual Studio 2017 versie 15,5 en hoger is het echter raadzaam om Extern bureaublad in te scha kelen via de wizard Publiceren tenzij u alleen als één ontwikkelaar werkt. Voor elke situatie waarin het project door andere ontwikkel aars kan worden geopend, kunt u in plaats daarvan Extern bureaublad via de Azure Portal, via Power shell of vanuit een release pijplijn in een doorlopende implementatie werk stroom. Deze aanbeveling wordt veroorzaakt door een wijziging in de manier waarop Visual Studio communiceert met Extern bureaublad op de Cloud service-VM, zoals wordt beschreven in dit artikel.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Extern bureaublad configureren via Visual Studio 2017 versie 15,4 en lager

Wanneer u Visual Studio 2017 versie 15,4 en eerder gebruikt, kunt u de optie **extern bureaublad voor alle rollen inschakelen** gebruiken in de wizard Publiceren. U kunt de wizard nog steeds met Visual Studio 2017 versie 15,5 en hoger gebruiken, maar de optie Extern bureaublad niet gebruiken.

1. Start in Visual Studio de wizard publiceren door met de rechter muisknop te klikken op het Cloud service project in Solution Explorer en vervolgens **publiceren** te kiezen.

2. Meld u indien nodig aan bij uw Azure-abonnement en selecteer **volgende**.

3. Selecteer op de pagina **instellingen** de optie **extern bureaublad inschakelen voor alle rollen** en selecteer vervolgens de koppeling **instellingen...** om het dialoog venster **extern bureaublad configuratie** te openen.

4. Selecteer onder in het dialoog venster **meer opties**. Met deze opdracht wordt een vervolg keuzelijst weer gegeven waarin u een certificaat maakt of kiest, zodat u referentie gegevens kunt versleutelen wanneer u verbinding maakt via extern bureau blad.

   > [!Note]
   > De certificaten die u nodig hebt voor een verbinding met een extern bureau blad, wijken af van de certificaten die u voor andere Azure-bewerkingen gebruikt. Het RAS-certificaat moet een persoonlijke sleutel hebben.

5. Selecteer een certificaat in de lijst of kies **&lt; maken... &gt;**. Als u een nieuw certificaat maakt, geeft u een beschrijvende naam op voor het nieuwe certificaat wanneer u hierom wordt gevraagd en selecteert u **OK**. Het nieuwe certificaat wordt weer gegeven in de vervolg keuzelijst.

6. Geef een gebruikers naam en wacht woord op. U kunt geen bestaand account gebruiken. Gebruik ' Administrator ' niet als de gebruikers naam voor het nieuwe account.

7. Kies een datum waarop het account verloopt en waarna Extern bureaublad verbindingen worden geblokkeerd.

8. Nadat u alle vereiste gegevens hebt opgegeven, selecteert u **OK**. Visual Studio voegt de Extern bureaublad-instellingen toe aan uw project `.cscfg` en `.csdef` bestanden, met inbegrip van het wacht woord dat is versleuteld met behulp van het gekozen certificaat.

9. Voltooi alle resterende stappen met behulp van de **volgende** knop en selecteer vervolgens **publiceren** wanneer u klaar bent om uw Cloud service te publiceren. Als u nog niet klaar bent om te publiceren, selecteert u **Annuleren** en antwoordt u op **Ja** wanneer u wordt gevraagd wijzigingen op te slaan. U kunt uw Cloud service later met deze instellingen publiceren.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Extern bureaublad configureren bij gebruik van Visual Studio 2017 versie 15,5 en hoger

Met Visual Studio 2017 versie 15,5 en hoger kunt u nog steeds de wizard Publiceren gebruiken met een Cloud service-project. U kunt ook de optie **extern bureaublad voor alle rollen inschakelen** gebruiken als u alleen als één ontwikkelaar werkt.

Als u als onderdeel van een team werkt, moet u in plaats daarvan extern bureau blad inschakelen op de Azure-Cloud service met behulp van de [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md) of [Power shell](cloud-services-role-enable-remote-desktop-powershell.md).

Deze aanbeveling wordt veroorzaakt door een wijziging in de manier waarop Visual Studio 2017 versie 15,5 en hoger communiceert met de Cloud service-VM. Wanneer u Extern bureaublad via de wizard Publiceren inschakelt, communiceren eerdere versies van Visual Studio met de virtuele machine via de zogenaamde RDP-invoeg toepassing. Visual Studio 2017 versie 15,5 en hoger communiceert in plaats daarvan met behulp van de ' RDP-extensie ' die veiliger en flexibeler is. Deze wijziging wordt ook uitgelijnd met het feit dat de Azure Portal-en Power shell-methoden voor het inschakelen van Extern bureaublad ook de RDP-uitbrei ding gebruiken.

Wanneer Visual Studio communiceert met de RDP-uitbrei ding, verzendt het een wacht woord voor onbewerkte tekst via TLS. De configuratie bestanden van het project slaan echter alleen een versleuteld wacht woord op, dat alleen kan worden ontsleuteld als tekst zonder opmaak met het lokale certificaat dat oorspronkelijk werd gebruikt om het te versleutelen.

Als u het Cloud service project op dezelfde ontwikkel computer implementeert, is het lokale certificaat beschikbaar. In dit geval kunt u nog steeds de optie **extern bureaublad voor alle rollen inschakelen** gebruiken in de wizard Publiceren.

Als u of andere ontwikkel aars het Cloud service project van verschillende computers willen implementeren, hebben deze andere computers echter niet het vereiste certificaat om het wacht woord te ontsleutelen. Als gevolg hiervan wordt het volgende fout bericht weer gegeven:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

U kunt het wacht woord wijzigen elke keer dat u de Cloud service implementeert, maar die actie is onhandig voor iedereen die Extern bureaublad moet gebruiken.

Als u het project deelt met een team, is het het beste om de optie in de wizard Publiceren te wissen en direct Extern bureaublad rechtstreeks via de [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md) in te scha kelen of door [Power shell](cloud-services-role-enable-remote-desktop-powershell.md)te gebruiken.

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Implementeren vanaf een build-server met Visual Studio 2017 versie 15,5 en hoger

U kunt een Cloud service project implementeren op basis van een build-server (bijvoorbeeld met Azure DevOps Services) waarop Visual Studio 2017 versie 15,5 of hoger is geïnstalleerd in de build-agent. Met deze indeling wordt de implementatie uitgevoerd vanaf dezelfde computer waarop het versleutelings certificaat beschikbaar is.

Als u de RDP-uitbrei ding van Azure DevOps Services wilt gebruiken, neemt u de volgende gegevens op in uw build-pijp lijn:

1. Voeg `/p:ForceRDPExtensionOverPlugin=true` in uw MSBuild-argumenten toe om ervoor te zorgen dat de implementatie werkt met de RDP-extensie in plaats van de RDP-invoeg toepassing. Bijvoorbeeld:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. Nadat u de stappen hebt gebouwd, voegt u de **Implementatie stap Azure Cloud service** toe en stelt u de eigenschappen in.

1. Voeg na de implementatie stap een **Azure Power shell** -stap toe, stel de eigenschap **weergave naam** in op ' Azure-implementatie: RDP-extensie inschakelen ' (of een andere geschikte naam) en selecteer het juiste Azure-abonnement.

1. Stel het **script type** in op inline en plak de onderstaande code in het veld **inline-script** . (U kunt ook een `.ps1` bestand in uw project maken met dit script, het **script type** instellen op het pad naar het script bestand en het **pad** naar het script instellen om naar het bestand te verwijzen.)

    ```ps
    Param(
        [Parameter(Mandatory=$True)]
        [string]$username,

        [Parameter(Mandatory=$True)]
        [string]$password,

        [Parameter(Mandatory=$True)]
        [string]$serviceName,

        [Datetime]$expiry = ($(Get-Date).AddYears(1))
    )

    Write-Host "Service Name: $serviceName"
    Write-Host "User Name: $username"
    Write-Host "Expiry: $expiry"

    $securepassword = ConvertTo-SecureString -String $password -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential $username,$securepassword

    # Try to remote existing RDP Extensions
    try
    {
        $existingRDPExtension = Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
        if ($existingRDPExtension -ne $null)
        {
            Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
        }
    }
    catch
    {
    }

    Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry -Verbose
    ```

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Verbinding maken met een Azure-rol met behulp van Extern bureaublad

Nadat u uw Cloud service op Azure hebt gepubliceerd en Extern bureaublad hebt ingeschakeld, kunt u Visual Studio Server Explorer gebruiken om u aan te melden bij de Cloud service-VM:

1. Vouw in Server Explorer het **Azure** -knoop punt uit en vouw vervolgens het knoop punt voor een Cloud service en een van de rollen uit om een lijst met exemplaren weer te geven.

2. Klik met de rechter muisknop op een instantie knooppunt en selecteer **verbinding maken met behulp van extern bureaublad**.

3. Voer de gebruikers naam en het wacht woord in die u eerder hebt gemaakt. U bent nu aangemeld bij uw externe sessie.

## <a name="additional-resources"></a>Aanvullende bronnen

[Cloud Services configureren](cloud-services-how-to-configure-portal.md)
