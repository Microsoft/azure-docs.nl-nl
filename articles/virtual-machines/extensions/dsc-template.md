---
title: Extensie voor desired state Configuration met Azure Resource Manager sjablonen
description: Meer informatie over de Resource Manager-sjabloon definitie voor de uitbrei ding desired state Configuration (DSC) in Azure.
services: virtual-machines-windows
author: bobbytreed
manager: carmonm
tags: azure-resource-manager
keywords: dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/05/2018
ms.author: robreed
ms.openlocfilehash: 8b862238c0c04fae72659d644dbaf882d00cca19
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735687"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Extensie voor desired state Configuration met Azure Resource Manager sjablonen

In dit artikel wordt de Azure Resource Manager sjabloon voor de [extensie-handler voor desired state Configuration (DSC)](dsc-overview.md)beschreven. Veel van de voor beelden gebruiken **RegistrationURL** (gegeven als een teken reeks) en **RegistrationKey** (als een [PSCredential](/dotnet/api/system.management.automation.pscredential) voor onboarding met Azure Automation. Zie voor meer informatie over het verkrijgen van deze waarden [onboarding machines voor beheer door Azure Automation status configuratie-beveiligde registratie](../../automation/automation-dsc-onboarding.md#enable-machines-securely-using-registration).

> [!NOTE]
> Er kunnen enigszins verschillende schema voorbeelden optreden. De wijziging in het schema is opgetreden in de release van oktober 2016. Zie [Update van een vorige indeling](#update-from-a-previous-format)voor meer informatie.

## <a name="template-example-for-a-windows-vm"></a>Voor beeld van een sjabloon voor een Windows-VM

Het volgende code fragment komt voor in de sectie **resource** van de sjabloon.
De DSC-extensie neemt de eigenschappen van de standaard extensie over.
Zie [VirtualMachineExtension class](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension)voor meer informatie.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-06-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "protectedSettings": {
      "Items": {
        "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
      }
    },
    "settings": {
      "Properties": [
        {
          "Name": "RegistrationKey",
          "Value": {
            "UserName": "PLACEHOLDER_DONOTUSE",
            "Password": "PrivateSettingsRef:registrationKeyPrivate"
          },
          "TypeName": "System.Management.Automation.PSCredential"
        },
        {
          "Name": "RegistrationUrl",
          "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
          "TypeName": "System.String"
        },
        {
          "Name": "NodeConfigurationName",
          "Value": "[parameters('nodeConfigurationName')]",
          "TypeName": "System.String"
        }
      ]
    }
  }
}
```

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Voor beeld van een sjabloon voor virtuele-machine schaal sets voor Windows

Een knoop punt van een virtuele-machine schaalset heeft een **Eigenschappen** sectie met een kenmerk **VirtualMachineProfile, extensionProfile** .
Voeg onder **uitbrei dingen** de gegevens voor de DSC-extensie toe.

De DSC-extensie neemt de eigenschappen van de standaard extensie over.
Zie [VirtualMachineScaleSetExtension class](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension)voor meer informatie.

```json
"extensionProfile": {
    "extensions": [
      {
        "name": "Microsoft.Powershell.DSC",
        "properties": {
          "publisher": "Microsoft.Powershell",
          "type": "DSC",
          "typeHandlerVersion": "2.77",
          "autoUpgradeMinorVersion": true,
          "protectedSettings": {
            "Items": {
              "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
            }
          },
          "settings": {
            "Properties": [
              {
                "Name": "RegistrationKey",
                "Value": {
                  "UserName": "PLACEHOLDER_DONOTUSE",
                  "Password": "PrivateSettingsRef:registrationKeyPrivate"
                },
                "TypeName": "System.Management.Automation.PSCredential"
              },
              {
                "Name": "RegistrationUrl",
                "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
                "TypeName": "System.String"
              },
              {
                "Name": "NodeConfigurationName",
                "Value": "[parameters('nodeConfigurationName')]",
                "TypeName": "System.String"
              }
            ]
          }
        }
      }
    ]
  }
```

## <a name="detailed-settings-information"></a>Gedetailleerde instellingen informatie

Gebruik het volgende schema in de sectie **instellingen** van de Azure DSC-uitbrei ding in een resource manager-sjabloon.

Zie het [standaard configuratie script](#default-configuration-script)voor een lijst met de argumenten die beschikbaar zijn voor het standaard configuratie script.

```json
"settings": {
    "wmfVersion": "latest",
    "configuration": {
        "url": "http://validURLToConfigLocation",
        "script": "ConfigurationScript.ps1",
        "function": "ConfigurationFunction"
    },
    "configurationArguments": {
        "argument1": "Value1",
        "argument2": "Value2"
    },
    "configurationData": {
        "url": "https://foo.psd1"
    },
    "privacy": {
        "dataCollection": "enable"
    },
    "advancedOptions": {
        "downloadMappings": {
            "customWmfLocation": "http://myWMFlocation"
        }
    }
},
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
            "userName": "UsernameValue1",
            "password": "PasswordValue1"
        },
        "parameterOfTypePSCredential2": {
            "userName": "UsernameValue2",
            "password": "PasswordValue2"
        }
    },
    "configurationUrlSasToken": "?g!bber1sht0k3n",
    "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}
