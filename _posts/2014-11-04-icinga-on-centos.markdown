---
layout: post
title: Setting up Icinga 1.x on CentOS 6
---
For one of my recent contracts we were using [Nagios XI](http://www.nagios.com/products/nagiosxi) for monitoring. This is, in theory, a conveniently packaged Nagios installation, with a configuration UI and a good selection of plugins and support. However, given we were using Git to manage our configuration, it was somewhat wasted. And once our support expired and it turned out our licence was locked to a single MAC address we decided it was probably time to find an alternative.

A bit of research turned up [Icinga](https://www.icinga.org). While Icinga 2 is an entirely new product, Icinga is based upon Nagios Core. It adds a fairly decent web UI and REST interfaces, both of which were clear gains over our existing solution.

Where it fell down, however, was installation. The documentation was spotty, and I ended up piecing it together from forums and other posts. In the end I came up with some notes to simplify future builds, and for the sake of prosperity, here they are!

This is based on Icinga 1.11.x, on CentOS 6.5, and were accurate as of May 2014. After that, your milage may vary.

<script src="https://gist.github.com/jshiell/9934929.js"></script>

