========================================
Scrapyjs - Scrapy+Javascript integration
========================================

这个库以两种不同机制提供Scrapy-Javascript集成：

- a Scrapy download handler 
- a Scrapy downloader middlware

你只需要使用其中的一种，而不是两种同时使用。

Requirements
============

- python-gtk2
- python-webkit
- python-jswebkit

使用
=====

不管你是使用download handler还是downloader middleware，使用方法都是相同的：
那些在request.meta中含有"renderjs" key的requests会通过webkit下载（javascript 渲染），
而不再通过Scrapy的downloader下载。

Download handler 设置
======================

To enable the download handler add the following to your settings::

    DOWNLOAD_HANDLERS = {
        'http': 'scrapyjs.dhandler.WebkitDownloadHandler',
        'https': 'scrapyjs.dhandler.WebkitDownloadHandler',
    }

And make sure Scrapy uses the gtk2 reactor on twisted, for example by adding
the following lines to scrapy/__init__.py::

    from twisted.internet import gtk2reactor
    gtk2reactor.install()

Handler pros:

- it's asynchronous, and will merge with the main twisted reactor thread

Handler cons:

- requires patching scrapy (to use gtk2 reactor), but this could be fixed by
making Scrapy reactor type configurable.


Downloader middlware 设置
==========================

在settings.py中添加下面的内容使能downloader middleware ::

    DOWNLOADER_MIDDLEWARES = {
        'scrapyjs.middleware.WebkitDownloader': 1,
    }

Middleware pros:

- does not require patching scrapy (gtk reactor)

Middleware cons:

- blocking API (it spawns a gtk loop for each request)


TODO
====

- return WebView in response.meta, to support interaction and running custom
  javscript code from the spider
- support custom request headers (User-Agent, Accept, etc)
