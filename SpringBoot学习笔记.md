 

 



 学习资源：https://km.sankuai.com/page/470202026



# 代码结构

domain的概念，通常会分很多层，比如经典的三层架构，控制层、业务层、数据访问层（DAO），此外，还有一个层，就是domain层

domain层，通常就是用于放置这个系统中，与数据库中的表，一一对应起来的JavaBean的



model层：和domain区别；可能都是javaBean，

这个区别是用途不同，domain通常就代表了与数据库表一一对应的javaBean,

model通常代表了不与数据库一一对应的javaBean，但是封装的数据是前端的JS脚本，需要使用的数据


domain是与数据库对应的对象，mapper是其对应的dao，model是与前端对应（xxxVo，View Object）的视图对象。model可以通过domain来构造



repository表示持久层的对象，使用mapper





`@Data` : 注解在类上, 为类提供读写属性, 此外还提供了 equals()、hashCode()、toString() 方

> 是lombok下的功能，需要导入lombok.Data
