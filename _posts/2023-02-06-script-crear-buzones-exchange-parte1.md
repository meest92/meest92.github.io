---
title: 'Script para crear buzones en Exchange. Parte 1'
layout: post
post-image: https://cdn.pixabay.com/photo/2012/04/15/17/55/note-34686_960_720.png
description: En esta entrada del blog veremos un script para crear nuevos buzones de manera individual en un Exchange 2019 propio. Este mismo script que veremos será capaz de crear los buzones de manera masiva mediante un archivo .CSV, aunque cabe destacar que esta funcionalidad aún está en fase de desarrollo.
date: 2023-02-06
tags: 
- powershell 
- script 
- exchange
- windows
- sysadmin
- correo
---

Volvemos a las entradas un poco más técnicas respecto a las últimas que hemos visto en el blog. Esta vez os presento un script que nos va a permitir **crear buzones de Exchange** en nuestro propio servidor de manera semi-automatizada.

Para crear estos buzones solamente nos hará falta saber el nombre del usuario, apellido, email y el tenant (OU) donde queremos crearlo. Cabe mencionar que el escenario de este script es un servidor Exchange propio donde existen diferentes clientes que tienen alojado su correo electrónico.

A continuación os dejo el código y vamos a ir comentando las diferentes partes que tiene. Podéis descargar el script completo desde el repositorio de [Github](https://github.com/meest92/addMailBox)

Empezamos con la implementación de un menú, donde podremos elegir las dos opciones que actualmente existen: 

1. Agregar un buzón de manera individual
2. Agregar de manera masiva los buzones mediante un CSV (código actualmente en desarrollo).

````powershell
# Main menu with all options, if you want to quit press "q".
function showMenu{
        param ([string]$title = 'Menu de creacion de buzon Exchange')

        Clear-host
        Write-Host "================ $title ================"

        Write-Host "1: Agregar usuario individual."
        Write-Host "2: Agregar usuarios masivo."
        Write-Host "Q: Salir."
}

do
{
        showMenu
        $selection = Read-Host "Elija la opcion que quiere llevar a cabo"

        switch($selection)
        {
                '1' {
                        addOneUser
                }
                '2'{
                        addMassiveUser
                }

        }
}
until ($selection -eq 'q')
````


Como podemos ver, existe una función llamada **"addOneUser"** que será la encargada de pedirnos la información para generar el usuario. Esta función llama a otras funciones adicionales para comprobar y crear nuestro buzón de manera satisfactoria.


````powershell
# Function that user write the information for the new Mailbox
function addOneUser{
        Clear-host
        Write-Host "================ Ha entrado en el modo de creacion de buzones individuales ================"
        $name= Read-Host "[+] Especifique el nombre del usuario"
        if(!($name -eq '')){
                $surname = Read-Host "[+]Especifique el apellido del usuario"
                if(!($surname -eq '')){
                        $email = Read-Host "[+]Especifique el email del usuario"
                        if(!($email -eq '')){
                                $tenant = Read-Host "[+] Especifique el cliente"
                                OuExist($tenant)
                                checkUser($email,$tenant)
                                userAdd($name,$surname,$email,$tenant)
                        }
                }
        }else{
                Write-Host "[!] ERROR: Ha introducido algun parametro incorrecto"
                pause
                addOneUser
        }
}
````


Dentro del último condicional existen las funciones **"OuExist", "chechUser" y "UserAdd"**, las cuales nos van a permitir comprobar que la OU que le indiquemos al script exista, así como el usuario que le hemos introducido también exista dentro de la OU. Finalmente, si no existe ningún error en las funciones anteriores, se creará el usuario.


````powershell
# Function to check if OU exist or not.
function OuExist{

        $Path = "OU=$tenant,OU=Tenants,DC=lab,DC=local" # SPECIFIES YOUR PATH

        $ou_exists = [adsi]::Exists("LDAP://$Path")

        if (-not $ou_exists) { # If not exist throw error and go arround to addOneUser function.
                Write-Host('[!] Supplied Path does not exist. Try Again!')
                pause
                Clear-Host
                addOneUser
        } else { # If exists write in debug mode that this path exists.
                Write-Debug "Path Exists: $Path"
        }
}
````

````powershell
# Function to check if user exist or not in OU.
function checkUser{
        $ErrorActionPreference = "SilentlyContinue" # Silent errors from cmdlets

        $Path = "OU=$tenant,OU=Tenants,DC=lab,DC=local" # SPECIFIES YOUR PATH
        
        $ADUser = Get-ADUser -Filter {UserPrincipalName -eq $email} -SearchBase $Path -SearchScope OneLevel #Search if user by UserPrincipalName exists.
       
        if (-not $ADUser) { # If not exists write on debug mode that this user not exists.
                Write-Debug ('[+] Supplied User does not exist.')
        } else { # If exist thow an alert and go arround to addOneUser function.
                Write-Host ('[!] User Exists, try again!')
                pause
                Clear-Host
                addOneUser
        }
}
````

````powershell
# Function to add new User if OU exist and if user don't exist into OU.
function userAdd {
        Add-PSSnapin Microsoft.Exchange.Management.PowerShell.SnapIn
        $c = NewCredencial # Get randomized credential.
        $secureString = ConvertTo-SecureString $c -AsPlainText -Force # Convert credential to a Secure String.

        # Try to create user, if fails throw an error to exit.
        try{
                New-Mailbox -Name "$name $surname | $tenant" -Alias "$tenant_$name$surname" -OrganizationalUnit "lab.local/Tenants/$tenant" -UserPrincipalName "$email" -SamAccountName "$name$surname" -FirstName "$name" -LastName "$surname" -Password $secureString -ResetPasswordOnNextLogon $false
                Set-Mailbox "$email" -CustomAttribute1 "$tenant"
        } catch {
                Throw("[!] ERROR Creando el usuario")
        }
        Clear-Host
        # Write to screen the resume information for the new user.
        Write-Host "================ Resumen de la operacion ================"
        Write-Host "Nombre: $name"
        Write-Host "Apellido: $surname"
        Write-Host "Email: $email"
        Write-Host "Password: $c"
        pause
        
}
````


Podemos apreciar que en la función **"userAdd"** existe una llamada a "NewCredential", esta función es la que se encargara de crearnos de manera randomizada, un password con **16 caracteres alfanuméricos** y con símbolos para que sea un **password fuerte**.


````powershell
# Function to create a new 16 characters random password.
function NewCredencial {
        Add-Type -AssemblyName 'System.Web'
        return [System.Web.Security.Membership]::GeneratePassword(16, 1)
}
````


Una vez terminado el script y generado el buzón correctamente, por pantalla nos aparecerá un **resumen** indicándonos los datos que hay que tener en cuenta, como el **usuario y la contraseña generada aleatoriamente**.

Esta entrada es la **primera parte** del desarrollo de este script, en un futuro espero poder presentaros la segunda parte, donde la idea será poder crear los buzones de forma automatizada mediante un archivo .CSV.

Para acabar, solamente recordaros que me podéis seguir en las redes sociales para estar informados de todas las novedades.

¡Un abrazo!
