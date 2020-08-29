---
layout: post
title:  "Intel Parallel Studio XE 更新 License 方法"
date:   2020-03-28 15:04:00 +0800
categories: Linux 
tags: Linux 
author: GliderHX
mathjax: true
---
* content
{:toc}





## License到期后报错


最近组里用的Intel Parallel Studio到期了，编译程序时
```
mpiifort -c hello_world.f90 
```
显示如下错误
```
Error: Product support for your (Comp-FL) license has expired.
License file(s) used were (in this order):
**  1.  /opt/intel/compilers_and_libraries_2018.3.222/linux/licenses
**  2.  /opt/intel/licenses/NCOM_L___S4*D-*******.lic
**  3.  /opt/intel/licenses/l_*******.lic
**  4.  /home/uername/intel/licenses
**  5.  /opt/intel_2019/compilers_and_libraries_2019.4.243/linux/licenses
**  6.  /home/opt/intel_2019/compilers_and_libraries_2019.4.243/linux/bin/intel64/../../Licenses

Please refer https://software.intel.com/en-us/faq/purchasing-renewing-upgrading#support-expiration  for more information..

ifort: error #10052: could not checkout FLEXlm license
```





##  查看License历史记录
由于使用的是学生教育用户申请的License，所以有效期只有一年。登录intel官网账号，在`仪表盘-我的内容`里可以查看申请过的的License历史记录以及到期时间。
![](/uploads/2020-03/intel_dashborad.png)


##  更新License

首先跟之前一样，在[intel官网产品页面](https://software.intel.com/en-us/parallel-studio-xe/choose-download/student-linux-fortran)用学生身份注册一个新的产品。然后在`仪表盘-我的内容`马上就可以看到新的License，点击`管理许可`
![](/uploads/2020-03/manage.jpg)
然后下载`*******.lic`命名的License文件
![](/uploads/2020-03/download-license.jpg)<br>
并放置于`/opt/intel/licenses`文件夹内，License更新完成。


##  参考资料
[Updating the License File after Renewal](https://software.intel.com/en-us/articles/updating-the-license-file-after-renewal)


