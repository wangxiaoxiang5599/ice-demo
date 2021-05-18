### 编译Java的切片定义
切片文件 （Printer.ice），编译此切片文件。
在构建项目时，sliceCompile 任务（由Ice Builder插件自动添加）编译 Printer.ice 并build/generated-src使用Slice to Java编译器生成java代码。
```
module Demo
{
    interface Printer
    {
        void printString(string s);
    };
};

```

### 编写和编译服务器
```
public class Server
{
    public static void main(String[] args)
    {
        try(com.zeroc.Ice.Communicator communicator = com.zeroc.Ice.Util.initialize(args))
        {
            com.zeroc.Ice.ObjectAdapter adapter = communicator.createObjectAdapterWithEndpoints("SimplePrinterAdapter", "default -p 10000");
            com.zeroc.Ice.Object object = new PrinterI();
            adapter.add(object, com.zeroc.Ice.Util.stringToIdentity("SimplePrinter"));
            adapter.activate();
            communicator.waitForShutdown();
        }
    }
}
```

### 编写和编译客户端
```
public class Client
{
    public static void main(String[] args)
    {
        try(com.zeroc.Ice.Communicator communicator = com.zeroc.Ice.Util.initialize(args))
        {
            com.zeroc.Ice.ObjectPrx base = communicator.stringToProxy("SimplePrinter:default -p 10000");
            Demo.PrinterPrx printer = Demo.PrinterPrx.checkedCast(base);
            if(printer == null)
            {
                throw new Error("Invalid proxy");
            }
            printer.printString("Hello World!");
        }
    }
}
```

### 运行客户端和服务器
执行gradle插件的build命令后，生成lib下可执行的jar.
先启动服务端
```
java -jar server/build/libs/server.jar
```
在执行客户端
```
java -jar client/builds/libs/client.jar
```
客户端运行并退出而不产生任何输出；但是，在服务器窗口中，我们看到"Hello World!"了打印机产生的。

参考:https://doc.zeroc.com/ice/3.7/hello-world-application/writing-an-ice-application-with-java
