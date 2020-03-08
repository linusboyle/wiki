# week3 report

本周看完了VLSI Physical Design From Graph Partitioning to Timing Closure关于布局的一章，此外阅读了相关论文：

- Lin, Yibo, Shounak Dhar, Wuxi Li, Haoxing Ren, Brucek Khailany, and David Z. Pan. “DREAMPlace: Deep Learning Toolkit-Enabled GPU Acceleration for Modern VLSI Placement.” In Proceedings of the 56th Annual Design Automation Conference 2019 on   - DAC ’19, 1–6. Las Vegas, NV, USA: ACM Press, 2019. https://doi.org/10.1145/3316781.3317803.
- Breuer, M. A. “A Class of Min-Cut Placement Algorithms.” In Papers on Twenty-Five Years of Electronic Design Automation  - 25 Years of DAC, 142–48. Not Known: ACM Press, 1988. https://doi.org/10.1145/62882.62896.
- Zhu, Ziran, Jianli Chen, Zheng Peng, Wenxing Zhu, and Yao-Wen Chang. “Generalized Augmented Lagrangian and Its Applications to VLSI Global Placement *.” In 2018 55th ACM/ESDA/IEEE Design Automation Conference (DAC), 1–6. San Francisco, CA: IEEE, 2018. https://doi.org/10.1109/DAC.2018.8465922.

这几篇是最近DAC中找的关于布局的一部分论文，从中能得到的信息有：

- 布局过程中，总体布局耗时最长，也最重要
- 目前工业界的主要布局算法是非线性的分析算法，这种算法准确但是速度很慢，可扩展性差
- 利用多核处理器并行处理所能得到的性能提升很有限
- Cost Estimation Function是优化的一个重点。因为最小Steiner树的构造是NP的，因此一般是采用近似。
- 现在有利用人工智能原理的算法出现

关于最后人工智能的应用，主要是用神经网络的方法。因为深度学习本质上就是解非线性方程，因此可以把布局过程中解非线性方程的部分交给神经网络来做。为了防止过拟合，可以把布局密度作为神经网络的规范项加入到训练中。第一篇论文描述了这个过程，并得到了很大的性能提升。

课程报告内容不太确定，大概是关于人工智能布局算法的吧……