```

## <a name="details"></a>Details

| Naam van eigenschap | Type | Beschrijving |
| --- | --- | --- |
| Settings. wmfVersion |tekenreeks |Hiermee geeft u de versie van Windows Management Framework (WMF) op die op uw virtuele machine moet worden geïnstalleerd. Als u deze eigenschap instelt op **laatst** , wordt de meest recente versie van WMF geïnstalleerd. Momenteel zijn de enige mogelijke waarden voor deze eigenschap **4,0**, **5,0**, **5,1** en **meest recent**. Deze mogelijke waarden zijn onderhevig aan updates. De standaard waarde is **meest recent**. |
| settings.configuratie. URL |tekenreeks |Hiermee geeft u de URL-locatie van waaruit u uw DSC-configuratie. zip-bestand wilt downloaden. Als voor de opgegeven URL een SAS-token voor toegang vereist is, stelt u de **protectedSettings.configeigenschap urationUrlSasToken** in op de waarde van uw SAS-token. Deze eigenschap is vereist als **settings.configuratie. script** of **settings.configuratie. functie** zijn gedefinieerd. Als er geen waarde wordt opgegeven voor deze eigenschappen, roept de extensie het standaard configuratie script aan om de meta gegevens van de locatie Configuration Manager (LCM) in te stellen, en moeten er argumenten worden opgegeven. |
| settings.configuratie. script |tekenreeks |Hiermee geeft u de bestands naam op van het script dat de definitie van uw DSC-configuratie bevat. Dit script moet zich in de hoofdmap van het zip-bestand bevindt dat is gedownload van de URL die is opgegeven door de eigenschap **settings.configuratie. URL** . Deze eigenschap is vereist als **settings.configuratie. URL** of **settings.configuratie. script** zijn gedefinieerd. Als er geen waarde wordt opgegeven voor deze eigenschappen, wordt het standaard configuratie script aangeroepen om de ICM-meta gegevens in te stellen, en moeten er argumenten worden opgegeven. |
| settings.configuratie. function |tekenreeks |Hiermee geeft u de naam van uw DSC-configuratie op. De configuratie met de naam moet worden opgenomen in het script dat **settings.configuratie. script** definieert. Deze eigenschap is vereist als **settings.configuratie. URL** of **settings.configuratie. function** zijn gedefinieerd. Als er geen waarde wordt opgegeven voor deze eigenschappen, wordt het standaard configuratie script aangeroepen om de ICM-meta gegevens in te stellen, en moeten er argumenten worden opgegeven. |
| settings.configurationArguments |Verzameling |Hiermee definieert u de para meters die u wilt door geven aan uw DSC-configuratie. Deze eigenschap is niet versleuteld. |
| settings.configurationData. URL |tekenreeks |Hiermee geeft u de URL op waaruit uw psd1-bestand (configuratie gegevens) moet worden gedownload om te worden gebruikt als invoer voor uw DSC-configuratie. Als voor de opgegeven URL een SAS-token voor toegang vereist is, stelt u de **protectedSettings.configeigenschap urationDataUrlSasToken** in op de waarde van uw SAS-token. |
| Settings. privacy. dataCollection |tekenreeks |Hiermee wordt de verzameling telemetrie in-of uitgeschakeld. De enige mogelijke waarden voor deze eigenschap zijn **Enable**, **Disable**, **' '** of **$Null**. Als u deze eigenschap leeg of null laat, kan telemetrie worden ingeschakeld. De standaard waarde is **.** Zie [Azure DSC extension-gegevens verzameling](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/)voor meer informatie. |
| Settings. advancedOptions. downloadMappings |Verzameling |Definieert alternatieve locaties van waaruit WMF kan worden gedownload. Zie [Azure DSC-extensie 2,8 en down loads van de extensie afhankelijkheden toewijzen aan uw eigen locatie](https://devblogs.microsoft.com/powershell/azure-dsc-extension-2-8-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location/)voor meer informatie. |
| protectedSettings.configurationArguments |Verzameling |Hiermee definieert u de para meters die u wilt door geven aan uw DSC-configuratie. Deze eigenschap is versleuteld. |
| protectedSettings.configurationUrlSasToken |tekenreeks |Hiermee geeft u het SAS-token op dat moet worden gebruikt om toegang te krijgen tot de URL die **settings.configuratie. URL** definieert. Deze eigenschap is versleuteld. |
| protectedSettings.configurationDataUrlSasToken |tekenreeks |Hiermee geeft u het SAS-token op dat moet worden gebruikt om toegang te krijgen tot de URL die  **settings.configurationData. URL** definieert. Deze eigenschap is versleuteld. |

## <a name="default-configuration-script"></a>Standaard configuratie script

Zie [Local Configuration Manager Basic Settings](/powershell/scripting/dsc/managing-nodes/metaConfig#basic-settings)(Engelstalig) voor meer informatie over de volgende waarden.
U kunt het standaard configuratie script van de DSC-extensie gebruiken om alleen de ICM-eigenschappen te configureren die in de volgende tabel worden weer gegeven.

| Naam van eigenschap | Type | Beschrijving |
| --- | --- | --- |
| protectedSettings.configurationArguments. RegistrationKey |PSCredential |Eigenschap Required. Hiermee geeft u de sleutel op die wordt gebruikt voor een knoop punt om te registreren bij de Azure Automation-Service als het wacht woord van een Power shell-referentie object. Deze waarde kan automatisch worden gedetecteerd met behulp van de **listkeys ophalen** -methode voor het Automation-account.  Zie het [voor beeld](#example-using-referenced-azure-automation-registration-values). |
| settings.configurationArguments. RegistrationUrl |tekenreeks |Eigenschap Required. Hiermee geeft u de URL op van het Automation-eind punt waar het knoop punt zich probeert te registreren. Deze waarde kan automatisch worden gedetecteerd met behulp van de **referentie** methode voor het Automation-account. |
| settings.configurationArguments. NodeConfigurationName |tekenreeks |Eigenschap Required. Hiermee geeft u de knooppunt configuratie in het Automation-account moet worden toegewezen aan het knoop punt. |
| settings.configurationArguments.ConfigurationMode |tekenreeks |Hiermee geeft u de modus voor LCM op. Geldige opties zijn **ApplyOnly**, **ApplyandMonitor** en **ApplyandAutoCorrect**.  De standaard waarde is **ApplyandMonitor**. |
| settings.configurationArguments. RefreshFrequencyMins | uint32 | Hiermee geeft u op hoe vaak LCM met het Automation-account probeert te controleren op updates.  De standaard waarde is **30**.  De minimum waarde is **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | uint32 | Hiermee geeft u op hoe vaak LCM de huidige configuratie valideert. De standaard waarde is **15**. De minimum waarde is **15**. |
| settings.configurationArguments. RebootNodeIfNeeded | booleaans | Hiermee geeft u op of een knoop punt automatisch opnieuw kan worden opgestart als een DSC-bewerking dit aanvraagt. De standaard waarde is **False**. |
| settings.configurationArguments. ActionAfterReboot | tekenreeks | Hiermee geeft u op wat er gebeurt na het opnieuw opstarten bij het Toep assen van een configuratie. Geldige opties zijn **ContinueConfiguration** en **de stopconfiguration**. De standaard waarde is **ContinueConfiguration**. |
| settings.configurationArguments. AllowModuleOverwrite | booleaans | Hiermee geeft u op of de LCM bestaande modules op het knoop punt overschrijft. De standaard waarde is **False**. |

## <a name="settings-vs-protectedsettings"></a>instellingen versus protectedSettings

Alle instellingen worden opgeslagen in een tekst bestand met instellingen op de virtuele machine.
Eigenschappen die worden weer gegeven onder **instellingen** zijn open bare eigenschappen.
Open bare eigenschappen worden niet versleuteld in het tekst bestand met instellingen.
Eigenschappen die worden weer gegeven onder **protectedSettings** zijn versleuteld met een certificaat en worden niet weer gegeven als tekst zonder opmaak in het instellingen bestand op de virtuele machine.

Als de configuratie referenties vereist, kunt u de referenties in **protectedSettings** insluiten:

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
               "userName": "UsernameValue1",
               "password": "PasswordValue1"
        }
    }
}
```

