---
title: 'stdout引发的curl 302跳转 crash'
layout: post
tags:
    - 302
    - curl
    - stdout
---

# 重现步骤：

* 0、开发环境：Windows 7 SP1 64bit, VS2008 SP1
* 1、进程中修改了stdout这个句柄的值：调用了prinft和cout都会修改stdout，TRACE不会修改stdout
* 2、使用curl请求一个会跳转的url（HTTP 跳转的话curl会收到包含302的response header）
* 3、crash

# 问题影响

* curl版本7.21.7和7.28.1都有这个问题

# 问题原因和解决方案

很可能是curl的一个bug，目前没有找到完美的解决方案，只能在进程中不要使用printf和cout

# 代码

如果你调用了printf或者cout，你会发现在curl初始化的时候，调用了这个代码，这个时候的stdout已经被修改了


    // code block from url.c
    /*
     * Initialize the UserDefined fields within a SessionHandle.
     * This may be safely called on a new or existing SessionHandle.
     */
    CURLcode Curl_init_userdefined(struct UserDefined *set)
    {
      CURLcode res = CURLE_OK;
    
      set->out = stdout; /* default output to stdout */ // linyehui:这个set.out有问题
      set->in  = stdin;  /* default input from stdin */
      set->err  = stderr;  /* default stderr to stderr */


实际crash的地方：


    // code block from sendf.c
    
    /* Curl_client_write() sends data to the write callback(s)
    
       The bit pattern defines to what "streams" to write to. Body and/or header.
       The defines are in sendf.h of course.
    
       If CURL_DO_LINEEND_CONV is enabled, data is converted IN PLACE to the
       local character encoding.  This is a problem and should be changed in
       the future to leave the original data alone.
     */
    CURLcode Curl_client_write(struct connectdata *conn,
                               int type,
                               char *ptr,
                               size_t len)
    {
        // ...  
        /* If the previous block of data ended with CR and this block of data is
           just a NL, then the length might be zero */
        if(len) {
          wrote = data->set.fwrite_func(ptr, 1, len, data->set.out); // linyehui:这个set.out有问题
        }
        else {
          wrote = len;
        }
    
    // ...


呵呵……