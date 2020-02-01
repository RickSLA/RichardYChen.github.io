---
layout: page
permalink: /projects/
title: Projects
description: a showcase for recent research
---


Understanding and monitoring financial system hinge on reliable and timely measurement of market-wide systemic risk, which is indispensably critical for the central bank and policy makers to maintain a health and stable economy.

[TAQ](https://wrds-www.wharton.upenn.edu/pages/support/data-overview/wrds-overview-taq/){:target="_blank"} provides data recorded at the millisecond level and is rapidly growing approximately 1000 gigabytes per month. The overwhelming amount of high-frequency data unveils a golden opportunity yet significant challenges in measuring risk on a large scale.

<div class="img">
    <a href="/assets/img/data_cum_rets_intraday.jpg" target="_blank">
    <img class="col three left" src="{{ site.baseurl }}/assets/img/data_cum_rets_intraday.jpg" alt="" title="intraday data (click to zoom in)"/>
    </a>
</div>
<div class="col three caption left">
    Intraday transaction data of SP100 constituents on 2019/12/20
</div>

This big-data problem has become an overarching theme in my Ph.D. dissertation. My previous research battled with some conceptual, methodological and theoretical challenges in the study of [microstructure](https://doi.org/10.1016/j.jeconom.2017.05.015){:target="_blank"} and [volatility](https://arxiv.org/abs/1810.04725){:target="_blank"}.

Recently, I spent some time applying my theoretical results on one particular statistical task -- PCA (principal component analysis) -- by implementing a few software packages to march across **7632.91 GBs** of transaction data spanning more than **16 years**. This is the focus of this post.

<div class="img">
    <a href="/assets/img/size_transaction.jpg" target="_blank">
    <img class="col two right" src="{{ site.baseurl }}/assets/img/size_transaction.jpg" alt="" title="data size (click to zoom in)"/>
    </a>
</div>
<div class="col two caption right">
    Cumulative data size of TAQ transaction data since 2003/09/10
</div>


### Why
This foray into database inquiry and big-data computation is stimulated by (1) my data science ideal, (2) plight of some of my fellow graduate students:

* A scientific theory, as Karl Popper emphasized, should in principle be disprovable/falsifiable by experiment or observation. In the same vein, data science needs to be reproducible to be scientific. To promote reproducibility, I hope that my empirical research could be based on open data and open code. To this end, I decided to choose a datebase that is open to academia, and write an assortment of codes that automize all the data query and computation tasks without human intervention, so that my peers and colleagues will be able to reproduce my results pretty conveniently (no more than 48 hours using the [WRDS Cloud](https://wrds-www.wharton.upenn.edu/pages/support/getting-started/3-ways-use-wrds/#the-wrds-cloud){:target="_blank"} cluster). 

* Another reason is relatively personal. It took my friend 3 months to download, clean, organize some large datasets from TAQ before the statistical analysis. It is a heartbreaking story of a back-breaking job. Not only consuming time, it costs money too! Another friend working with Datastream had to purchase a new expensive desktop last September since the previous one lacks sufficient RAM to read in data from hard drive. 

In the light of these, what can I contribute? It would be wonderful if we have a data pipeline that
1. inquires data by a few scripts;
2. cleans data on the server;
3. computes statistics on the fly;
4. saves final results and reveals the glory;
5. ~~allows you to lie on a sunny beach musing over what's your next paper while it is running data for you, instead of~~ toiling, sweating, bleeding through your current project.

If these are attainable, not only can results be easily reproduced, but also downloading and hand-cleaning become unnecessary.

Kill two birds with one stone! Can we?

*Yes we can.* (not a political slogan :sweat:)

For example, below is a plot (click to zoom in) produced by my automatic data pipeline after it scanned through 7632.91 GB data on WRDS server.

<div class="img">
    <a href="/assets/img/pca_eigenvalue_week_bc.jpg" target="_blank">
    <img class="col three" src="{{ site.baseurl }}/assets/img/pca_eigenvalue_week_bc.jpg" alt="" title="eigenvalues (click to zoom in)"/>
    </a>
</div>
<div class="col three caption">
    Proportions of cross-sectional variations explained by principal components, 2003/09/10-2019/12/31
</div>

Based on Corollary 1 of [my paper](/assets/pdf/Chen18_preaveraging_func_vol.pdf){:target="_blank"}, we can quantify the statistical uncertainty and compute confidence intervals.

<div class="img">
    <a href="/assets/img/pca_eigenvalue_week_ci.jpg" target="_blank">
    <img class="col three" src="{{ site.baseurl }}/assets/img/pca_eigenvalue_week_ci.jpg" alt="" title="uncertainty quantification (click to zoom in)"/>
    </a>
</div>
<div class="col three caption">
    Uncertainty quantification and statisitcal inference based on <a href="https://arxiv.org/abs/1810.04725" target="_blank">my research</a>, 2003/09/10-2019/12/31
</div>


### How :interrobang:
There are several softwares that work to our computational advantage.

[PostgreSQL](https://www.postgresql.org/){:target="_blank"} is a open-source database management system adapted by WRDS. It greatly facilitates our data query. It has been configured by WRDS and we do not need to work out its backend ourselves.

[wrds](https://pypi.org/project/wrds/){:target="_blank"} is a free module written by WRDS. It establishes connection to the PostgreSQL server and seamlessly access data in the format of pandas [DataFrame](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html){:target="_blank"}. To submit customized query requests, only elementary SQL syntax is needed.

[pandas](https://pandas.pydata.org/){:target="_blank"} is a fantastic (and free) data science library built on top of [numpy](https://numpy.org/){:target="_blank"}. pandas offers expressive data structures and make it pretty easy to work with tabular and time series data.

However, the WRDS engineering team still faces a substantial task of maintaining the humongous and increasing database. [SQLAlchemy](https://www.sqlalchemy.org/){:target="_blank"} sometimes raises an operational error `canceling statement due to conflict with recovery` when data query (the most time-cosuming part of this pipeline with SQL machinery behind the scene) runs for more than 6 hours. Besides, the database server occasionally experiences a communication glitch with the LDAP server (for authentication), which prohibits the connection to be reestablished.

Due to these constraints, the pipeline is only partially automatic and needs human care every a few hours. Rather than looping over all the years at once, we currently need to submit batch jobs to scan subsets of data which can finish in less than, for example, 6 hours. If you decide to leave for the beach, please bring a ssh-capable device with you.

More to come, data and pipeline. Mark my words~

<br/><br/>
