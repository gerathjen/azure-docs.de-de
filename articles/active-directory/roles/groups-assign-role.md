---
title: Zuweisen von Azure AD-Rollen zu Gruppen – Azure Active Directory
description: Weisen Sie Gruppen, denen Rollen zugewiesen werden können, im Azure-Portal, über PowerShell oder über die Graph-API Azure AD-Rollen zu.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 07/30/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35982d62a0e92b5f4647243cde5920529d6c2989
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122338895"
---
# <a name="assign-azure-ad-roles-to-groups"></a>Zuweisen von Azure AD-Rollen zu Gruppen

In diesem Abschnitt wird beschrieben, wie ein IT-Administrator einer Azure AD-Gruppe (Azure Active Directory) eine Azure AD-Rolle zuweisen kann.

## <a name="prerequisites"></a>Voraussetzungen

- Eine Lizenz vom Typ Azure AD Premium P1 oder P2
- „Administrator für privilegierte Rollen“ oder „Globaler Administrator“
- AzureAD-Modul bei Verwendung von PowerShell
- Administratorzustimmung bei Verwendung von Graph-Tester für die Microsoft Graph-API

Weitere Informationen finden Sie unter [Voraussetzungen für die Verwendung von PowerShell oder Graph-Tester](prerequisites.md).

## <a name="azure-portal"></a>Azure-Portal

Das Zuweisen einer Gruppe zu einer Azure AD-Rolle ähnelt der Zuweisung von Benutzern und Dienstprinzipalen mit der Ausnahme, dass nur Gruppen verwendet werden können, denen Rollen zugewiesen werden können. Im Azure-Portal werden nur Gruppen angezeigt, denen Rollen zugewiesen werden können.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder bei [Azure AD Admin Center](https://aad.portal.azure.com) an.

1. Klicken Sie auf **Azure Active Directory** > **Rollen und Administratoren**, und wählen Sie die Rolle aus, die Sie zuweisen möchten.

1. Wählen Sie auf der Seite ***Rollenname** _ die Option _*Zuweisung hinzufügen** aus.

   ![Hinzufügen der neuen Rollenzuweisung](./media/groups-assign-role/add-assignment.png)

1. Wählen Sie die Gruppe aus. Es werden nur die Gruppen angezeigt, die zu Azure AD-Rollen hinzugefügt werden können.

    [![Es werden nur die Gruppen für eine neue Rollenzuweisung angezeigt, die zugewiesen werden können.](./media/groups-assign-role/eligible-groups.png "Es werden nur die Gruppen für eine neue Rollenzuweisung angezeigt, die zugewiesen werden können.")](./media/groups-assign-role/eligible-groups.png#lightbox)

1. Wählen Sie **Hinzufügen**.

Weitere Informationen zum Zuweisen von Rollenberechtigungen finden Sie unter [Zuweisen von Administrator- und anderen Rollen zu Benutzern mithilfe von Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="powershell"></a>PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Erstellen einer Gruppe, der die Rolle zugewiesen werden kann

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true 
```

### <a name="get-the-role-definition-for-the-role-you-want-to-assign"></a>Abrufen der Rollendefinition für die Rolle, die Sie zuweisen möchten

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'" 
```

### <a name="create-a-role-assignment"></a>Erstellen einer Rollenzuweisung

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id 
```

## <a name="microsoft-graph-api"></a>Microsoft Graph-API

### <a name="create-a-group-that-can-be-assigned-azure-ad-role"></a>Erstellen einer Gruppe, die einer Azure AD-Rolle zugewiesen werden kann

```
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [],
"mailEnabled": false,
"securityEnabled": true,
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true
}
```

### <a name="get-the-role-definition"></a>Abrufen der Rollendefinition

```
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq 'Helpdesk Administrator'
```

### <a name="create-the-role-assignment"></a>Erstellen der Rollenzuweisung

```
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<ID of role definition>",
"directoryScopeId":"/"
}
```
## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von Azure AD-Gruppen zum Verwalten von Rollenzuweisungen](groups-concept.md)
- [Behandeln von Problemen bei Azure AD-Rollen mit Gruppenzuweisung](groups-faq-troubleshooting.yml)
