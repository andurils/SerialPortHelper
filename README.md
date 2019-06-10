# SerialPortHelper

SerialPortHelper 是一款基于 C#的串口助手类库。

## 更新状态

串口助手类库项目，由 .NET Framework 3.5 升级到 .NET Framework 4。

主要解决 .NET3.5 系统中存在虚拟串口时 SerialPort.GetPortNames 获取端口号存在特殊字符。

> 参考文章：https://www.cnblogs.com/mic86/archive/2012/06/16/2551771.html

## 开发计划

- WPF 版本实现

## 使用实例

- [SerialPortHelperTest](https://github.com/xiaoxinpro/SerialPortHelper/tree/master/SerialPortHelperTest) 串口助手类库测试项目，主要用于演示 SerialPortHelper 类库的各项功能与测试任务。

- [SerialPostTool](https://github.com/xiaoxinpro/SerialPortHelper/tree/master/SerialPostTool) 串口调试工具，基于 SerialPortHelper 类库开发的高级串口调试工具，下载后可直接使用。

## 快速上手

这里只列举部分功能，详细操作文档请参考[使用文档](https://github.com/xiaoxinpro/SerialPortHelper/wiki)

### 前期准备

使用前需要引用 `SerialPortHelperLib`，可以直接将源码直接添加到解决方案中，也可以引用`SerialPortHelperLib.dll`文件；接下来在代码文件顶部添加 ：

    using SerialPortHelperLib;

即可开始使用串口助手类库。

### 初始化串口助手类

首先需要定义一个全局类

    //定义SerialPortHelper类
    private SerialPortHelper spb;

然后添加一个 `ComboBox` 控件，命名 `cbPortName` 。

最后在窗体加载中加入初始化代码：

    //实例化串口助手
    spb = new SerialPortHelper();

    //设置串口号ComboBox，并写入默认配置
    spb.ConfigSerialPort = new ConfigCom(cbPortName).GetConfigComData();

若要自定义串口配置可以定义 ConfigCom 类：

    //定义ConfigCom类
    private ConfigCom cc;

    //修改配置
    cc = new ConfigCom(cbSerial);
    cc.BaudRate = 14400;
    cc.DataBits = 7;
    cc.StopBits = StopBits.OnePointFive;
    cc.Parity = Parity.Even;

    //写入串口配置
    spb.ConfigSerialPort = cc.GetConfigComData();

### 开启串口

使用 `OpenCom` 开启串口，需要传入一个`out`字符串，用于返回错误信息。

    spb.OpenCom(out string strError);
    if(strError != "null")
    {
      //串口开启失败
      MessageBox.Show(strError);
    }

### 关闭串口

与开启串口类似，使用 `CloseCom` 方法关闭串口。

    spb.CloseCom(out string strError);
    if (strError != "null")
    {
      //关闭串口失败
      MessageBox.Show(strError);
    }

### 发送数据

发送 Byte 数组到串口：

    spb.Write(Byte数组);

发送十六进制到串口：

    spb.Write(SerialData.ToHexByteArray(十六进制字符串));

发送字符串到串口：

    spb.Write(SerialData.ToByteArray(字符串));

### 接收数据

若要接收数据，需要在串口初始化时增加接收事件绑定：

    //串口助手类初始化
    //绑定接收数据函数SerialPortDataReceivedProcess
    spb.BindSerialPortDataReceivedProcessEvent(new SerialPortHelper.DelegateSerialPortDataReceivedProcessEvent(SerialPortDataReceivedProcess));

绑定的方法 `SerialPortDataReceivedProcess` 示例如下：

    /// <summary>
    /// 串口接收数据处理
    /// </summary>
    /// <param name="sender">串口助手类对象</param>
    /// <param name="arrData">接收数据数组</param>
    private void SerialPortDataReceivedProcess(object sender, byte[] arrData)
    {
        this.Invoke(new Action(() =>
        {
            //串口数据处理
        }));
    }

> 关于接收数据转换可参考 Wiki 中的 [SerialData](https://github.com/xiaoxinpro/SerialPortHelper/wiki/SerialData) 部分。
