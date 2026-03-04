# Generar-Backups-automaticamente
Query para generar Backups de Todas las bases de datos automáticamente utilizando  Task Scheduler, programando cada cuando y a que hora.
Contando con un archivo .bat el cual se pone en la tarea para la ejecución


🗄️ Sistema de Backups Automáticos – SQL Server

Este proyecto permite realizar respaldos automáticos de todas las bases de datos en SQL Server utilizando:

📄 Un script SQL

⚙️ Un archivo .bat

🕒 Una tarea programada en Windows

🧹 Eliminación automática de respaldos antiguos

📌 ¿Qué hace este sistema?

✔️ Genera backups .bak de todas las bases de datos
✔️ Guarda los archivos en una carpeta definida
✔️ Elimina automáticamente archivos antiguos (ej. mayores a 7 días)
✔️ Se ejecuta automáticamente sin intervención manual

1️⃣ Script SQL de Backup

Este script genera el respaldo de todas las bases de datos del servidor (excepto las del sistema si así se configura).

📜 Ejemplo de Script SQL
DECLARE @name NVARCHAR(256)
DECLARE @path NVARCHAR(512)
DECLARE @fileName NVARCHAR(512)
DECLARE @date NVARCHAR(20)

SET @path = 'C:\RespaldoSQL\'
SET @date = CONVERT(VARCHAR(20), GETDATE(), 112)

DECLARE db_cursor CURSOR FOR
SELECT name
FROM sys.databases
WHERE name NOT IN ('tempdb')

OPEN db_cursor
FETCH NEXT FROM db_cursor INTO @name

WHILE @@FETCH_STATUS = 0
BEGIN
    SET @fileName = @path + @name + '_' + @date + '.bak'
    
    BACKUP DATABASE @name
    TO DISK = @fileName
    WITH INIT, FORMAT

    FETCH NEXT FROM db_cursor INTO @name
END

CLOSE db_cursor
DEALLOCATE db_cursor

🔎 ¿Qué hace este script?

Recorre todas las bases de datos.

Genera un archivo .bak con fecha.

Guarda los respaldos en C:\RespaldoSQL.

2️⃣ Archivo .bat

Este archivo ejecuta el script automáticamente usando sqlcmd.

📄 Ejemplo: BackupAllDBs.bat
@echo off

sqlcmd -S NOMBRE_SERVIDOR -E -i C:\Scripts\Backup.sql

echo Backup completado.
pause

🔎 ¿Qué hace este archivo?

Ejecuta el script SQL.

Se conecta al servidor usando autenticación de Windows (-E).

Permite automatizar el proceso sin abrir SSMS.

⚠️ Cambiar NOMBRE_SERVIDOR por el nombre real del servidor SQL.

Ejemplo:

-S localhost\SQLEXPRESS

3️⃣ Eliminación Automática de Backups Antiguos

Para evitar que el disco se llene, se puede agregar al .bat:

forfiles /p "C:\RespaldoSQL" /s /m *.bak /d -7 /c "cmd /c del @path"

🔎 ¿Qué hace esto?

Busca archivos .bak

Elimina los que tengan más de 7 días

Mantiene solo los respaldos recientes

4️⃣ Crear la Tarea Programada en Windows

Para automatizar completamente el proceso:

🕒 Paso 1

Ir a:

Panel de Control → Herramientas Administrativas → Programador de tareas

🕒 Paso 2

Seleccionar:

Crear tarea básica

🕒 Paso 3

Configurar:

Nombre: Backup SQL Diario

Frecuencia: Diario

Hora: (ejemplo 2:00 AM)

🕒 Paso 4

Acción:

Iniciar un programa


Seleccionar el archivo:

C:\Scripts\BackupAllDBs.bat

🕒 Paso 5

Finalizar.

🛠 Requisitos

SQL Server instalado

Permisos de administrador

Carpeta de destino creada (C:\RespaldoSQL)

sqlcmd habilitado

📂 Estructura del Proyecto
📁 Scripts
 ├── Backup.sql
 ├── BackupAllDBs.bat
📁 RespaldoSQL
 └── (archivos .bak generados)

🚀 Ventajas de este Sistema

No depende de SQL Agent (ideal para SQL Express)

Fácil de implementar

Control total desde Windows

Automatización completa


Angel Manuel Ruiz
