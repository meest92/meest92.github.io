---
title: Cómo escribir errores de Cobian Backup en Visor de Eventos de Windows
layout: post
post-image: "https://cdn.pixabay.com/photo/2013/07/12/14/48/bash-148836_960_720.png"
description: En este post veremos como generar un script para escribir si el log de Cobian Backup existe algun error o no en el Visor de Eventos de Windows.
tags:
- powershell
- script
- windows
- cobian
- backup
---

En este post veremos como generar un script que nos permita escribir en el Visor de Eventos de Windows el resultado del log de la copia de seguridad de Cobian Backup.
Este script lo uso a diario, ya que me permite saber mediante el registro que deja en el Visor de Windows si el backup ha sido satisfactorio o por el contrario, si ha fallado. Si tienes algun programa que sea capaz de generar alertas leyendo en visor de eventos, este script puede ser de mucha ayuda.

---

A continuación os dejo el codigo para que lo podais implementar o mejorar y el enlace al repositorio de [Github](https://github.com/meest92/writeEventViewer-CobianBackup):


> $date = Get-Date -Format "yyyy-MM-dd"
>
> $file = "C:\Program Files (x86)\Cobian Backup 11\Logs\log $date.txt"
>
> $logFileExists = Get-EventLog -list \| Where-Object {$_.logdisplayname -eq "Backup Cobian"}
>
>if (! $logFileExists) {
>New-EventLog -LogName "Backup Cobian" -Source "Backup Cobian"
>
>}
>
>if(Select-String -Path $file -Pattern "ERR" -CaseSensitive){
>
>Write-EventLog -log "Backup Cobian" -source "Backup Cobian" -EntryType Error -eventID 1 -Message "Se ha producido un error en la copia de seguridad de Cobian. Revise los logs."
>
>}else {
>
>Write-EventLog -log "Backup Cobian" -source "Backup Cobian" -EntryType Information -eventID 0 -Message "Backup de Cobian realizado sin errores"
>
>}


¡Nos vemos!


