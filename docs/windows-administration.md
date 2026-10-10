# Windows Administration Reference

Practical PowerShell commands and concepts used in the Cyber Motors home lab.

## PowerShell Fundamentals

PowerShell uses cmdlets, variables, objects, and pipelines to administer Windows systems.

### Variables and Objects

Variables begin with `$`. They can store the objects returned by cmdlets.

```powershell
$acl = Get-Acl "C:\CompanyData\Sales"
```

This stores the folder's access-control information in `$acl`.

```powershell
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule(
    "Sales Department",
    "Modify",
    "ContainerInherit,ObjectInherit",
    "None",
    "Allow"
)
```

This creates a permission-rule object. It does not apply the rule to the folder.

### Applying an NTFS Permission Rule

```powershell
$acl.AddAccessRule($rule)
Set-Acl "C:\CompanyData\Sales" $acl
```

`AddAccessRule()` adds the rule to the ACL object in memory. `Set-Acl` applies the modified ACL to the folder.

### Inspecting Permissions

```powershell
Get-Acl "C:\CompanyData\Sales" |
    Format-List Owner, AccessToString
```

Display individual access-control entries:

```powershell
(Get-Acl "C:\CompanyData\Sales").Access |
    Format-Table IdentityReference, FileSystemRights, AccessControlType, IsInherited -AutoSize
```

### Discovering Commands

```powershell
Get-Help Get-Acl -Examples
Get-Command *-Acl
Get-Alias ls
```

- `Get-Help` displays command documentation and examples.
- `Get-Command` discovers available commands.
- `Get-Alias` identifies aliases for other commands.

### Linux and Windows Comparison

| Linux | PowerShell | Purpose |
|---|---|---|
| `getfacl` | `Get-Acl` | Inspect access-control rules |
| `mkdir` | `New-Item -ItemType Directory` | Create a directory |
| `ls` | `Get-ChildItem` | List directory contents |
| `cd` | `Set-Location` | Change location |
| `id` / `groups` | `Get-LocalGroupMember` | Inspect local group membership |

## Administration Workflow

1. Inspect the current configuration.
2. Identify the intended change.
3. Find and understand the relevant command.
4. Apply the smallest appropriate change.
5. Verify the result.
6. Document the resulting state and any important lessons.
