---
title: Créer et publier une application managée du catalogue de services Azure | Microsoft Docs
description: Montre comment créer une application managée Azure destinée aux membres de votre organisation.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.date: 05/15/2018
ms.author: tomfitz
ms.openlocfilehash: b7f8bbcad39000e7e71149824535a6a82b26c758
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305308"
---
# <a name="publish-a-managed-application-for-internal-consumption"></a>Publier une application managée pour une utilisation interne

Vous pouvez créer et publier des[applications managées](overview.md) Azure pour les besoins des membres de votre organisation. Par exemple, un service informatique peut publier des applications managées destinées à contrôler la conformité par rapport aux normes de l’organisation. Ces applications managées sont disponibles dans le catalogue de services, et non dans la Place de marché Azure.

Pour publier une application managée pour le catalogue de services, vous devez :

* Créer un modèle qui définit les ressources à déployer avec l’application managée.
* Définir les éléments d’interface utilisateur du portail lors du déploiement de l’application managée.
* Créer un package .zip qui contient les fichiers de modèles nécessaires.
* désigner l’utilisateur, le groupe ou l’application qui doit avoir accès au groupe de ressources dans l’abonnement de l’utilisateur ;
* créer la définition de l’application managée qui pointe vers le package .zip et qui demande l’accès pour l’identité.

Pour cet article, votre application managée contient uniquement un compte de stockage. Il a pour but d’illustrer les étapes de la publication d’une application managée. Pour obtenir des exemples complets, consultez [Exemples de projets pour des applications managées Azure](sample-projects.md).

## <a name="create-the-resource-template"></a>Créer le modèle de ressource

Chaque définition d’application managée contient un fichier nommé **mainTemplate.json**. Vous y définissez les ressources Azure à provisionner. Le modèle n’est en rien différent d’un modèle Resource Manager normal.

Créez un fichier nommé **mainTemplate.json**. Le nom respecte la casse.

Ajoutez le code JSON suivant à votre fichier. Il définit les paramètres pour la création d’un compte de stockage et spécifie les propriétés du compte de stockage.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        },
        "storageAccountType": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
        }
    }
}
```

Enregistrez le fichier mainTemplate.json.

## <a name="create-the-user-interface-definition"></a>Créer la définition d’interface utilisateur

Le portail Azure utilise le fichier **createUiDefinition.json** afin de générer l’interface utilisateur pour les utilisateurs qui créent l’application managée. Vous déterminez la façon dont les utilisateurs fournissent une entrée pour chaque paramètre. Vous pouvez utiliser des options comme un sélecteur de liste déroulante, une zone de texte, une zone de mot de passe et d’autres outils de saisie. Pour en savoir plus sur la création d’un fichier de définition de l’interface utilisateur pour une application gérée, consultez [Prise en main de CreateUiDefinition](create-uidefinition-overview.md).

Créez un fichier nommé **createUiDefinition.json**. Le nom respecte la casse.

Ajoutez le code JSON suivant au fichier.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "storageConfig",
                "label": "Storage settings",
                "subLabel": {
                    "preValidation": "Configure the infrastructure settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Storage settings",
                "elements": [
                    {
                        "name": "storageAccounts",
                        "type": "Microsoft.Storage.MultiStorageAccountCombo",
                        "label": {
                            "prefix": "Storage account name prefix",
                            "type": "Storage account type"
                        },
                        "defaultValue": {
                            "type": "Standard_LRS"
                        },
                        "constraints": {
                            "allowedTypes": [
                                "Premium_LRS",
                                "Standard_LRS",
                                "Standard_GRS"
                            ]
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
            "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
            "location": "[location()]"
        }
    }
}
```

Enregistrez le fichier createUiDefinition.json.

## <a name="package-the-files"></a>Empaqueter les fichiers

Ajoutez les deux fichiers à un fichier .zip nommé app.zip. Les deux fichiers doivent se trouver au niveau racine du fichier .zip. Si vous les placez dans un dossier, vous obtenez une erreur au moment de créer la définition de l’application managée qui indique que les fichiers nécessaires ne sont pas présents. 

Chargez le package à un emplacement accessible à partir de là où il peut être utilisé. 

