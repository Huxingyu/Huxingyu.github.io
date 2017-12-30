最后，我们在一个图片类别的evidence中加入偏置(bias)，加入偏置的目的是加入一些与输入独立无关的信息。所以图片类别的evidence为

$$ evidence\_{i}=\sum \_{j}W\_{ij}x\_{j}+b\_{i} $$

其中，\\( W\_i \\) 和 \\( b\_i \\) 分别为类别 \\( i \\) 的权值和偏置。


<script type="text/javascript"
   src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>