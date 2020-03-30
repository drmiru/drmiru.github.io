---
id: 4866
title: Windows Server 2016 March Quality Update
date: 2017-03-14T21:09:56+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=4866
permalink: /windows-server-2016-march-quality-update/
bfa_virtual_template:
  - hierarchy
categories:
  - Microsoft
  - Storage Spaces Direct
tags:
  - Windows Server 2016
---
Today Microsoft released the March monthly Quality Update for Windows Server 2016 / Windows 10.

Really glad to see that the product teams took our feedback very serious and we have know the brief list of fixed issues re-introduced in the KB article. Amongst several critical fixes for S2D, there are also other fixes we&#8217;ve been waiting for. So it&#8217;s time to patch now 😉

The update can be obtained through the following link or by using Windows Update / WSUS.

<a href="https://support.microsoft.com/en-us/help/4013429/windows-10-update-kb4013429" target="_blank">https://support.microsoft.com/en-us/help/4013429/windows-10-update-kb4013429</a>

There is also a Servicing Stack Update (SSU) for March, so if you are installing manually you must also first install KB4013418

<https://support.microsoft.com/en-us/help/4013418/servicing-stack-update-for-windows-10-version-1607-and-windows-server-2016-march-14-2017>

List of fixes issues in KB4013429:

<div class="container">
  <div class="row">
    <div class="col-xs-24">
      <div class="ng-scope">
        <div class="ng-scope">
          <div class="ng-scope">
            <div class="content-article spacer-32-top col-lg-16 col-lg-offset-4 col-md-20 col-md-offset-2 ng-scope">
              <div class="ng-isolate-scope">
                <div class="content-article">
                  <div class="ng-scope">
                    <div class="container ng-scope">
                      <div class="row">
                        <div class="col-md-18 col-md-push-6 col-sm-16 col-sm-push-8">
                          <div class="ng-isolate-scope">
                            <div class="ng-scope">
                              <div class="ng-isolate-scope">
                                <section class="section ng-scope"> 
                                
                                <div class="container section-body ng-scope">
                                  <div class="row">
                                    <div class="ng-scope col-xs-24">
                                      <div class="ng-isolate-scope">
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed known issue called out in KB3213986. Users may experience delays while running 3D rendering apps with multiple monitors.
                                          </li>
                                          <li>
                                            Addressed issue in KB3213986 where the Cluster Service may not start automatically on the first reboot after applying the update.
                                          </li>
                                          <li>
                                            Addressed issue where the Active Directory Administrative Center (ADAC) crashes when attempting to modify any attribute of any user account in Active Directory.
                                          </li>
                                          <li>
                                            Addressed issue where the Japanese Input Method Editor is leaking graphics device interface resources, which causes windows to disappear or only partially render after typing approximately 100 sentences.
                                          </li>
                                          <li>
                                            Addressed an issue which improves the reliability of Enable-ClusterS2D PowerShell cmdlet.
                                          </li>
                                          <li>
                                            Addressed an issue where the Virtual Machine Management Service (Vmms.exe) may crash during a live migration of virtual machines.
                                          </li>
                                          <li>
                                            Improved the bandwidth of SSD/NVMe drives available to application workloads during S2D rebuild operations.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed issue where Work Folders clients get duplicate files (sync conflict files) when Work Folders is configured using Group Policy.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed an issue where Remote Desktop Servers crash with a Stop 0x27 in RxSelectAndSwitchPagingFileObject when RDP clients connect and utilize redirected drives, printers, or removable USB drives.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed issue where adjusting the Windows Server Update Services settings using the Group Policy feature causes downloads to fail.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed issue to hard code Microsoft&#8217;s first-party provider registry key values.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed issue that causes the System Preparation (Sysprep) tool to fail.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed issue that causes Office 2016 profile corruption when used with User Experience Virtualization (UE-V) roaming.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed issue that causes the Local Security Authority Subsystem Service to become unresponsive after upgrading the OS.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed issue that causes the Local Security Authority Subsystem Service to fail when a SAP® application uses Transport Layer Security authentication.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed issue where sequencing large registries using the Application Virtualization 5.1 Sequencer results in missing registry keys in the final package.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed issue that fails to retain the sort order of names in a contact list after a device restarts when using the Japanese language.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed issue that causes transactions to fail because of a memory shortage.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed issue that allows files that are forbidden by the security zone setting to be opened in Internet Explorer.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed issue that causes Internet Explorer 11 to fail after installing KB3175443.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed issue that causes applications that use the VBScript engine to fail after applying KB3185319.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed issue that occurs in Internet Explorer when the CSS float style is set to &#8220;center&#8221; in a webpage.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed issue that occurs whenever the multipath IO attempts to log I/O statistics with no paths present.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed issue that causes a 32-bit static route added by a VPN solution to fail, which prevents users from establishing a connection through the VPN.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed issue that may decrease performance by up to 50% when Ethernet adapters that support receive side scaling (RSS) fail to re-enable RSS after a fault or system upgrade.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed issue to allow wildcards in the <strong>Allowed list</strong> field for the Point and Print Restrictions Group Policy.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed issue with multipath I/O failure that can lead to data corruption or application failures.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed issue that can lead to system failure when removing a multipath IO <strong>ID_ENTRY</strong>.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed issue that occurs when a Network Driver Interface Specification function <strong>NdisMFreeSharedMemory()</strong> is not called at the correct Interrupt Request Level.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed issue to utilize the proper service vault for Azure Backup integration.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed issue where SQL server takes 30 minutes to shut down on machines with a lot of RAM (>2TB).
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Addressed additional issues with updated time zone information, Internet Explorer, file server and clustering, wireless networking, Map apps, mobile upgrades for IoT, display rendering, USB 2.0 safe removal, multimedia, Direct3D, Microsoft Edge, enterprise security, Windows Server Update Services, storage networking, Remote Desktop, clustering, Windows Hyper-V, and Credential Guard.
                                          </li>
                                        </ul>
                                        
                                        <ul class="ng-scope">
                                          <li>
                                            Security updates to Microsoft Edge, Internet Explorer, Microsoft Graphics Component, Internet Information Services, Windows SMB Server, Microsoft Windows PDF Library, Windows kernel-mode drivers, Microsoft Uniscribe, the Windows kernel, DirectShow, the Windows OS, and Windows Hyper-V.
                                          </li>
                                        </ul>
                                      </div>
                                    </div>
                                  </div>
                                </div></section>
                              </div>
                            </div>
                          </div>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>