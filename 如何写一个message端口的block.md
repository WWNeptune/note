# message端口（c++)
  以官方的tagged_stream_to_pdu源码为参考，位置gnuradio-maint-3.9\gr-blocks\lib\tagged_stream_to_pdu_impl.cc，写一个输出message的模块，用来控制usrp sink运行参数
# 添加原理
  1. 按照需求新建一个block，我此处新建的是sink block，因为没有流输出需求（因为目前还不知道怎么让模块自己动，所以留了个stream输入用来驱动）
  2. 对模块进行常规配置（就是input_type这些），完成后，添加头文件```#include <gnuradio/blocks/pdu.h>```，在构造函数内，注册一个message端口。如输出端口：```message_port_register_out(pdu::pdu_port_id())``` 此处pdu::pdu_port_id()是固定id，内容为```pdus```，后续在yml注册中会用到（自定义为pmt::mp("port_id"),我使用gnuradio-3.9时用pdu_port_id()会报错找不到，用自定义方法则正常）
  3. 在work函数中新建一个pmt变量```pmt pmt_t name=pmt::cons(pmt::mp("freq"), pmt::mp(1.1e9) )```变量名为name，变量的key和value为freq和1.1e9。这是控制usrp的规定格式，实际内容按需替换。若要一次包含多个数据，则需使用字典类型，参考```https://www.gnuradio.org/doc/doxygen/page_uhd.html```的示例
  4. 向端口发送message```message_port_pub(pdu::pdu_port_id(),name)```,第一个为端口id，第二个为pmt变量（如果端口id是自定义的，也要写一样的格式为pmt::mp("port_id"))
  5. 在yml中添加message端口并绑定，如  
    ```outputs:```  
  ```-   domain: message```  
  ```    id: pdus```  
  ```    optional: true```  
  注意yml空格格式
#### 注意事项
  除了注册id步骤是在构造函数，其它的都在work函数；  
  由于tagged_stream_to_pdu声明了命名空间blocks，而pmt在gr::blocks::pmt,因此在使用如pdu::pdu_port_id()，要么声明命名空间blocks，要么写作blocks::pdu::pdu_port_id()的形式  
  注意yml的格式，id和optional一定要正确