# DesignPattern
设计模式整理

将本人在编程过程中的一些体会，以及网上看到的心得的记录和整理，方便以后自己和团队的参考，提高效率。如果本文对其他开发人员也有所帮助，也欢迎大家给出反馈或者提出改进意见。

*	[工厂模式](#factory)
*	[单例模式](#singleton)
*	[建造者模式](#builder)
*	[适配器模式](#adaptor)
*	[观察者模式](#observer)
*	[组合模式](#composite)
*	[迭代器模式](#iterator)
*	[原型模式](#prototype)
*	[代理模式](#proxy)

<h4 id="factory">工厂模式</h4>

定义：定义一个用于创建对象的接口，让子类决定实例化成哪一个类。适用于出现的类有共同的接口时，用工厂模式进行创建。工厂类一般通过静态方法返回指定的对象

优点
*   客户端不需要创建对象，交给具体的工厂类去创建
*   客户端无需知道具体实现，封装

缺点
*   如果对象较多，则类的体积庞大
*   如果添加新的类，就需要修改工厂类


示例：Android中BitmapFactory 通过静态方法返回Bitmap对象

---

<h4 id="singleton">单例模式</h4>

定义：该对象只有一个实例存在

优点
*   某些需要创建频繁的类，尤其是大型的对象，单例模式可以降低系统开销
*   某些类属于核心类，同一时间只能有一个实例存在，或是系统只需要一个实例，如交易所的核心交易引擎

缺点
*   如果构造函数中抛出异常，就无法创建实例
*   单例模式一般没有接口，扩展困难，除非修改代买
*   单例模式对测试不利。在并行环境中，如果单例模式没有完成，则不能进行测试
*   单例模式和“单一职责原则”冲突

---

<h4 id="builder">建造者模式</h4>

定义：将一个复杂对象的构建与其表示相分离，使得同样的构建过程可以创建不同的表示

*   与抽象工厂模式相比， 建造者模式返回一个组装好的完整产品 ，而 抽象工厂模式返回一系列相关的产品，这些产品位于不同的产品等级结构，构成了一个产品族。
*   在抽象工厂模式中，客户端实例化工厂类，然后调用工厂方法获取所需产品对象，而在建造者模式中，客户端可以不直接调用建造者的相关方法，而是通过指挥者类来指导如何生成对象，包括对象的组装过程和建造步骤，它侧重于一步步构造一个复杂对象，返回一个完整的对象
*   如果将抽象工厂模式看成 汽车配件生产工厂 ，生产一个产品族的产品，那么建造者模式就是一个 汽车组装工厂 ，通过对部件的组装可以返回一辆完整的汽车。

示例：Android: AlertDialog.Builder 通过静态类Builder设置参数，一步一步创建一个新的AlertDialog

---

<h4 id="adaptor">适配器模式</h4>

定义：新接口和老接口进行适配，让原本因为接口不兼容而不能一起工作的类可以一起工作

示例：Android中ListViewAdaptor，根据用户定义的adaptor实现getView方法，从而获取不同的view item

---

<h4 id="observer">观察者模式</h4>

定义：对象之间一对多的关系，当一个对象（被观察者）的状态发生改变时，所有依赖于它的对象（观察者）都会得到通知并自动更新

示例：Android中ListViewAdaptor, 当向listview添加新数据后，调用notifyDataSetChanged()进行更新. Adaptor中会有一个DataSetObservable（观察者），会调用对应的onChanged函数，获取Adaptor中数据集的新数量，然后调用listview的requestLayout重新布局，更新界面

---

<h4 id="composite">组合模式</h4>

定义：用树形的结构来表示，分级或递归调用，将单一对象和对象组合用相同的方式看待。如树形菜单，文件夹管理

示例：Android中view(Button, TextView)和viewGroup(RelativeLayout, LinearLayout)

     <RelativeLayout>
          <Button />（第一层）
          <TextView />（第一层）
          <RelativeLayout>（第一层）
               <Button />（第二层）
               <TextView />（第二层）
          </RelativeLayout>
     </RelativeLayout>

---

<h4 id="iterator">迭代器模式</h4>

定义：迭代器模式：提供一种方法访问容器中的每个元素，而又不会暴露内部细节

示例：Java容器的迭代器，STL中容器的迭代器

    Iterator it = arrayList.iterator();
    while(it.hasNext()) {
         String str = (String)it.next();
    }

---

<h4 id="prototype">原型模式</h4>

定义：原型模式：对于已有的对象，希望生成该对象的一个副本，于是通过拷贝（克隆）根据原型生成新的对象。拷贝可以是浅拷贝或是深拷贝，java中深拷贝必须重写clone方法（必须实现Cloneable接口）

示例：

    A a = new A();
    A b = (A)a.clone();

---

<h4 id="proxy">代理模式</h4>

定义：为其他对象提供一种代理以控制对这个对象的访问（在初始对象上封装一层）

示例：
*   授权机制 不同级别的用户对同一对象拥有不同的访问权利，如网站登录（会员，管理员）
*   对象比较复杂或需要长时间去构造，如网页加载时，如果图片较大，或是需要进行下载，就通过图片proxy
