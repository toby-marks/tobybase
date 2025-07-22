+++
draft       = false
date        = "2013-06-14T21:47:00+00:00"
lastmod     = "2017-02-22"
title       = "How To Reset Page Numbering Between Document Sections in BI Publisher's Template Builder for Word"
categories  = ["Reporting"]
tags        = ["Oracle", "BI Publisher", "Microsoft Word"]
+++
Under normal circumstances, it is fairly easy in Microsoft Word to [reset page numbering](http://www.youtube.com/watch?v=E1WtKR6FDM4) when starting a new section of your document. However, such a simple thing can be non-trivial when dealing with the BI Publisher Template Builder plugin for Microsoft Office ([available from Oracle](http://www.oracle.com/technetwork/middleware/bi-publisher/downloads/index.html) — see BI Publisher Desktop). 

In short, doing this the normal way did not work for me, and in researching the issue I ran across a [number](http://stackoverflow.com/questions/9603481/rtf-number-of-pages-page-x-of-y) [of](https://forums.oracle.com/thread/2493003) [folks](https://forums.oracle.com/thread/496869) who were having the same problem. Page numbering was simply not being reset between sections, or worse, did not take into account a fluid document size based upon available data. 

![](/images/2013-06-14-resetting-page-numbering-between-document-sections-in-bi-publishers-template-builder-for-word/9f704cdbd7a5ec85bff722e3469edae0993faa219b4e42459782b94f692aa53e.png)

One [suggestion I read](https://forums.oracle.com/thread/2225641) said to wrap the page number cells in a BI Publisher repeating group using for-each@section, like so:

`<?for-each@section:ID?> x/y <?end for-each?>`

But this also didn't work for me. Finally I had to edit the RTF file directly.

The [RTF specification](http://www.biblioscape.com/rtf15_spec.htm) was of some help to me in doing this, as well as [this explanatory page from Microsoft](http://msdn.microsoft.com/en-us/library/office/aa140283(v=office.10).aspx#rtfspec_secformatprop).

RTF files can be a little confusing, but most of the "junk" in the files is just special codes for formatting the text. I was able to find my way around easily enough by searching for labels or keywords from the document. By searching for the first few words from the second section of the document (in this case, "Sender's Receipt"), I was able to spot where the section break should go.

![](/images/2013-06-14-resetting-page-numbering-between-document-sections-in-bi-publishers-template-builder-for-word/0d902604df4939682bb7821c4044affb3b03f809274551b8172827bc846d7b2d.png)

Checking the RTF spec, I could see that the `\page` directive shown above was the page break between two sections of my document where I wanted to reset the page numbering. To create the section break, I inserted the following RTF directives at that point:

`\sect \sectd \pgnstarts1\pgnrestart `

![](/images/2013-06-14-resetting-page-numbering-between-document-sections-in-bi-publishers-template-builder-for-word/466447c609e7fa65f188357e7531def7cb8f1443944e602f62e3eff984f1388d.png)

Since the default behavior of the section break is to start on a new page, I found I had to remove the `\page` directive to avoid an extra page being inserted between the document sections.

Interestingly, resetting the page numbering also works when calculating total number of pages, whereby the total is from the section, not the entire document, even though the NumPages field is used instead of SectionPages! Go figure, but it worked for me.

![](/images/2013-06-14-resetting-page-numbering-between-document-sections-in-bi-publishers-template-builder-for-word/1efc8813e70f4ee7bda4647b6fd38f4dce646c46b41e20fb482a3443e5bbb17a.png)