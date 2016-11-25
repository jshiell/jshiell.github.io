---
layout: post
title: Saving Yourself from Amazon
---
Living in a small London flat, I've developed a strong aversion to physical clutter. As such, I'm very fond of e-books. And, like the majority of lazy people, I tend to buy most (although not all) of them from Amazon. And here we run into a problem: Amazon is, if not evil, certainly not on our side. They've taken books from accounts before, and no doubt will again. As such, I consider it healthy to maintain backups.

This turns out to be fairly easy to do, thanks to [Calibre](http://calibre-ebook.com). This is an open source e-book manager, and on top of being a great way to track your various e-books is the ability to strip Amazon's DRM. 

To do this:

* Install the [Kindle application for Mac](http://www.amazon.com/gp/kindle/mac/download).
* Open it, then log in using your Amazon account.
* Download the books you wish to back up.
* They'll be placed in *~/Library/Containers/com.amazon.Kindle/Data/Library/Application Support/Kindle/My Kindle/Content*.

While it's easy enough to copy everything on the first download, I tend to find all files modified in the last hour so to only backup my new downloads.
```
cd ~/Library/Containers/com.amazon.Kindle/Data/Library/Application\ Support/Kindle/My\ Kindle\ Content
find . -cmin -60 -name '*.az*' -exec cp \{\} ~/Desktop \;
```
This will copy the most recent files to your desktop.

Now, open Calibre, and drag the *azw* files from your dekstop into the Calibre window. They'll be imported, and cleaned of DRM during the process.

As a final touch, if you move the Calibre library directory to the cloud provider of your choice (e.g. iCloud, Dropbox) you end up with a cloud backup, and can then access these e-books on all your computers.