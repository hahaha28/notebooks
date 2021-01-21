# layout_weight 失效

[layout_weight is not working at all](https://stackoverflow.com/a/38195938/13479599)

## 问题描述

<img src="http://img.inaction.fun/static/73571.png" alt="image-20201231093843588" style="zoom:80%;" />

布局如图所示，最外层的`LinearLayout`的`weight_sum`为8，里面的`LinearLayout`的`layout_weight`为4，下面4个同级的`View`的`layout_weight`都为1。

理想中的效果应该是里面的`LinearLayout`占总宽度的一半，但实际上它几乎占了全部的宽度。

## 解决方案

google 后发现，问题应该出在内层`LinearLayout`里面的两个`TextView`上，这两个`TextView`的宽度都是`match_parent`，改为 ==fill_parent== 就没问题了