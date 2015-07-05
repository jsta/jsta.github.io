---
layout: post
title: GIS "in the cloud" with R
---
After running across [this tutorial](http://deanattali.com/2015/05/09/setup-rstudio-shiny-server-digital-ocean/) by [Dean Attali](https://twitter.com/daattali), I attempted to setup my own R GIS platform "in the cloud". Dean's tutorial was excellent for the most part. I was able to spin up my own server with RStudio installed. The two difficulties that I ran into dealt with [gdal](www.gdal.org) and ssh. Specifically, it was hard to track down the Ubuntu system-level dependencies for [rgdal](http://cran.r-project.org/package=rgdal). The solution ultimately was to install the `gdal-bin` and `libgdal-dev` packages using `apt-get`.

Once your system level dependencies are set, you can use `rsync` to transfer datasets and R packages. A command like the following can be used to transfer the contents of your `Data` folder to your remote server.

        rsync -av /home/<user_name>/Data/ <user_name>@<ip_address>:~/Data

I encourage people to try this solution if you find yourself running long computations on your local machine. If you sign up at Digital Ocean using my [referer link](https://www.digitalocean.com/?refcode=cf7a61ffdec0), you will recieve $10 in starter money. This is esentially a free trial!

