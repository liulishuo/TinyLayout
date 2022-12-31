# TinyLayout

## UI布局方案

- frame
- AutoresizingMask
- NSLayoutConstraint
  - NSLayoutAnchor
  - SnapKit
  - TinyLayout

## 设计思路

- 体积要小
- 每个函数功能正交
- 性能要好

## 应用场景

- 不想手写约束

  ![img1](/Users/liulishuo/Documents/resource/开源项目/TinyLayout/Images/img1.png)

![img2](/Users/liulishuo/Documents/resource/开源项目/TinyLayout/Images/img2.png)

- 不方便引用第三方布局库

## Snapkit（5.6.0） vs TinyLayout

### 37个文件 vs 1个文件

<img src="/Users/liulishuo/Documents/resource/开源项目/TinyLayout/Images/img3.png" alt="img3" style="zoom:50%;" />

### UML

#### SnapKit

![SnapKit_UML](/Users/liulishuo/Documents/resource/开源项目/TinyLayout/Images/SnapKit_UML.png)



		- 布局靠ConstraintMaker执行
		- 数据靠ConstraintDescription存储
		- 实现链式语法导致设计复杂
		- 组合Attribute、DEBUG、快捷函数，大部分比较鸡肋，同时占用了较多资源

#### TinyLayout

![TinyLayout_UML](/Users/liulishuo/Documents/resource/开源项目/TinyLayout/Images/TinyLayout_UML.png)

### 功能对比

|               | TinyLayout | Snapkit                                 |
| ------------- | ---------- | --------------------------------------- |
| mac OS        | ❌          | ✅                                       |
| 20个Attribute | ✅          | ✅                                       |
| 组合Attribute | ❌          | 如edges、horizontalEdges、verticalEdges |
| 链式语法      | ❌          | .left.right.top                         |
| 倍率          | *\|/       | multipliedBy\|dividedBy                 |
| 偏移          | +｜-       | offset｜inset                           |
| 赋值          | ==         | equalTo()                               |
| 大于等于      | >=         | greaterThanOrEqualTo()                  |
| 小于等于      | <=         | lessThanOrEqualTo()                     |
| UILayoutGuide | ❌          | ✅                                       |
| 优先级        | ~          | priority()                              |
| DEBUG标签     | ❌          | ✅                                       |
| 多线程安全    | ❌          | ❌                                       |

- 加了主线程异步执行后，代码的执行顺序与观感不符，容易误判。

### 弱点

- Snapkit 每一条布局语句make会激活一次约束，效率低

![img4](/Users/liulishuo/Documents/resource/开源项目/TinyLayout/Images/img4.png)

- 同理Snapkit在删除约束时也是一条一条的deactivate，效率低

- Snapkit判断约束是否存在是遍历数组

- 使用关联对象管理布局目标上的约束集合，涉及运行时，性能较差。
  - Tinylayout一开始使用当前视图和其父视图的constraints数组的集合实现当前视图约束的更新，但是当约束两个不在同一个父视图的上的视图时存在巨大漏洞，所以虽然关联对象性能堪忧，但在实现约束的更新、重置功能时，自行通过关联对象管理布局目标上的约束是无法绕过的，两种方式的实现效率大概相差7-8倍
- TinyLayout 与 SnapKit 的性能对比
