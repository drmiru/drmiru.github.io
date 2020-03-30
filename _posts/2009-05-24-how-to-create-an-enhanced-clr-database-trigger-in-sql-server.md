---
id: 74
title: How to create an enhanced CLR Database Trigger in SQL Server
date: 2009-05-24T13:04:44+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=74
permalink: /how-to-create-an-enhanced-clr-database-trigger-in-sql-server/
categories:
  - Scripting
  - vb
tags:
  - .net
  - SQL Server
---
In SQL Server 2005 and later Triggers can be more than simple T-SQL statements. You can include complete procedures and program code.  
This post describes how to  create a &#8220;Common Language Runtime&#8221; Trigger on a SQL Database which fires on INSERTS into a specific table.  
A CLR Trigger is a kind of stored procedure including .net compiled program code.

**Step 1 (Create the .NET CLR Code)**

Create a piece of code in Visual Studio or in an editor of your choice.  
The sample code watches a table for inserts and starts a pwershell script with the inserted values as arguments. Of course you can do much more complex things than run a script.  
You can use vb.net, c# or either c++ as source language

Save  the file under C:\cla_trigger.vb

<pre>Imports System.Data.SqlClient</pre>

<pre>Imports Microsoft.SqlServer.Server</pre>

<pre>Imports System</pre>

<pre>Imports System.IO</pre>

<pre>Imports System.Diagnostics</pre>

<pre>Public Class sqltrigger</pre>

<pre>    &lt;SqlTrigger(Name:="SessionTrigger", Target:="&lt;your table name&gt;", Event:="FOR UPDATE")&gt; _</pre>

<pre>    Public Shared Sub UserNameAudit()</pre>

<pre>        'Define SQL query result variables</pre>

<pre>        Dim susername As String</pre>

<pre>        Dim sComputername As String</pre>

<pre>        Dim sID As Integer</pre>

<pre>        'Define contect of CLR Trigger</pre>

<pre>        Dim triggContext As SqlTriggerContext = SqlContext.TriggerContext()</pre>

<pre>        Try</pre>

<pre>            If triggContext.TriggerAction = TriggerAction.Insert Then</pre>

<pre>                Using conn As New SqlConnection("context connection=true")</pre>

<pre>                    conn.Open()</pre>

<pre>                    Dim sqlComm As New SqlCommand</pre>

<pre>                    Dim sqlP As SqlPipe = SqlContext.Pipe()</pre>

<pre>                    sqlComm.Connection = conn</pre>

<pre>                    sqlComm.CommandText = "SELECT USERNAME from INSERTED"</pre>

<pre>                    susername = sqlComm.ExecuteScalar.ToString()</pre>

<pre>                    sqlComm.CommandText = "SELECT COMPUTERNAME from INSERTED"</pre>

<pre>                    sComputername = sqlComm.ExecuteScalar.ToString()</pre>

<pre>                    sqlComm.CommandText = "SELECT ID from INSERTED"</pre>

<pre>                    sID = sqlComm.ExecuteScalar.ToString()</pre>

<pre>                    ' New ProcessStartInfo created</pre>

<pre>                    Dim proc As New ProcessStartInfo</pre>

<pre>                    proc.FileName = "C:\Windows\system32\WindowsPowershell\V1.0\powershell.exe</pre>

<pre>                    proc.Arguments = "C:\Scripts\triggeroutput.ps1" _
                    & " " & sID & " " & susername & " " & sComputername</pre>

<pre>                    proc.WindowStyle = ProcessWindowStyle.Hidden</pre>

<pre>                    ' Start the process</pre>

<pre>                    Process.Start(proc)</pre>

<pre>                End Using</pre>

<pre>            End If</pre>

<pre>        Catch ex As Exception</pre>

<pre>        End Try</pre>

<pre>    End Sub</pre>

**Step 2 (compile the code to a dll (class)**  
C:\Windows\Microsoft.NET\Framework\v2.0.50727/vbc.exe /t:library /out:C:\CLRTrigger.dll /r:sqlaccess.dll C:\cla_trigger.vb

**Step 3 (prepare the database for CLR Trigger Operations)**

<pre>ALTER DATABASE &lt;your database&gt; SET TRUSTWORTHY ON
go
sp_configure 'clr enabled', 1
Go
RECONFIGURE with Override
Go

DROP TRIGGER InsertTrigger
DROP ASSEMBLY clrtrigger
CREATE ASSEMBLY clrtrigger
FROM 'C:\CLRTrigger.dll'
go 

CREATE TRIGGER InsertTrigger
ON &lt;your table&gt;
FOR INSERT
AS
EXTERNAL NAME clrtrigger.sqltrigger.UserNameAudit
go</pre>