## <a name="example-configuration-script"></a>Voorbeeld configuratie script

In het volgende voor beeld wordt het standaard gedrag voor de DSC-uitbrei ding weer gegeven, dat wil zeggen dat meta gegevens worden ingesteld op LCM en kunnen worden geregistreerd bij de Automation DSC-service.
Configuratie argumenten zijn vereist.
Configuratie argumenten worden door gegeven aan het standaard configuratie script om de ICM-meta gegevens in te stellen.

```json
"settings": {
    "configurationArguments": {
        "RegistrationUrl" : "[parameters('registrationUrl1')]",
        "NodeConfigurationName" : "nodeConfigurationNameValue1"
    }
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "registrationKey"
        }
    }
}
```

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Voor beeld met behulp van het configuratie script in Azure Storage

Het volgende voor beeld is afkomstig uit het overzicht van de [DSC-extensie-handler](dsc-overview.md).
In dit voor beeld worden Resource Manager-sjablonen gebruikt in plaats van cmdlets voor het implementeren van de uitbrei ding.
Sla de IisInstall.ps1 configuratie op en plaats deze in een zip-bestand (bijvoorbeeld: `iisinstall.zip` ) en upload het bestand vervolgens naar een toegankelijke URL.
In dit voor beeld wordt Azure Blob-opslag gebruikt, maar u kunt. zip-bestanden downloaden vanaf een wille keurige locatie.

