---
title: hitszthesis
date: 2025-05-26 11:07:03
tags:scholar
---



  

提供的本科毕设word模板具有非常多的格式毛病，全部按着写容易被痛批“从头错到尾”。而且由于office祖传的单核优化，图文太长会导致卡顿，MathML对长公式也很不友好。遂改用网传的LaTeX模板（

[hitszthesis]: https://github.com/YangLaTeX/hitszthesis	"hitszthesis"

），包含从本科到博士毕业论文所需的样式，基本上也符合要求。

即便如此，对于规格严格来说，hitszthesis也还有一点小瑕疵。在写作过程中被狠狠攻打了（笑）  

根据身边统计学收集到的问题，花大力气修改了hitszthesis的格式，基本上能够糊弄过去。已经发现的问题有：

-   行间距

    根据泥工规定，正文字号小四行距1.25倍，在hitszthesis.cls中是这样实现的：

    ```tex
    \renewcommand\normalsize{%
      \@setfontsize\normalsize{12bp}{19.27374bp \@plus 2bp \@minus 2bp}%
      \abovedisplayskip=8pt
      \abovedisplayshortskip=8pt
      \belowdisplayskip=\abovedisplayskip
      \belowdisplayshortskip=\abovedisplayshortskip}
    ```

    ——然后就被一眼盯帧盯出来了行间距不对。

    经过考证，字号是12bp（小四）没错，行距19.27374bp也是根据LaTeX的方式计算的（1.2*1.3），\@plus 2bp \@minus 2bp是灵活增加/减小行距的参数。问题就出在这个“灵活”上，删去即可。

-   摘要页

    摘要环境是这样实现的：

    ```tex
    \newcommand{\hitsz@makeabstract}{%
      \ifhitsz@openright\cleardoublepage\else\clearpage\fi
      \hitsz@appendix@chapter*{\cabstractcname}[\cabstractename]
      \pagestyle{hitsz@headings}
      \pagenumbering{Roman}
      \hitsz@cabstract
      \vskip12bp
      \hitsz@put@keywords{\heiti\hitsz@ckeywords@title}{\hitsz@ckeywords}
      \ifhitsz@openright\cleardoublepage\else\clearpage\fi
      \hitsz@appendix@chapter*{\eabstractcname}[\eabstractename]
      \hitsz@eabstract
      \vskip12bp
      \hitsz@put@keywords{\textbf{Keywords:\enskip}}{\hitsz@ekeywords}}
    ```

    效果如下：

    ![image-20250526113939672](D:\MoS2\hexo\source\fig\image-20250526113939672.png)

    根据模板，Abstract的标题是**不加粗**的。关键词太多太长时，还会出现换行缩进的问题。  

    ![image-20250526114430505](D:\MoS2\hexo\source\fig\image-20250526114430505.png)

    我们首先来看标题的定义：

    ```tex
    \newcommand\hitsz@chapter@titleformat[1]{%开启悬挂缩进选项
        \ifthenelse%
          {\equal{#1}{\eabstractcname}}%
          {\bfseries #1}%
          %实现章标题的居中加悬挂缩进，注意，此处一定是\CTEX@chaptername\CTEX@chapter@aftername, 否则是英文标题长度
          {\ifhitsz@chapterhang%
            \settowidth{\hangindent}{\CTEX@chaptername\CTEX@chapter@aftername}\hangafter=1
            \fi#1}%
          %{\begin{varwidth}[t]{\hitsz@chapter@indentboxwidth}#1\end{varwidth}}
    }
    ```

    我们看到针对\eabstractcname写了一个if条件判断，这正好就是英文标题页的**Abstract**，直接把\bfseries删掉就可以了。

    针对关键词的问题，没有什么好办法，直接注释掉，再在.\front\coverinformation.tex中重写：

    ```tex
    \newcommand{\hitsz@makeabstract}{%
      \ifhitsz@openright\cleardoublepage\else\clearpage\fi
      \hitsz@appendix@chapter*{\cabstractcname}[\cabstractename]
      \pagestyle{hitsz@headings}
      \pagenumbering{Roman}
      \hitsz@cabstract
      \vskip12bp
      \hitsz@put@keywords{\heiti\hitsz@ckeywords@title}{\hitsz@ckeywords}
      \ifhitsz@openright\cleardoublepage\else\clearpage\fi
      \hitsz@appendix@chapter*{\eabstractcname}[\eabstractename]
      \hitsz@eabstract
      %\vskip12bp
      %\hitsz@put@keywords{\textbf{Keywords:\enskip}}{\hitsz@ekeywords}
      }
    ```

    ```Tex
    \vskip12bp
    
    \begin{flushleft}
    \textbf{Keywords: }fiber optic sensor, surface plasmon resonance, D-shaped fiber, tilted fiber Bragg grating, biosensing
    \end{flushleft}
    ```

    ![image-20250526115325631](D:\MoS2\hexo\source\fig\image-20250526115325631.png)

    ![image-20250526115257996](D:\MoS2\hexo\source\fig\image-20250526115257996.png)

    这就符合要求了。

-   参考文献

    hitszthesis原来使用的是GB/T 7714-2005，没能跟上时代。现在已经全面更新为GB/T 7714-2015，只需把hitszthesis.bst更换为对应的bst格式就行了。

-   毕业论文（设计）

    due把论文与设计分开了，论文一定要是论文，设计一定要是设计，不再用毕业论文（设计）的表述。解决方法是修改cfg文件（或者修改main.tex的变量）.
