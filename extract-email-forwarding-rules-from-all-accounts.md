# Extract-Email-Forwarding-rules-From-all-accounts

## Install module

```
Install-Module -Name ExchangeOnlineManagement
```

## Connect to Exchange Online

```
Connect-ExchangeOnline
```

The following types of automatic forwarding are available in Microsoft 365:

- Users can configure [Inbox rules](https://support.microsoft.com/office/c24f5dea-9465-4df4-ad17-a50704d66c59) to automatically forward messages to external senders (deliberately or as a result of a compromised account).

### To list mailboxes that have forwarding enabled via Inbox Rules:
```
$results = @()

Get-Mailbox -ResultSize Unlimited | ForEach-Object {
    $mailbox = $_
    $rules = Get-InboxRule -Mailbox $mailbox.UserPrincipalName -ErrorAction SilentlyContinue

    $forwardingRules = $rules | Where-Object {
        $_.ForwardTo -ne $null -or
        $_.ForwardAsAttachmentTo -ne $null -or
        $_.RedirectTo -ne $null
    }

    foreach ($rule in $forwardingRules) {
        $results += [PSCustomObject]@{
            Mailbox       = $mailbox.UserPrincipalName
            RuleName      = $rule.Name
            Enabled       = $rule.Enabled
            ForwardTo     = ($rule.ForwardTo -join ", ")
            ForwardAsAtt  = ($rule.ForwardAsAttachmentTo -join ", ")
            RedirectTo    = ($rule.RedirectTo -join ", ")
        }
    }
}

$results | Format-Table -AutoSize

$results | Export-Csv -Path "ForwardingRules.csv" -NoTypeInformation
```

```
PS C:\Users\JJJ\Documents\Useful_PS_Scripts> .\Get_InboxRule_forwarding.ps1
```

- Admins can configure [mailbox forwarding](https://learn.microsoft.com/en-us/exchange/recipients-in-exchange-online/manage-user-mailboxes/configure-email-forwarding) (also known as *SMTP forwarding*) to automatically forward messages to external recipients. The admin can choose whether to forward messages, or keep copies of forwarded messages in the mailbox.

### To list all mailboxes with administrator-set forwarding:

```
Get-Mailbox -ResultSize Unlimited |
Where-Object { $_.ForwardingAddress -ne $null -or $_.ForwardingSMTPAddress -ne $null } |
Select-Object DisplayName, UserPrincipalName, ForwardingAddress, ForwardingSMTPAddress, DeliverToMailboxAndForward
```

> **NOTE**
> - Outbound spam policies aren't part of Standard or Strict preset security policies.
> - The **Standard** and **Strict** values indicate recommended values in the default or custom outbound spam policies.
>
> [Learn more](https://learn.microsoft.com/en-us/defender-office-365/recommended-settings-for-eop-and-office365?view=o365-worldwide)

> **NOTE**
> - Disabling automatic forwarding disables Inbox rules (users) and mailbox forwarding (admins) to external addresses.
> - Forwarding between internal users is not affected by outbound spam filter policies.
>
> [Learn more](https://learn.microsoft.com/en-us/defender-office-365/outbound-spam-policies-external-email-forwarding)