In de Resource Manager-sjabloon geeft de volgende code de virtuele machine de opdracht om het juiste bestand te downloaden en voert u vervolgens de juiste Power shell-functie uit:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/iisinstall.zip",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="example-using-referenced-azure-automation-registration-values"></a>Voor beeld van het gebruik van Azure Automation registratie waarden waarnaar wordt verwezen

In het volgende voor beeld worden de **RegistrationUrl** en **RegistrationKey** opgehaald door te verwijzen naar de Azure Automation-account eigenschappen en de methode  **Listkeys ophalen** te gebruiken om de primaire sleutel op te halen (0).  In dit voor beeld zijn de para meters **automationAccountName** en **NodeConfigName** aan de sjabloon door gegeven.

```json
"settings": {
    "RegistrationUrl" : "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
    "NodeConfigurationName" : "[parameters('NodeConfigName')]"
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-01-15').Keys[0].value]"
        }
    }
}
```

## <a name="update-from-a-previous-format"></a>Bijwerken vanuit een vorige indeling

Instellingen in een vorige indeling van de uitbrei ding (en die de open bare eigenschappen **ModulesUrl**, **ModuleSource**, **ModuleVersion**, **ConfigurationFunction**, **SasToken** of **Properties** hebben), worden automatisch aangepast aan de huidige indeling van de uitbrei ding.
Ze worden net zo uitgevoerd als voorheen.

In het volgende schema ziet u hoe het vorige schema voor instellingen eruitziet:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties": {
        "ParameterToConfigurationFunction1": "Value1",
        "ParameterToConfigurationFunction2": "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1"
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": {
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Dit is de manier waarop de vorige notatie wordt aangepast aan de huidige indeling:

| Huidige eigenschaps naam | Vorige schema-equivalent |
| --- | --- |
| Settings. wmfVersion |Instellingen. WMFVersion |
| settings.configuratie. URL |Instellingen. ModulesUrl |
| settings.configuratie. script |Eerste deel van settings.ConfigurationFunction (vóór \\ \\ ) |
| settings.configuratie. function |Tweede deel van settings.ConfigurationFunction (na \\ \\ ) |
| settings.configuration.module.name | Instellingen. ModuleSource |
| settings.configuratie. module. version | Instellingen. ModuleVersion |
| settings.configurationArguments |Instellingen. Eigenschappen |
| settings.configurationData. URL |protectedSettings. DataBlobUri (zonder SAS-token) |
| Settings. privacy. dataCollection |Instellingen. Privacy. dataCollection |
| Settings. advancedOptions. downloadMappings |Instellingen. AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments |protectedSettings. Properties |
| protectedSettings.configurationUrlSasToken |Instellingen. SasToken |
| protectedSettings.configurationDataUrlSasToken |SAS-token van protectedSettings. DataBlobUri |

