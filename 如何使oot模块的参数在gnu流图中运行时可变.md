# 如何使oot模块的参数在gnu流图中运行时可变（c++)
  一般来说，使用gr_modtool生成模块时，输入参数在流图启动时用来构造对象，而没有提供方法供运行时进行参数更新。若想在运行时实现参数更新，则要创建更新方法，并逐个更新文件。该过程步骤较多，以官方源码add_const_v进行参考学习
  1. 更改基类头文件  
  此处要注意的是pybind调用的方法是从基类调用，而不是实现类，基类位于include文件夹中，而实现类在lib文件夹中，区别在于后缀带不带_impl。打开基类头文件后，在类的public里新建方法，如```virtual void set_range(float range)=0```。此处创建的是纯虚函数，是因为其实现仍然要在继承类中(_impl.h_)。
  2. 重新生成bind文件
  与常规编译不同，修改基类后需要重新生成bind文件，否则无法通过编译检查（我试过不重新生成bind的话，多一个空格都不会过编译）。生成方法为终端打开模块根目录（gr-模块名），然后在终端输入```gr_modtool bind 子模块名```，等待执行完毕。期间若有报错一般是基类语法错误。注意，最初生成的基类内容不要轻易修改，尽量只添加。
  3. 于实现类中完善方法
  完成bind文件更新后，后面的步骤就是常规编程。于实现类头文件中重写刚刚添加的方法，比如我是```void set_range(float range)override {d_range=range};```。此处和基本模块编写步骤相同故不做详述。
  4. 在yml中绑定端口
  虽然新的方法已经完善，但gnu模块仍然对接的是以前的接口，新的接口需要进行新的绑定。其中，parameters内的参数是用于构造函数阶段的，对于参数更新则是通过回调。在templates分类下，新建callbacks分类，然后使用刚刚方法的名称创建回调，比如：  
    ```templates:```  
    ```    imports: from gnuradio import blocks```  
    ```    make: blocks.add_const_${ 'v' if context.get('vlen')() > 1 else '' }${type.fcn}(${const})```  
    ```    callbacks:```  
    ```	   - set_k(${const})```  
    此部分是于blocks_add_const_vxx.block.yml中复制，详细格式可自行打开源码文件查看```gnuradio-maint-3.9\gr-blocks\grc\blocks_add_const_vxx.block.yml```,关键部分为其中的callbacks。set_k是方法名，$(const)是引用的parameter（这个已经在parameters里创建）