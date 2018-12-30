---
id: 355
title: App-V / Database Mirroring
date: 2010-11-27T01:08:21+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=355
permalink: /app-v-database-mirroring/
aktt_notify_twitter:
  - 'no'
categories:
  - Application Virtualization
  - Virtualization
tags:
  - App-V
  - fail over
  - Mirroring
  - SQL
---
**SQL DB fail over for App-V Management Server**

Since <a href="http://www.google.ch/url?sa=t&source=web&cd=1&ved=0CBkQFjAA&url=http%3A%2F%2Fwww.microsoft.com%2Fdownloads%2Fdetails.aspx%3FFamilyID%3Dc8ef481e-ddbd-472b-8ce8-ffa2fdba314f&rct=j&q=download%20app-v%204.5%20sp2&ei=7jrwTOW8A9D2sgbwlaH7Cg&usg=AFQjCNHA24AFRCFzuDmv7FTfeiBa-gpSNA&cad=rja" target="_blank">4.5 SP2</a> App-V Supports SQL Database mirroring.
  
That helps if you&#8217;re using the full enterprise model and want to increase the availability of your App-V environment but not like to use MS fail over clustering for the database.

For how to Configure Database Mirroring read the following articles
  
<a href="http://go.microsoft.com/fwlink/?LinkId=187478" target="_blank">For SQL 2005 </a>
  
<a href="http://go.microsoft.com/fwlink/?LinkId=187477" target="_blank">For SQL 2008</a>

**How to configure your App-V Management Server(s) to support fail over  to a mirrored database**

  * enable TCP/IP and Named Pipes on both SQL Servers
  * run SQL Server and SQL browser service under domain account
  * If you use Kerberos (I&#8217;ll recommend that) you have to manually <a href="http://technet.microsoft.com/en-us/library/cc773257(WS.10).aspx" target="_blank">set the SPN</a> for both SQL Servers (Principal and mirror) for the service account ( setspn -A MSSQLSvc/<FQDN> <SQL\_Service\_Account> )
  * Restart both SQL Server services
  * Add a login for the computer account of the principal server to mirror server <domain\server$> and grant SFT_Admin role to AppV DB
  * Add a login for the computer account of the App-V Management Server to mirror server <domain\server$> and grant SFT_Admin role to AppV DB
  * Grant connect permission to the logins created above
  
    USE master;
  
    GRANT CONNECT ON ENDPOINT::Mirroring TO &#8220;domain\server$&#8221; 
  
        WITH GRANT OPTION;
  
    GO
  * Edit the Connection File on the AppV Management Server
  * (i.e. C:\Program Files\Microsoft System Center App Virt Management Server\App Virt Management Service\SftMgmt.udl)
  * On the Provider tab, select the OLE DB provider SQL Server Native Client 10.0.
  * Click Next to select the Connection tab. In the Server Name box, enter the server name of the SQL Server. Next, select Use Windows NT Integrated Security. Finally, click the list Select the database, and then select the App-V database name.
  * Click the All tab, and then select the entry fail over Partner. Click Edit Value, and then enter the server name of the failover SQL Server. Click OK

  * Add the following Reg Keys/Values to the App-V Management Server
  
    HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Softgrid\4.5\Server\SQLFailOverServerName as REG_SZ and then set the value to the host name of the SQL Server that hosts the mirror.
  
    HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Softgrid\4.5\Server\SQLFailOverServerPort as DWORD and then set the value to the port number that is used for SQL on the computer that is running SQL Server to host the mirror. If you are using a named instance for the mirror this key value must be set to the port number that is used for the named instance.

**The failover case (where your primary database server has gone)**

In my environment I didn&#8217;t use a witness server. So in a fail over case I&#8217;ve to manually fail over the mirror database, so it can be used by the App-V Management server.

USE
  
master
  
GO
  
ALTER DATABASE <your appv db name> SET PARTNER FORCE\_SERVICE\_ALLOW\_DATA\_LOSS
  
GO

!!! This can only be performed if the principal SQL server is not reachable. Be aware that you could have some data loss using this option (depending if you configured a synchronous mirroring or not)

**To go back (if original server is available again)**

&#8211; backup database on mirror server
  
&#8211; restore db on the recovered server (with no recovery and override)
  
&#8211; configure the mirroring (so your recovered server will get a mirror from the new principal)
  
&#8211; stop the sql server on the principal
  
&#8211; repeat the above statement on the recovered server, which will be your principal again.
  
&#8211; repeat the steps for your original mirror server to get the original situation back up and running