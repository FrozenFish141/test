## 功能设计
&emsp;&emsp;界面的实现包含四个类，分别为MainWindow、MainMenu、SelectMenu和Level类。<br/>
&emsp;&emsp;主界面由MainWindow实现，定义了窗口大小和全局的背景图片，并负责管理各类间的信号-槽。为了便于界面切换，CentralWidget为StackedWidget类型，含MainMenu和SelectMenu两个Widget。MainWindow除ui外没有其它成员对象，利用setLayout()函数保存在构造函数创建的MainMenu和SelectMenu指针。<br/>
&emsp;&emsp;MainMenu类显示初始的“开始-退出”页面。Start和Quit按钮点击后发送信号至MainWindow，由MainWindow调用相应槽函数。Start对应槽函数将页面切换至SelectMenu；Quit信号对应槽函数则退出应用。<br/>
&emsp;&emsp;SelectMenu显示关卡的选择页面，成员包括ui（私有）和若干个Level类指针（公有）。Level类指针的初始化在SelectMenu构造函数中实现。<br/>
&emsp;&emsp;Level类负责关卡qml文件的载入，parent为SelectMenu。成员有ui，int类型level，和Qstring类型qml_url，分别记录关卡序号和对应qml文件路径。Level类本身不加入MainWindow的CentralWidget，仅在SelectMenu界面上覆盖显示Back按钮以便返回（调用MainWindow槽函数）。用户在SelectMenu界面点击关卡选择按钮时，由SelectMenu直接发送信号给Level类，调用Level类的槽函数loadQml(int)载入qml文件。在这里使用QQuickView类对象进行qml文件载入，每次载入前先进行清除。<br/>

------------

## 个人感想
&emsp;&emsp;我在这次的工程中主要负责图形化交互界面的设计和少量的qml对象设计。<br/>
&emsp;&emsp;小组讨论后决定以C++和qml相结合来实现工程。最开始由于分工尚不明确，我先学习了使用qml编写具有不同功能的可视对象，也初步了解了Box2D物理引擎。之后我的分工明确为界面编写，这部分基本不需要用到Box2D和qml，但由于我之前没有接触过Qt，仍然需要学习很多东西。<br/>
&emsp;&emsp;我先将重点放在界面的UI设计上，选用了Qt Widgets Application类进行View部分单独的实现与测试，重心在于MainWindow、QWidget及其衍生类、以及ui对象间的相互调用关系。在对比了QStackedLayout、QTabWidget等类的特性后，我决定使用QStackedWidget类来模拟游戏界面切换。<br/>
&emsp;&emsp;在实现了基本的界面切换和按钮交互设计后，我开始设计界面的接口。小组商量后决定将单个关卡整合为一个qml文件，以减少耦合度，方便工程的分工工作。<br/>
&emsp;&emsp;在C++中调用qml对象，一般思路为使用QQmlApplicationEngine加载qml文件。但直接创建一个QQmlApplicationEngine对象并调用其load()函数的做法更多见于只需调用一个主qml的工程中，往往只需在MainWindow甚至main函数中直接进行单个QQmlApplicationEngine对象创建和一次qml文件载入。但我设计的接口类Level仅为一个子对象Widget，且存在同类不同实例分别多次调用的现象，查阅资料后决定采用QQuickView/QQuickWidget类的setSource函数，方便不同qml文件的临时载入。<br/>
&emsp;&emsp;在一开始的设计中，Level类指针是在MainWindow构造函数中初始化并加入了CentralWidget，但实际运行时出现了崩溃问题。实验后发现不仅需要将Level类的parent指向SelectMenu，同时也应保证Level类指针在SelectMenu构造函数中初始化，否则崩溃现象仍然存在。另一个有趣的发现在于，当Level类指针在SelectMenu构造函数中初始化时，本属于SelectMenu类layout的返回按钮失效，因此最终将控制返回MainMenu界面的按钮赋给了Level类。<br/>
&emsp;&emsp;在界面实现之外，我也少量参与了利用qml创建图形对象的设计，了解了一些基础的qml类的使用方法。<br/>
&emsp;&emsp;这次暑期课程的工程实践让我接触到了MVVM思想和工程协作模式，并学习到了一些Qt编程的实践经验，是一次难得的学习经历。
