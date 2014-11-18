###ARM的mbed项目

Tags: ARM

[mbed项目](http://mbed.org/)是由ARM公司sponsor的一个开源项目，使用Apache2.0许可证。目前对NXP的demo板支持的比较多，还有一款Freescale的，ST最近刚加入。感觉和Arduino很类似，API抽象度很高，比较简洁(但相对于用STM32库函数编写的代码，可读性较差)，适合快速prototype开发，而且其使用了现在流行的Online IDE(内含Compiler)。但是目前对调试的支持似乎不足，还不适合专业的开发。

库程序基本基于C++构建，并使用了template，面向对象的比较彻底，所以完全看懂源码其实难度不小。

另外，该项目还包含一个据说衍生自RTX的[RTOS](http://mbed.org/handbook/RTOS)，图文并茂，讲的不错。




