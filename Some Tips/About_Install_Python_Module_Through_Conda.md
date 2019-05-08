使用conda安装包的时候遇到这个情况：<br />
>
```
Anaconda安装包时，遇到PackageNotFoundError： ''Package missing in current channels"
```
>
该教程来源于:
https://blog.csdn.net/Erice_s/article/details/80156191<br />
>
解决方法如下:
```
anaconda search -t conda wordcloud
```
(wordcloud是要安装的包的名称)<br />
```
(python36) C:\Users\zbb>anaconda search -t conda wordcloud
Using Anaconda API: https://api.anaconda.org
Packages:
     Name                      |  Version | Package Types   | Platforms       | Builds
     ------------------------- |   ------ | --------------- | --------------- | ----------
     bushmanlab/r-pubmedwordcloud |    0.3.3 | conda           | linux-64        | r3.3.2_0
     bushmanlab/r-wordcloud    |      2.5 | conda           | linux-64        | r3.3.2_0
     conda-forge/r-wordcloud   |      2.5 | conda           | linux-64, win-64, osx-64 | r3.3.2_0, r3.4.1_0
                                          : Pretty word clouds.
     conda-forge/wordcloud     |    1.4.1 | conda           | linux-64, win-32, win-64, osx-64 | py36_0, py34_0, py35_0, py27_0
                                          : A little word cloud generator in Python
     esgirones/r-wordcloud     |      2.5 | conda           | linux-64        | r3h064c28a_0
                                          : Pretty word clouds.
     pjones/r-wordcloud        |      2.5 | conda           | linux-64        | r3.3.1_0
     sibirbil/wordcloud        |    1.1.3 | conda           | osx-64          | py27_0
                                          : A little word cloud generator
     t3kcit/wordcloud          |    1.2.1 | conda           | linux-64        | py35_0
Found 8 packages

Run 'anaconda show <USER/PACKAGE>' to get installation details
```
然后使用以下指令来install这个包
```
$ conda install –channel https://conda.anaconda.org/conda-forge wordcloud
```
