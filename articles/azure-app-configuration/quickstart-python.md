---
title: Quickstart voor het gebruiken van Azure App Configuration met Python-apps | Microsoft Docs
description: In deze quickstart maakt u een Python-app met Azure App Configuration om opslag en beheer van toepassingsinstellingen gescheiden van uw code te centraliseren.
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-python
ms.date: 9/17/2020
ms.author: drewbat
ms.openlocfilehash: 1be74ceeb1f4808a8df8f56b71b66aa870cdd91d
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800870"
---
# <a name="quickstart-create-a-python-app-with-azure-app-configuration"></a>Quickstart: Een Python-app maken met Azure App Configuration

In deze quickstart gaat u met Azure App Configuration de opslag en het beheer van toepassingsinstellingen centraliseren met behulp van de [Azure App Configuration-clientbibliotheek voor Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration).

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)
- Python 2,7, of 3,6 of hoger, voor informatie over het instellen van python in Windows raadpleegt u de [documentatie van python in Windows]( https://docs.microsoft.com/windows/python/)

## <a name="create-an-app-configuration-store"></a>Een App Configuration-archief maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecteer **Configuratieverkenner** > **Maken** > **Sleutelwaarde** om de volgende sleutel-waardeparen toe te voegen:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Message | Gegevens van Azure App Configuration |

    Laat **Label** en **Inhoudstype** nog even leeg.

8. Selecteer **Toepassen**.

## <a name="setting-up-the-python-app"></a>De Python-app instellen

1. In deze zelfstudie maakt u een nieuwe map voor het project, met de naam *app-configuration-quickstart*.

    ```console
    mkdir app-configuration-quickstart
    ```

1. Schakel over naar de zojuist gemaakte map *app-configuration-quickstart*.

    ```console
    cd app-configuration-quickstart
    ```

1. Installeer de Azure App Configuration-clientbibliotheek met behulp van de opdracht `pip install`.

    ```console
    pip install azure-appconfiguration
    ```

1. Maak een nieuw bestand met de naam *app-configuration-quickstart.py* in de map *app-configuration-quickstart* en voeg de volgende code toe:

    ```python
    import os
    from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting
    
    try:
        print("Azure App Configuration - Python Quickstart")
        # Quickstart code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

> [!NOTE]
> Met de codefragmenten in deze quickstart kunt u aan de slag gaan met de App Configuration-clientbibliotheek voor Python. Voor uw toepassing moet u ook rekening houden met het afhandelen van uitzonderingen op basis van uw behoeften. Raadpleeg de [Python SDK documentation](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration) voor meer informatie over het afhandelen van uitzonderingen.

## <a name="configure-your-app-configuration-connection-string"></a>Uw App Configuration-verbindingsreeks configureren

1. Stel een omgevingsvariabele in met de naam **AZURE_APP_CONFIG_CONNECTION_STRING** en stel deze in op de toegangssleutel van het App Configuration-archief. Voer op de opdrachtregel de volgende opdracht uit:

    ```cmd
    setx AZURE_APP_CONFIG_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Als u Windows PowerShell gebruikt, voert u de volgende opdracht uit:

    ```azurepowershell
    $Env:AZURE_APP_CONFIG_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    Als u macOS of Linux gebruikt, voert u de volgende opdracht uit:

    ```console
    export AZURE_APP_CONFIG_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

2. Start de opdrachtprompt opnieuw op om de wijziging door te voeren. Druk de waarde van de omgevingsvariabele af om te controleren of deze correct is ingesteld.

## <a name="code-samples"></a>Codevoorbeelden

De voorbeeldcodefragmenten in deze sectie laten zien hoe u veelgebruikte bewerkingen kunt uitvoeren met de App Configuration-clientbibliotheek voor Python. Voeg deze codefragmenten toe aan het blok `try` in het bestand *app-configuration-quickstart.py* dat u eerder hebt gemaakt.

> [!NOTE]
> De App Configuration-clientbibliotheek verwijst naar een sleutel-waardeobject als `ConfigurationSetting`. Daarom worden in dit artikel de **sleutel-waarden** in het App Configuration-archief **configuratie-instellingen** genoemd.

* [Verbinding maken met een App Configuration-archief](#connect-to-an-app-configuration-store)
* [Een configuratie-instelling ophalen](#get-a-configuration-setting)
* [Een configuratie-instelling toevoegen](#add-a-configuration-setting)
* [Een lijst met configuratie-instellingen ophalen](#get-a-list-of-configuration-settings)
* [Een configuratie-instelling vergrendelen](#lock-a-configuration-setting)
* [Een configuratie-instelling ontgrendelen](#unlock-a-configuration-setting)
* [Een configuratie-instelling bijwerken](#update-a-configuration-setting)
* [Een configuratie-instelling verwijderen](#delete-a-configuration-setting)

### <a name="connect-to-an-app-configuration-store"></a>Verbinding maken met een App Configuration-archief

Met het volgende codefragment maakt u een instantie van **AzureAppConfigurationClient** met behulp van de in uw omgevingsvariabelen opgeslagen verbindingsreeks.

```python
    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)
```

### <a name="get-a-configuration-setting"></a>Een configuratie-instelling ophalen

Met het volgende codefragment wordt een configuratie-instelling opgehaald met de `key`-naam.

```python
    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)
```

### <a name="add-a-configuration-setting"></a>Een configuratie-instelling toevoegen

Met het volgende codefragment maakt u een `ConfigurationSetting`-object met de velden `key` en `value` en roept u de methode `add_configuration_setting` aan. Deze methode genereert een uitzondering als u probeert een configuratie-instelling toe te voegen die al in uw archief voorkomt. Als u deze uitzondering wilt voorkomen, kunt u in plaats daarvan de methode [set_configuration_setting](#update-a-configuration-setting) gebruiken.

```python
    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)
```

### <a name="get-a-list-of-configuration-settings"></a>Een lijst met configuratie-instellingen ophalen

Met het volgende codefragment wordt een lijst met configuratie-instellingen opgehaald. De argumenten `key_filter` en `label_filter` kunnen worden opgegeven om sleutel-waarden te filteren op basis van `key` respectievelijk `label`. Zie [configuratie-instellingen opvragen](./concept-key-value.md#query-key-values) voor meer informatie over filteren.

```python
    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)
```

### <a name="lock-a-configuration-setting"></a>Een configuratie-instelling vergrendelen

De vergrendelingsstatus van een sleutel-waarde in App Configuration wordt aangeduid met het kenmerk `read_only` van het object `ConfigurationSetting`. Als `read_only` `True`is, is de instelling vergrendeld. De methode `set_read_only` kan worden aangeroepen met het argument `read_only=True` om de configuratie-instelling te vergrendelen.

```python
    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))
```

### <a name="unlock-a-configuration-setting"></a>Een configuratie-instelling ontgrendelen

Als het `read_only`-kenmerk van een `ConfigurationSetting` de waarde `False` heeft, is de instelling ontgrendeld. De methode `set_read_only` kan worden aangeroepen met het argument `read_only=False` om de configuratie-instelling te ontgrendelen.

```python
    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))
```

### <a name="update-a-configuration-setting"></a>Een configuratie-instelling bijwerken

De methode `set_configuration_setting` kan worden gebruikt om een bestaande instelling bij te werken of een nieuwe instelling te maken. Met het volgende codefragment wordt de waarde van een bestaande configuratie-instelling gewijzigd.

```python
    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)
```

### <a name="delete-a-configuration-setting"></a>Een configuratie-instelling verwijderen

Met het volgende codefragment wordt een configuratie-instelling met de `key`-naam.

```python
    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)
```

## <a name="run-the-app"></a>De app uitvoeren

In deze quickstart hebt u een Python-app gemaakt waarin gebruikgemaakt wordt van de Azure App Configuration-clientbibliotheek om een configuratie-instelling op te halen die is gemaakt via de Azure-portal, een nieuwe instelling toe te voegen, een lijst met bestaande instellingen op te halen, een instelling te vergrendelen en te ontgrendelen, een instelling bij te werken en ten slotte een instelling te verwijderen.

Op dit moment moet uw *app-configuration-quickstart.py*-bestand de volgende code bevatten:

```python
import os
from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting

try:
    print("Azure App Configuration - Python Quickstart")
    # Quickstart code goes here

    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)

    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)

    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)

    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)

    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))

    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))

    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)

    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)

except Exception as ex:
    print('Exception:')
    print(ex)
```

Navigeer in uw consolevenster naar de map die het bestand *app-configuration-quickstart.py* bevat en voer de volgende Python-opdracht uit om de app uit te voeren:

```console
python app-configuration-quickstart.py
```

In dat geval moet de volgende uitvoer worden weergegeven:

```output
Azure App Configuration - Python Quickstart

Retrieved configuration setting:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration

Added configuration setting:
Key: TestApp:Settings:NewSetting, Value: New setting value

Retrieved list of configuration settings:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration
Key: TestApp:Settings:NewSetting, Value: New setting value

Read-only status for TestApp:Settings:NewSetting: True

Read-only status for TestApp:Settings:NewSetting: False

Updated configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!

Deleted configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!
```

## <a name="clean-up-resources"></a>Resources opschonen


[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een nieuw App Configuration-archief gemaakt, en hebt u geleerd hoe u toegang kunt krijgen sleutel-waarden vanuit een Python-app.

Ga voor meer codevoorbeelden naar:

> [!div class="nextstepaction"]
> [Azure App Configuration client library samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration/samples)
