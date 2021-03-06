---
title: Matrice de version et module Ansible pour Azure
description: Matrice de version et module Ansible pour Azure
ms.service: ansible
keywords: ansible, rôles, matrice, version, azure, devops
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 03/25/2018
ms.topic: article
ms.openlocfilehash: 011cb173ffdecc7a22c2e470209719ccaf6bda58
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2018
---
# <a name="ansible-module-and-version-matrix"></a>Matrice de version et module Ansible

## <a name="ansible-modules-for-azure"></a>Modules Ansible pour Azure
Ansible est fourni avec une série de modules qui peuvent être exécutés directement sur les hôtes distants ou par le biais de playbooks.
Cet article répertorie les modules Ansible pour Azure qui peuvent provisionner des ressources cloud Azure telles qu’une machine virtuelle, une mise en réseau et des services conteneur. Vous pouvez obtenir ces modules par le biais de la version officielle d’Ansible ou des rôles de playbook suivants publiés par Microsoft.

| Module Ansible pour Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Rôle de playbook [azure_preview_module](#introduction-to-azurepreviewmodule) | 
|---------------------------------------------|--------------|-----------------------------|-------------------------------------| 
| **Calcul**                    |           |                          |                                  | 
| azure_rm_availabilityset                    | OUI          | OUI                         | OUI                                 | 
| azure_rm_availabilityset_facts              | OUI          | OUI                         | OUI                                 | 
| azure_rm_deployment                         | OUI          | OUI                         | OUI                                 | 
| azure_rm_virtualmachine_scaleset_facts      | OUI          | OUI                         | OUI                                 | 
| azure_rm_virtualmachineimage_facts          | OUI          | OUI                         | OUI                                 | 
| azure_rm_resourcegroup                      | OUI          | OUI                         | OUI                                 | 
| azure_rm_resourcegroup_facts                | OUI          | OUI                         | OUI                                 | 
| azure_rm_virtualmachine                     | OUI          | OUI                         | OUI                                 | 
| azure_rm_virtualmachine_extension           | OUI          | OUI                         | OUI                                 | 
| azure_rm_virtualmachine_scaleset            | OUI          | OUI                         | OUI                                 | 
| azure_rm_image                              |              | OUI                         | OUI                                 | 
| **Mise en réseau**                    |           |                          |                                  | 
| azure_rm_virtualnetwork                     | OUI          | OUI                         | OUI                                 | 
| azure_rm_virtualnetwork_facts               | OUI          | OUI                         | OUI                                 | 
| azure_rm_subnet                             | OUI          | OUI                         | OUI                                 | 
| azure_rm_networkinterface                   | OUI          | OUI                         | OUI                                 | 
| azure_rm_networkinterface_facts             | OUI          | OUI                         | OUI                                 | 
| azure_rm_publicipaddress                    | OUI          | OUI                         | OUI                                 | 
| azure_rm_publicipaddress_facts              | OUI          | OUI                         | OUI                                 | 
| azure_rm_dnsrecordset                       | OUI          | OUI                         | OUI                                 | 
| azure_rm_dnsrecordset_facts                 | OUI          | OUI                         | OUI                                 | 
| azure_rm_dnszone                            | OUI          | OUI                         | OUI                                 | 
| azure_rm_dnszone_facts                      | OUI          | OUI                         | OUI                                 | 
| azure_rm_loadbalancer                       | OUI          | OUI                         | OUI                                 | 
| azure_rm_loadbalancer_facts                 | OUI          | OUI                         | OUI                                 | 
| azure_rm_appgw                              | -            | -                           | OUI                                 | 
| azure_rm_appgwroute                         | -            | -                           | OUI                                 | 
| azure_rm_appgwroute                         | -            | -                           | OUI                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | OUI                                 |
| azure_rm_appgwroutetable                    | -            | -                           | OUI                                 |
| azure_rm_securitygroup                      | OUI          | OUI                         | OUI                                 | 
| azure_rm_appgwroutetable_facts              | -            | -                           | OUI                                 | 
| **Stockage**                    |           |                          |                                  | 
| azure_rm_storageaccount                     | OUI          | OUI                         | OUI                                 | 
| azure_rm_storageaccount_facts               | OUI          | OUI                         | OUI                                 | 
| azure_rm_storageblob                        | OUI          | OUI                         | OUI                                 | 
| azure_rm_managed_disk                       | OUI          | OUI                         | OUI                                 | 
| azure_rm_managed_disk_facts                 | OUI          | OUI                         | OUI                                 | 
| **Conteneurs**                    |           |                          |                                  | 
| azure_rm_acs                                | OUI          | OUI                         | OUI                                 | 
| azure_rm_containerinstance                  | -            | OUI                         | OUI                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | OUI                                 | 
| azure_rm_containerregistry                  | -            | OUI                         | OUI                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | OUI                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | OUI                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | OUI                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | OUI                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | OUI                                 | 
| **Azure Functions**                    |           |                          |                                  | 
| azure_rm_functionapp                        | OUI          | OUI                         | OUI                                 | 
| azure_rm_functionapp_facts                  | OUI          | OUI                         | OUI                                 | 
| **Bases de données**                    |           |                          |                                  | 
| azure_rm_sqlserver                          | -            | OUI                         | OUI                                 | 
| azure_rm_sqlserver_facts                    | -            | OUI                         | OUI                                 | 
| azure_rm_sqldatabase                        | -            | OUI                         | OUI                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | OUI                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | OUI                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | OUI                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | OUI                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | OUI                                 | 
| azure_rm_mysqlserver                        | -            | OUI                         | OUI                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | OUI                                 | 
| azure_rm_mysqldatabase                      | -            | OUI                         | OUI                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | OUI                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | OUI                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | OUI                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | OUI                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | OUI                                 | 
| azure_rm_postgresqlserver                   | -            | OUI                         | OUI                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | OUI                                 | 
| azure_rm_postgresqldatabase                 | -            | OUI                         | OUI                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | OUI                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | OUI                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | OUI                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | OUI                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | OUI                                 | 
| **Key Vault**                    |           |                          |                                  | 
| azure_rm_keyvault                           | -            | OUI                         | OUI                                 |
| azure_rm_keyvault_facts                     | -            | -                           | OUI                                 |
| azure_rm_keyvaultkey                        | -            | OUI                         | OUI                                 |
| azure_rm_keyvaultsecret                     | -            | OUI                         | OUI                                 |


## <a name="introduction-to-playbook-role-for-azure"></a>Introduction au rôle de playbook pour Azure
Le [rôle de playbook azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) est le rôle le plus complet et inclut tous les derniers modules Azure. Les mises à jour et correctifs de bogues sont effectués de façon plus opportune qu’avec la version Ansible officielle. Si vous utilisez Ansible à des fins de provisionnement de ressources Azure, nous vous invitons à installer le rôle azure_preview_module.

Le rôle de playbook azure_preview_module est publié toutes les trois semaines.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les rôles de playbook, consultez [Creating Reusable Playbooks](http://docs.ansible.com/ansible/latest/playbooks_reuse.html) (Création de playbooks réutilisables). 