```powershell
New-AzureRmResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context

New-AzureStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzureStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>Créer la définition d’application gérée

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Créer un groupe d’utilisateurs ou une application Azure Active Directory

L’étape suivante consiste à sélectionner un groupe d’utilisateurs ou une application pour gérer les ressources pour le compte du client. Ce groupe d’utilisateurs ou l’application dispose d’autorisations sur le groupe de ressources managé en fonction du rôle attribué. Le rôle peut être n’importe quel rôle Contrôle d’accès en fonction du rôle (RBAC) intégré, par exemple Propriétaire ou Contributeur. Vous pouvez également autoriser un utilisateur individuel à pour gérer les ressources, mais en général, cette autorisation est attribuée à un groupe d’utilisateurs. Pour créer un groupe d’utilisateurs Active Directory, consultez [Créer un groupe et ajouter des membres dans Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

Vous avez besoin de l’ID d’objet du groupe d’utilisateurs à utiliser pour gérer les ressources. 

```powershell
$groupID=(Get-AzureRmADGroup -DisplayName mygroup).Id
```

### <a name="get-the-role-definition-id"></a>Obtenir l’ID de définition de rôle

Ensuite, vous avez besoin de l’ID de définition de rôle du rôle RBAC intégré auquel vous souhaitez accorder l’accès à l’utilisateur, au groupe d’utilisateurs ou à l’application. En règle générale, vous utilisez le rôle Propriétaire, Collaborateur ou Lecteur. La commande suivante montre comment obtenir l’ID de définition de rôle pour le rôle Propriétaire :

```powershell
$ownerID=(Get-AzureRmRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>Créer la définition d’application gérée

Si vous ne disposez pas déjà d’un groupe de ressources pour stocker la définition de votre application managée, créez-en un maintenant :

```powershell
New-AzureRmResourceGroup -Name appDefinitionGroup -Location westcentralus
```

À présent, créez la ressource de définition de l’application managée.

```powershell
$blob = Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzureRmManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

## <a name="create-the-managed-application"></a>Créer l’application managée

Vous pouvez déployer l’application managée via le portail, PowerShell ou Azure CLI.

### <a name="powershell"></a>PowerShell

Pour commencer, nous allons utiliser PowerShell pour déployer l’application managée.

```powershell
# Create resource group
New-AzureRmResourceGroup -Name applicationGroup -Location westcentralus

# Get ID of managed application definition
$appid=(Get-AzureRmManagedApplicationDefinition -ResourceGroupName appDefinitionGroup -Name ManagedStorage).ManagedApplicationDefinitionId

# Create the managed application
New-AzureRmManagedApplication `
  -Name storageApp `
  -Location westcentralus `
  -Kind ServiceCatalog `
  -ResourceGroupName applicationGroup `
  -ManagedApplicationDefinitionId $appid `
  -ManagedResourceGroupName "InfrastructureGroup" `
  -Parameter "{`"storageAccountNamePrefix`": {`"value`": `"demostorage`"}, `"storageAccountType`": {`"value`": `"Standard_LRS`"}}"
```

Votre application managée et votre infrastructure managée existent maintenant dans l’abonnement.

### <a name="portal"></a>Portail

Maintenant, nous allons utiliser le portail pour déployer l’application managée. Vous voyez l’interface utilisateur que vous avez créée dans le package.

1. Accédez au portail Azure. Sélectionnez **+ Créer une ressource**, puis recherchez **catalogue de services**.

   ![Rechercher catalogue de services](./media/publish-service-catalog-app/create-new.png)

1. Sélectionnez **Application managée du catalogue de services**.

   ![Sélectionner Catalogue de services](./media/publish-service-catalog-app/select-service-catalog-managed-app.png)

1. Sélectionnez **Créer**.

   ![Sélectionner Créer](./media/publish-service-catalog-app/select-create.png)

1. Recherchez l’application managée que vous voulez créer dans la liste des solutions disponibles, puis sélectionnez-la. Sélectionnez **Créer**.

   ![Rechercher l’application managée](./media/publish-service-catalog-app/find-application.png)

1. Fournissez les informations de base nécessaires pour l’application managée. Spécifiez l’abonnement et un nouveau groupe de ressources devant contenir l’application managée. Sélectionnez **Ouest-Centre des États-Unis** comme emplacement. Lorsque vous avez terminé, sélectionnez **OK**.

   ![Spécifier les paramètres de l’application managée](./media/publish-service-catalog-app/add-basics.png)

1. Fournissez des valeurs propres aux ressources de l’application managée. Lorsque vous avez terminé, sélectionnez **OK**.

   ![Fournir des paramètres de ressources](./media/publish-service-catalog-app/add-storage-settings.png)

1. Le modèle valide les valeurs que vous avez fournies. Si la validation aboutit, sélectionnez **OK** pour commencer le déploiement.

   ![Valider l’application managée](./media/publish-service-catalog-app/view-summary.png)

Une fois le déploiement terminé, l’application managée existe dans un groupe de ressources nommé applicationGroup. Le compte de stockage existe dans un groupe de ressources nommé applicationGroup plus une valeur de chaîne de hachage.

## <a name="next-steps"></a>Étapes suivantes

* Pour voir une présentation des applications gérées, consultez [Vue d’ensemble des applications gérées](overview.md).
* Pour voir des exemples de projets, consultez [Exemples de projets pour des applications managées Azure](sample-projects.md).
* Pour en savoir plus sur la création d’un fichier de définition de l’interface utilisateur pour une application gérée, consultez [Prise en main de CreateUiDefinition](create-uidefinition-overview.md).
