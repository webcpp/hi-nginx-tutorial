# LRU 缓存加速

hi-nginx自带LRU缓存加速机制。该机制结合时间过期算法，可有效提升应用吞吐率。使用方法极其简单，设置`x_expires`大于0即可。其中，`x`表示web应用开发语言之一种。

众所周知，python是比较慢的脚本语言。如果设置`py_expires 1s`，那么由它开发的web应用可比肩于其他开发语言,稍逊而已。