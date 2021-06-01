# 1 Java语法

- jdk1.7以后，`try{}catch(){}finaliy{} `变成了` try(){}catch(){}`

  凡是在try的括号中声明的类都必须实现java.io.Closeable接口，这样try就会自动将声明的流在使用完毕后自动关闭。

- hashmap定义时，不指定类型，就可以传入任意的k-v，指定了类型，会进行类型检查。