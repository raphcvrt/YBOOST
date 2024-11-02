On crée un nouvel utilisateur 'User667' 

![img1](1.png)
![img2](2.png)
![img3](3.png)

on aurait aussi pu faire ainsi 
```PowerShell 
New-ADUser -Name "OsiJack" `
           -DisplayName "osijack" `
           -GivenName "osijack" `
           -Surname "osijack" `
           -SamAccountName "osijack" `
           -UserPrincipalName "osijack@yboost.local" `
           -EmailAddress "osijack@yboost.local" `
           -Path "OU=Personnel,DC=IT-CONNECT,DC=LOCAL" `
           -AccountPassword(Read-Host -AsSecureString "BlackTsar667") `
           -Enabled $true
```

sur le client on ajoute le domaine comme DNS
![img4](4.png)

et on se met dans le bon domaine
![img5](5.png)

je retire l'utilisateur de base
![img6](6.png)

sur le server je crée une GPO 'yboostGPO'
![img7](7.png)

Dans le Group Policy Management Editor je cherche le paramètre correspondant a la protection en temps réel (trouvé dans Computer Configuration > Policies > Administrative Templates > Windows Components > Windows Security > Virus and threat protection) j'active ensuite 'Hyde Virus and threat protection Area'
![img8](8.png)

je force la Màj 
![img9](9.png)

ET C'EST BON
![img10](10.png)

**Je comptais faire les autres parties du TP mais je ne pense pas pouvoir les dinir d'ici Dimanche**