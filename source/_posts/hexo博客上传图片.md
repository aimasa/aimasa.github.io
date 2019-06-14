---
title: hexo博客上传图片
copyright: true
date: 2019-03-29 19:15:36
categories:
- hexo更新配置
- hexo插入图片
tags:
- hexo
---

因为之前用插件在本地导入图片到博客上这个方法因为别的缘故，不能很稳定的让图片插入进去……
这就要从我开始装数学公式的插件说起了，反正我不想再折腾了，听说本地导入图片的方法也会导致项目过大然后会受github项目仓库的限制，所以我干脆卸载了本地导入图片的这个插件，换成了用七牛云去在线导入图片。当然这个过程不太顺畅。特此记录一下。

<!--more-->

# 准备工作

去七牛云注册了一个账号（前不久他们特地打电话过来关怀一下注册了账号就不见人影的我，我还嘚瑟的和他们说我不用你们的产品，真香）

然后百度教程

# 开始

下载插件

    npm install hexo-qiniu-sync --save

然后去！！！！hexo下面的_config.yml里面加上qiniu的配置！！！！是hexo下面不是next下面

    #plugins:
      #- hexo-qiniu-sync（这部分要删掉的，不然会报错）

    qiniu:
      offline: false
      sync: true
      bucket: bucket_name # 你在七牛上面设置的存储图片的存储空间的名字
      access_key: AccessKey # 你在七牛上面账号的密钥管理的key
      secret_key: SecretKey # 你在七牛上面账号的密钥管理的key
      dirPrefix: static #自动在你七牛的那个存储空间里面新建一个这个文件夹，听说是加个文件夹比较好
      urlPrefix: http://7xqb0u.com1.z0.glb.clouddn.com/static  
      local_dir: xxx # 当你hexo qiniu s时候，这个文件夹的东西会自动上传到你七牛云里头创建的static文件夹里面。
      update_exist: true
      image:
        folder: images
        extend:
      js:
        folder: js
      css:
        folder: css

好了，其他详细的说明下面有指路辽==

# 插入图片

我用的是
    
    {% qnimg xxx.png %}

这种语法，其他的我不想看辽，目前也用不到。

# 上传图片到七牛云

先去博客本地生成的存图的文件夹（上面有注解），然后博客文里面对它进行引用。然后上传图片去七牛云本地时候，就用：

    hexo qiniu s

这个语法即可。

# 参考资料

[我觉得很详细的教程](https://lwtang.github.io/2018/11/27/qiniu-store-image/)