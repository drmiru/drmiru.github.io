---
id: 4895
title: Starting new Cross-Platform DevOps Blog Post Series
date: 2017-10-20T21:07:36+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=4895
permalink: /starting-new-cross-platform-devops-blog-post-series/
bfa_virtual_template:
  - hierarchy
categories:
  - Azure
  - Cross Platform
  - DevOps
---
With this post I&#8217;m kicking off a new series of blog post covering cross platform and DevOps topics for IT Pros.

## Why Cross-Platform?

Back in the days Microsoft was focused on it&#8217;s own operating system flagship, called Windows. Remember..anyone? 🙂

<a class="irc_mil i3597 iV9GWz5RafsM-zixyDjKkw5M" tabindex="0" href="https://www.google.ch/url?sa=i&rct=j&q=&esrc=s&source=images&cd=&cad=rja&uact=8&ved=0ahUKEwj3rLfv8P_WAhUFsBQKHexsBK0QjRwIBw&url=https%3A%2F%2Fwww.pcworld.com%2Farticle%2F253287%2Fwindows%2Fwindows-3-1-twenty-years-later.html&psig=AOvVaw02uRHrDHhlRH1V7Vw3bftm&ust=1508612282250743" target="_blank" rel="noopener noreferrer" data-ved="0ahUKEwj3rLfv8P_WAhUFsBQKHexsBK0QjRwIBw" data-noload="" data-cthref="/url?sa=i&rct=j&q=&esrc=s&source=images&cd=&cad=rja&uact=8&ved=0ahUKEwj3rLfv8P_WAhUFsBQKHexsBK0QjRwIBw&url=https%3A%2F%2Fwww.pcworld.com%2Farticle%2F253287%2Fwindows%2Fwindows-3-1-twenty-years-later.html&psig=AOvVaw02uRHrDHhlRH1V7Vw3bftm&ust=1508612282250743" data-ctbtn="2"><img class="irc_mi alignleft" src="https://images.pcworld.com/images/article/2012/04/windows31_01-11344006.jpg" alt="Image result for windows 3.1" width="200" height="133" /></a>[<img class="rg_ic rg_i aligncenter" src="data:image/jpeg;base64,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" alt="Image result for new azure logo" width="111" height="93" name="jNT01EeoQAl-oM:" data-sz="f" />](https://www.google.ch/imgres?imgurl=https%3A%2F%2Fwww.onmsft.com%2Fwp-content%2Fuploads%2F2017%2F09%2FAzure-310x260.png&imgrefurl=https%3A%2F%2Fwww.onmsft.com%2Fnews%2Fignite-2017-microsoft-azure-gets-new-logo-tagline&docid=IVNzRl-WdX55_M&tbnid=jNT01EeoQAl-oM%3A&vet=10ahUKEwjNx4nQ8f_WAhXECMAKHeRFC_oQMwgsKAUwBQ..i&w=310&h=260&bih=768&biw=1304&q=new%20azure%20logo&ved=0ahUKEwjNx4nQ8f_WAhXECMAKHeRFC_oQMwgsKAUwBQ&iact=mrc&uact=8){.rg_l}

<div id="main">
  <div id="cnt" class="big">
    <div id="rcnt">
      <div class="col">
        <div id="center_col">
          <div id="res" class="med">
            <div id="search" data-jiis="uc">
              <div data-ved="0ahUKEwiKibuA8v_WAhVKBsAKHXeXA2QQGggm">
                <div id="ires" data-async-context="query:microsoft%20loves%20linux">
                  <div id="rso">
                    <div id="isr_mc">
                      <div id="irc_bg" class="irc_land irc_bg">
                        <div id="_YTc">
                          <div id="irc_cc">
                            <div class="irc_c i8187 immersive-container" data-ved="0ahUKEwj4lq2I8v_WAhUGwxQKHYl3DaoQ-z8IEg" data-item-id="nuxcbd1ZxRzbxM:" data-hveid="18">
                              <div class="irc_t i30052" data-ved="0ahUKEwj4lq2I8v_WAhUGwxQKHYl3DaoQ5OoBCBM" data-hveid="19" data-noload="">
                                <div class="irc_mic r-iE9ZhN2qfWIY">
                                  <div class="irc_mimg irc_hic iE9ZhN2qfWIY-lvVgf-rIiHk">
                                    <a class="irc_mil i3597 iE9ZhN2qfWIY-zixyDjKkw5M" tabindex="0" href="https://www.google.ch/url?sa=i&rct=j&q=&esrc=s&source=images&cd=&cad=rja&uact=8&ved=0ahUKEwj4lq2I8v_WAhUGwxQKHYl3DaoQjRwIBw&url=http%3A%2F%2Fwww.zdnet.de%2F88283015%2Fmicrosoft-tritt-der-linux-foundation-bei%2F&psig=AOvVaw1TiuGUVt6Mm18r1uoNCMPL&ust=1508612596833795" target="_blank" rel="noopener noreferrer" data-ved="0ahUKEwj4lq2I8v_WAhUGwxQKHYl3DaoQjRwIBw" data-noload="" data-cthref="/url?sa=i&rct=j&q=&esrc=s&source=images&cd=&cad=rja&uact=8&ved=0ahUKEwj4lq2I8v_WAhUGwxQKHYl3DaoQjRwIBw&url=http%3A%2F%2Fwww.zdnet.de%2F88283015%2Fmicrosoft-tritt-der-linux-foundation-bei%2F&psig=AOvVaw1TiuGUVt6Mm18r1uoNCMPL&ust=1508612596833795" data-ctbtn="2"><img class="irc_mi alignright" src="http://www.zdnet.de/wp-content/uploads/2016/11/microsoft-loves-linux.png" alt="Image result for microsoft loves linux" width="179" height="100" /></a>
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
  </div>
</div>

Nowadays the boundaries between Microsoft and the OpenSource world are disappearing more and more. To prove this a little bit I&#8217;ve collected some facts:

  * In 2014, Microsoft open sourced .Net
  * In 2016, Satja Nadella stated, that Microsoft loves Linux
  * In 2017, Microsoft announced Linux Containers on Windows
  * 60% of all Azure Marketplace offerings are Linux-based
  * Last months: 50% of all new VMs are running Linux
  * Microsoft offers Linux certifications and MVP awards for Linux

## Why DevOps?

DevOps is not a tool, it&#8217;s nor a software or a specific technology. DevOps is a process driven software engineering practice framework. It&#8217;s primary goal is to tighten the relationship between developers, engineers and operators by implementing agile and continous development/deployment processes. DevOps aims at shorter development cycles and increased deployment frequency. Not only software can be developed using this methodology, also infrastructure can be configured and deployed using code only.

## Why mentioning IT Pros?

<img class="alignnone" src="https://www.familie-und-tipps.de/Kinder/Erziehung/Bild/Beleidigt-b4.jpg" alt="" width="224" height="149" /> 

(Disclaimer: The following statements, are reflecting my personal opinion, and may not apply to everyone reading this post 🙂

  * IT Pros are a generally focused on getting things to just work, mainly on infrastructure level
  * IT Pros don&#8217;t care about code
  * IT Pros are used to do things once, often with GUI based admin- and config tools
  * If they have to repeat their work, tons of print-screen based documentation is consulted (if present though :-))
  * IT Pros have some batch-script or vb-script know how from the past, now trying to arrange themselves with PowerShell just because everyone tells them to do so
  * IT Pros believe, automation just being a hype and a cost driver, being too complex and not applicable to infrastructure

The truth is, that automation is a key success factor for user self service, large environments, distributed environments, applications based on micro services and containers, etc. Don&#8217;t get me wrong here. Not every IT Pro will become a professional automation engineer, nor will a developer typically become a infrastructure architect or network engineer. The thing is, that all of us have to open their minds for change, and things will change, faster than we might like it.

With that said, I&#8217;m trying to create a series of blog posts, which targets IT Pros willing to change, to learn and to be open minded for future trends and technologies.

The topics for the first series of posts will be:

#1 Using Posh-SSH to automate Linux with PowerShell

#2 PowerShell Desired State Configuration (from zero to hero in 60 minutes)

#3 Manage Linux with PowerShell DSC and PowerShell Core

#4 Leveraging Azure Automation / OMS to follow &#8220;infrastructure as code principle&#8221;

#5 Version Control / Continuous Release Pipeline (CI/CD)

Stay tuned 🙂

&nbsp;