---
title: Prendre en main le Kit de développement logiciel (SDK) de passerelle IoT Hub | Microsoft Docs
description: Procédure pas à pas du Kit de développement logiciel (SDK) de passerelle Azure IoT Hub sous Windows pour illustrer les concepts clés que vous devez comprendre lorsque vous utilisez le Kit de développement logiciel (SDK) de passerelle Azure IoT Hub.
services: iot-hub
documentationcenter: ''
author: chipalost
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2016
ms.author: andbuc

---
# <a name="iot-gateway-sdk-(beta)---get-started-using-windows"></a>Kit de développement logiciel de passerelle IoT (bêta) - Prise en main de Windows
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Comment créer l'exemple
Avant de commencer, vous devez [configurer votre environnement de développement][lnk-setupdevbox] pour utiliser le Kit de développement logiciel (SDK) sous Windows.

1. Ouvrez une **invite de commandes Développeur pour VS2015** .
2. Accédez au dossier racine de votre copie locale du référentiel **azure-iot-gateway-sdk** .
3. Exécutez le script **tools\\build.cmd**. Ce script crée un fichier de solution Visual Studio, génère la solution, puis exécute les tests. Vous trouverez la solution Visual Studio dans le dossier **build** de votre copie locale du référentiel **azure-iot-gateway-sdk**.

## <a name="how-to-run-the-sample"></a>Comment exécuter l’exemple
1. Le script **build.cmd** crée un dossier appelé **build** dans votre copie locale du référentiel. Ce dossier contient les deux modules utilisés dans cet exemple.
   
    Le script build place **logger_hl.dll** dans le dossier **build\\modules\\logger\\Debug**, et **hello_world_hl.dll** dans le dossier **build\\modules\\hello_world\\Debug**. Utilisez ces chemins d'accès pour la valeur **module path** comme indiqué dans le fichier de paramètres JSON ci-dessous.
2. Le fichier **hello_world_win.json** dans le dossier **samples\\hello_world\\src** est un exemple de fichier de paramètres JSON pour Windows, que vous pouvez utiliser pour exécuter l’exemple. Les paramètres JSON ci-dessous supposent que vous avez cloné le référentiel du SDK de passerelle à la racine de votre lecteur **C:** . Si vous l’avez téléchargé vers un autre emplacement, vous devez modifier en conséquence les valeurs **module path** dans le fichier **samples\\hello_world\\src\\hello_world_win.json**.
3. Pour le module **logger_hl**, dans la section **args**, définissez la valeur **filename** sur le nom et le chemin d’accès du fichier qui contiendra les données de journalisation.
   
    Il s’agit d’un exemple de fichier de paramètres JSON pour Windows qui écrira les données dans le fichier **log.txt** à la racine de votre lecteur **C:**.
   
    ```
    {
      "modules" :
      [
        {
          "module name" : "logger_hl",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\logger\\Debug\\logger_hl.dll",
          "args" : 
          {
            "filename":"C:\\log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\\\modules\\hello_world\\Debug\\hello_world_hl.dll",
          "args" : null
        }
      ],
      "links" :
      [
        {
          "source": "hello_world",
          "sink": "logger_hl"
        }
      ]
    }
    ```
4. Dans une invite de commande, accédez au dossier racine de votre copie locale du référentiel **azure-iot-gateway-sdk** .
5. Exécutez la commande suivante :
   
   ```
   build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json
   ```

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md


<!--HONumber=Oct16_HO2-->


