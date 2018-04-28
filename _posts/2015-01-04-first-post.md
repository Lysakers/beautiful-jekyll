---
layout: post
title: First post!
image: /img/hello_world.jpeg
---

This is my first post, how exciting!


```powershell
$csv = Import-Csv -Encoding UTF8 -Delimiter ";" -Path C:\temp\steffen\pass\HellandHeimen.csv
$users = $csv.navn

$xpath = "/Person[(ObjectID = /Group[ObjectID='f1de1f72-df82-4f22-a9ab-04ede19fb9b7']/ExplicitMember) and EnableUser = True]"
$users = Get-FIMObjectByXPath $xpath

$pwdset = @()

foreach ($user in $users) {
$passwordtext = "error $user"
$enabled = Get-FIMObjectByXPath "/Person[DisplayName='$user']"

        if (get-aduser -identity $enabled.Accountname -properties lastlogondate | where {$_.lastlogondate}) {
                $passwordtext = "$user har allerede benyttet sin konto. "            
               }
            else {
                Set-ADAccountPassword -Identity $enabled.Accountname -NewPassword (ConvertTo-SecureString -String $enabled.InitialPassword -AsPlainText -Force) -Confirm:$false
          $password = $enabled.initialPassword
                $passwordtext = "Det ser ut til at $user aldri har brukt sin nye konto, slik at vi har satt passordet til <b>$password</b>." 
                }
write-host "$passwordtext"
}

$pwdset
$pwdset.count
$pwdset | Select AccountName, Email, InitialPassword, DisplayName, Description | Out-GridView -Title "Kopier til Excel" -Wait

```
