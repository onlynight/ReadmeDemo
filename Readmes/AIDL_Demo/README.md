AIDL使用以及原理分析
==============

AIDL的使用
-----------
注：由于基于eclipse的adt过于老旧这里不再讲解操作，请使用android studio完成以下操作。
+ 使用AIDL文件
	1. 在你想要创建aidl的包下新建aidl文件（这里我们命名为IDataManager），aidl文件的语法与java类似，默认生成的aidl会有一个demo方法
    ```
    interface IDataManager {
    /**
     * Demonstrates some basic types that you can use as parameters
     * and return values in AIDL.
     */
    void basicTypes(int anInt, long aLong, boolean aBoolean, float aFloat,double aDouble, String aString);
}
    ```
+ 自己实现Binder

AIDL原理分析
-------------