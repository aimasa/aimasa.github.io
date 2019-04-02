---
title: hexo_next图片无法加载问题
copyright: true
date: 2019-03-05 19:40:52
categories:
- hexo错误笔记
- hexo_next图片无法加载问题
tags:
- hexo err类型
---

错误描述：自从加入了可以插入公式的插件后，经常碰到图片无法显示的问题，去查看页面代码，发现
    
    ！[]()

无法被hexo g解析生成图片链接

<!--more-->

用谷歌不管怎么搜索关键词都没有用，所以我直接删掉了".deploy_git"，然后再用
    
    hexo d -g

重新生成静态页面并且部署在网站上，然后图片成功生成了。

但是有时候这个办法还是会失效，然后我参考了[这篇博客的做法](https://850552586.github.io/2018/11/15/hexo%E5%BC%95%E7%94%A8%E6%9C%AC%E5%9C%B0%E5%9B%BE%E7%89%87%E6%97%A0%E6%B3%95%E6%98%BE%E7%A4%BA/)替换掉了hexo-asset-image下载后生成的index.js这个文件（我把它的代码复制到这）
    
    'use strict';
    var cheerio = require('cheerio');

    // http://stackoverflow.com/questions/14480345/how-to-get-the-nth-occurrence-in-a-string
    function getPosition(str, m, i) {
      return str.split(m, i).join(m).length;
    }

    var version = String(hexo.version).split('.');
    hexo.extend.filter.register('after_post_render', function(data){
      var config = hexo.config;
      if(config.post_asset_folder){
        	var link = data.permalink;
    	if(version.length > 0 && Number(version[0]) == 3)
    	   var beginPos = getPosition(link, '/', 1) + 1;
    	else
    	   var beginPos = getPosition(link, '/', 3) + 1;
    	// In hexo 3.1.1, the permalink of "about" page is like ".../about/index.html".
    	var endPos = link.lastIndexOf('/') + 1;
        link = link.substring(beginPos, endPos);

        var toprocess = ['excerpt', 'more', 'content'];
        for(var i = 0; i < toprocess.length; i++){
          var key = toprocess[i];
 
          var $ = cheerio.load(data[key], {
            ignoreWhitespace: false,
            xmlMode: false,
            lowerCaseTags: false,
            decodeEntities: false
          });

          $('img').each(function(){
    		if ($(this).attr('src')){
    			// For windows style path, we replace '\' to '/'.
    			var src = $(this).attr('src').replace('\\', '/');
	    		if(!/http[s]*.*|\/\/.*/.test(src) &&
	    		   !/^\s*\//.test(src)) {
	    		  // For "about" page, the first part of "src" can't be removed.
	    		  // In addition, to support multi-level local directory.
	    		  var linkArray = link.split('/').filter(function(elem){
	    			return elem != '';
	    		  });
	    		  var srcArray = src.split('/').filter(function(elem){
	    			return elem != '' && elem != '.';
	    		  });
	        		  if(srcArray.length > 1)
	    			srcArray.shift();
	    		  src = srcArray.join('/');
	    		  $(this).attr('src', config.root + link + src);
		    	  console.info&&console.info("update link as:-->"+config.root + link + src);
		    	}
	    	}else{
	    		console.info&&console.info("no src attr, skipped...");
	    		console.info&&console.info($(this));
	    	}
          });
          data[key] = $.html();
        }
      }
    });

然后再去删掉".deploy_git"，用
    
    hexo d -g

重新生成静态页面并且部署在网站上，然后图片成功生成了。