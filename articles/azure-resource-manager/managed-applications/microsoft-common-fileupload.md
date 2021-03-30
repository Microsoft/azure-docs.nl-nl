---
title: Element van de gebruikers interface van FileUpload
description: Hierin wordt het micro soft. common. FileUpload UI-element voor Azure Portal beschreven. Hiermee kunnen gebruikers bestanden uploaden bij het implementeren van een beheerde toepassing.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: tomfitz
ms.openlocfilehash: c4eb3972be85ac7e291380f64127c20424b0d0f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87004274"
---
# <a name="microsoftcommonfileupload-ui-element"></a>UI-element micro soft. common. FileUpload

Een besturings element waarmee een gebruiker een of meer bestanden kan opgeven die moeten worden geüpload.

## <a name="ui-sample"></a>UI-voor beeld

![Microsoft.Common.FileUpload](./media/managed-application-elements/microsoft-common-fileupload.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.FileUpload",
  "label": "Some file upload",
  "toolTip": "",
  "constraints": {
    "required": true,
    "accept": ".doc,.docx,.xml,application/msword"
  },
  "options": {
    "multiple": false,
    "uploadMode": "file",
    "openMode": "text",
    "encoding": "UTF-8"
  },
  "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

Als opties. Multiple is False en Options. uploadMode is bestand, dan heeft de uitvoer de inhoud van het bestand als een JSON-teken reeks:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Als opties. Multiple de waarde True and'options. uploadMode is. de uitvoer heeft de inhoud van de bestanden als een JSON-matrix:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Als opties. Multiple is False en de opties. uploadMode is URL, dan heeft de uitvoer een URL als JSON-teken reeks:

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

Als opties. Multiple de waarde True heeft en de opties. uploadMode is URL, dan heeft de uitvoer een lijst met Url's als een JSON-matrix:
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

Bij het testen van een CreateUiDefinition kapten sommige browsers (zoals Google Chrome) Url's af die zijn gegenereerd door het element micro soft. common. FileUpload in de browser console. Mogelijk moet u met de rechter muisknop op afzonderlijke koppelingen klikken om de volledige Url's te kopiëren.

## <a name="remarks"></a>Opmerkingen

- `constraints.accept` Hiermee geeft u de typen bestanden die worden weer gegeven in het dialoog venster bestand van de browser. Zie de [HTML5-specificatie](https://html.spec.whatwg.org/multipage/input.html#attr-input-accept) voor toegestane waarden. De standaard waarde is **Null**.
- Als `options.multiple` is ingesteld op **True**, mag de gebruiker meer dan één bestand selecteren in het dialoog venster bestand van de browser. De standaardwaarde is **onwaar**.
- Dit element ondersteunt het uploaden van bestanden in twee modi, op basis van de waarde van `options.uploadMode` . Als het **bestand** is opgegeven, heeft de uitvoer de inhoud van het bestand als een blob. Als de **URL** is opgegeven, wordt het bestand geüpload naar een tijdelijke locatie en bevat de uitvoer de URL van de blob. Tijdelijke blobs worden na 24 uur leeg gemaakt. De standaard waarde is **File**.
- Een geüpload bestand is beveiligd. De uitvoer-URL bevat een [SAS-token](../../storage/common/storage-sas-overview.md?toc=/azure/storage/blobs/toc.json) voor toegang tot het bestand tijdens de implementatie.
- De waarde van `options.openMode` bepaalt hoe het bestand wordt gelezen. Als het bestand wordt verwacht als tekst zonder opmaak, geeft u **tekst** op. u kunt ook **binary** opgeven. De standaard waarde is **tekst**.
- Als `options.uploadMode` is ingesteld op **File** en `options.openMode` is ingesteld op **binary**, is de uitvoer base64-gecodeerd.
- `options.encoding` Hiermee geeft u de code ring op die moet worden gebruikt bij het lezen van het bestand. De standaard waarde is **UTF-8** en wordt alleen gebruikt wanneer `options.openMode` is ingesteld op **tekst**.

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