## <a name="troubleshooting"></a>Problemen oplossen

Hier volgen enkele van de fouten die u kunt uitvoeren in en hoe u deze herstelt.

### <a name="invalid-values"></a>Ongeldige waarden

Privacy. dataCollection is {0} .
De enige mogelijke waarden zijn ' ', ' inschakelen ' en ' uitschakelen ' '.
' WmfVersion is ' {0} '.
Alleen mogelijke waarden zijn... en ' meest recent '.

**Probleem**: een gegeven waarde is niet toegestaan.

**Oplossing**: Wijzig de ongeldige waarde in een geldige waarde.
Zie de tabel in [Details](#details)voor meer informatie.

### <a name="invalid-url"></a>Ongeldige URL

"ConfigurationData. URL is {0} . Dit is geen geldige URL: ' DataBlobUri is ' {0} '. Dit is geen geldige URL "" configuratie. de URL is {0} . Dit is geen geldige URL '

**Probleem**: een gegeven URL is niet geldig.

**Oplossing**: Controleer alle door u aangelegde url's.
Zorg ervoor dat alle Url's worden omgezet naar geldige locaties waartoe de extensie toegang heeft op de externe computer.

### <a name="invalid-registrationkey-type"></a>Ongeldig RegistrationKey-type

"Ongeldig type voor para meter RegistrationKey van het type PSCredential."

**Probleem**: de waarde *RegistrationKey* in protectedSettings.configurationArguments kan niet worden gegeven als een ander type dan een PSCredential.

**Oplossing**: wijzig uw protectedSettings.configurationArguments-vermelding voor RegistrationKey met een PSCredential-type in de volgende indeling:

```json
"configurationArguments": {
    "RegistrationKey": {
        "userName": "NOT_USED",
        "Password": "RegistrationKey"
    }
}
```

### <a name="invalid-configurationargument-type"></a>Ongeldig ConfigurationArgument-type

"Ongeldig configurationArguments-type {0} "

**Probleem**: de eigenschap *ConfigurationArguments* kan niet worden omgezet in een object met een **hash-tabel** .

**Oplossing**: Maak de *ConfigurationArguments* -eigenschap een **hash-tabel**.
Volg de indeling die wordt vermeld in de voor gaande voor beelden. Bekijk de aanhalings tekens, komma's en accolades.

### <a name="duplicate-configurationarguments"></a>Dubbele ConfigurationArguments

' Dubbele argumenten gevonden ' {0} ' in zowel open bare als beveiligde configurationArguments '

**Probleem**: de *ConfigurationArguments* in open bare instellingen en de *ConfigurationArguments* in beveiligde instellingen hebben eigenschappen met dezelfde naam.

**Oplossing**: Verwijder een van de dubbele eigenschappen.

### <a name="missing-properties"></a>Ontbrekende eigenschappen

De settings.Configuratie. function vereist dat settings.configuratie. URL of settings.configuratie. module is opgegeven.

settings.Configuratie. URL vereist dat settings.configuratie. script is opgegeven.

Voor settings.Configuratie. script moet settings.configuratie. URL zijn opgegeven.

Voor de settings.Configuratie. URL moet settings.configuratie. function zijn opgegeven.

Voor de protectedSettings.ConfigurationUrlSasToken moet settings.configuratie. URL zijn opgegeven.

Voor de protectedSettings.ConfigurationDataUrlSasToken moet settings.configurationData. URL zijn opgegeven.

**Probleem**: een gedefinieerde eigenschap heeft een andere eigenschap nodig, die ontbreekt.

**Oplossingen**:

- Geef de ontbrekende eigenschap op.
- Verwijder de eigenschap waarvoor de ontbrekende eigenschap is vereist.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het gebruik van schaal sets voor virtuele machines met de Azure DSC-extensie](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
- Meer informatie over [het beheer van beveiligde referenties van DSC](dsc-credentials.md).
- Krijg een [Inleiding tot de Azure DSC-extensie-handler](dsc-overview.md).
- Voor meer informatie over Power shell DSC gaat u naar het [Power shell-documentatie centrum](/powershell/scripting/dsc/overview/overview).
