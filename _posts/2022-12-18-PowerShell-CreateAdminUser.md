---
title: Script para crear usuario administrador local y deshabilitar el resto
layout: post
post-image: "https://cdn.pixabay.com/photo/2013/07/12/14/48/bash-148836_960_720.png"
description: En esta entrada al blog veremos como generar un script que será capaz de crearnos un usuario local predefinido por el usuario y protegido por contraseña. También el script será capaz de deshabilitar el resto de usuarios locales y dejar solamente el que crea.
tags:
- powershell
- script
- windows
- dominio
- administrador
- sysadmin
---

En esta entrada al blog veremos como generar un script que será capaz de crearnos un usuario local predefinido por el usuario y protegido por contraseña. También será capaz de deshabilitar el resto de usuarios de la máquina para solamente dejar este usuario activo.

Este script se puede ejecutar mediante las políticas de Windows Server, si tenemos un entorno de dominio. De esa manera, todo equipo que metamos en dominio automáticamente se generará el usuario administrador local que nosotros hemos predefinido y deshabilitar el resto de usuarios por seguridad.

---

A continuación os dejo el codigo para que lo podais implementar o mejorar y el enlace al repositorio de [Github](https://github.com/meest92/CreateAdminUser):


> $user='Adminacc' # Username that you want to create
> 
>$localuserpwd = ConvertTo-SecureString "MyPassword" -AsPlainText -Force # Password that you what to have the user
>
>$logFile = "c:\%HOMEPATH%\Desktop\log.txt" # Log Path, if you don'to want logFile you can't comment this line
>
>Function Write-Log {
>
>  param(
>
>      [Parameter(Mandatory = $true)][string] $message,
>
>      [Parameter(Mandatory = $false)]
>
>      [ValidateSet("INFO","WARN","ERROR")]
>
>      [string] $level = "INFO"
>
>  )
>
>  # Create timestamp
>
>  $timestamp = (Get-Date).toString("yyyy/MM/dd HH:mm:ss")
>
>  # Append content to log file
>
>  Add-Content -Path $logFile -Value "$timestamp [$level] - $message"
>
>}
>
>Function CreateLocalUser {
>
>    process {
>
>      try {
>
>        # Create new user with default password and password never expires as policy
>
>        New-LocalUser -Name $user -Password $localuserpwd -FullName $user -PasswordNeverExpires -ErrorAction stop
>
>        Write-Log -message "$user local user created"
>
>        # Add new user to administrator group
>
>        Add-LocalGroupMember -Group Administradores -Member $user -ErrorAction stop
>
>        Write-Log -message "$user added to the local users group"
>
>      }catch{
>
>        Write-log -message "Creating local account failed" -level "ERROR"
>
>      }
>
>    }    
>
>}
>
>Function Disable-LocalUsers {
>
>process {
>
>      try {
>
>        # Disable all users exept our user local admin
>
>        Get-WmiObject -ComputerName $env:computername -Class Win32_UserAccount -Filter "LocalAccount='True' and 
>Disabled='False' and Name!='$user'"| Disable-LocalUser -ErrorAction stop
>
>        Write-Log -message "All users disabled"
>
>      }catch{
>
>        Write-log -message "Error disabling users" -level "ERROR"
>
>      }
>
>    }    
>
>}
>
>if ( ! (Get-WmiObject -ComputerName $env:computername -Class Win32_UserAccount -Filter "Name='$user'")){
>
>        Write-Log -message "#########"
>
>        Write-Log -message "$env:COMPUTERNAME - Create local user account"
>
>        CreateLocalUser
>
>        Write-Log -message "#########"
>
>        Write-Log -message "$env:COMPUTERNAME - Disable All Localusers"
>
>        Disable-LocalUsers
>
>    }else{
>
>        Write-Log -message "#########"
>
>        Write-Log -message "$env:COMPUTERNAME - Disable All Localusers"
>
>        Disable-LocalUsers
>
>    }
>
>}


Como siempre, muchas gracias por leerlo y compartirlo.

Recordad que podeis seguirme en mis redes sociales para ver mas información.

¡Hasta Pronto!


