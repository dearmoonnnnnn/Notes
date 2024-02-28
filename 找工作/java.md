# arrays.stream()

https://blog.csdn.net/a13662080711/article/details/84928181

java8中的Stream是对**集合(collection)对象**功能的增强，它专注于对集合对象进行各种非常遍历、高效的**聚合操作(aggregate operation)**。与java.io包里的InputStream和OutputStream是完全不同概念。

Stream不是集合元素，不是数据结构并不保存数据，它是有关算法和计算的，更像一个高级版本的Iterator

# ArrayList

可以动态修改的数组，没有固定大小的限制，可以添加或删除元素

List和ArrayList的区别：List是一个**接口**，ArrayList是List接口的一个实现类