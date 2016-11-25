---
layout: post
title: Fixing Santander UK
---
I recently opened a new business account with Santander UK, and was generally finding the experience not too terrible until I decided to export my transaction history.

Somewhat surprisingly, not only do they not offer CSV export, but when you select XLS what you in fact receive is a rather dodgy HTML file, containing your data in a terrible HTML table. Whoops.

Even worse, this [problem has been going on a while...](http://forums.contractoruk.com/accounting-legal/79481-santander-csv-download.html)

Since Santander didn't seem interested in resolving this, I took the obvious next step and bitched about the issue on Twitter. Once done, I then wrote a script to fix it. Enjoy.

<script src="https://gist.github.com/jshiell/70490674326cb97b44e8.js"></script>
