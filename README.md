# UE学习笔记

## UCLASS()
宏，用于开启反射系统（让UE在运行时和编辑时能够识别、检查和管理C++类）
- 关键功能：
  1. 编辑器集成：在UE编辑器中可见、可编辑、可创建。
  2. 垃圾回收：自动内存管理。
  3. 序列化：保存和加载对象状态。
  4. 网络复制：支持多玩家游戏数据同步。
  5. 蓝图交互：连接C++代码和蓝图可视化脚本。
- 使用要点：
  1. 继承自 UObject（或其子类，如 AActor, UActorComponent）。
  2. 在类声明前使用 UCLASS() 宏。
  3. 在类体内最前面包含 GENERATED_BODY() 宏。
- 常用说明符：

| 说明符 | 类别 | 作用描述 |
| :--- | :--- | :--- |
| `Blueprintable` | 蓝图交互 | 允许这个类被蓝图继承。 |
| `NotBlueprintable` | 蓝图交互 | 禁止这个类被蓝图继承（默认行为）。 |
| `BlueprintType` | 蓝图交互 | 允许这个类在蓝图中作为变量类型使用。 |
| `Placeable` | 编辑器集成 | 允许把这个类的实例放置到关卡中（默认行为）。 |
| `NotPlaceable` | 编辑器集成 | 禁止把这个类的实例放置到关卡中。 |
| `ClassGroup="GroupName"` | 编辑器集成 | 在编辑器下拉菜单中将此类分组到自定义类别下。 |
| `Within=OuterClass` | 高级 | 限制此类的实例只能作为指定外部类的子对象创建。 |

---
 
## UPROPERTY()
宏，修饰成员变量，用于开启反射系统
- 关键功能：
  同UCLASS()。
- 常用说明符：

| 说明符 | 类别 | 作用描述 |
| :--- | :--- | :--- |
| `VisibleAnywhere` | 编辑器访问 | 在任何细节面板中都**可见**但**不可编辑**。 |
| `VisibleDefaultsOnly` | 编辑器访问 | 仅在类默认对象（CDO）的细节面板中可见（即蓝图编辑器中）。 |
| `VisibleInstanceOnly` | 编辑器访问 | 仅在关卡中实例对象的细节面板中可见。 |
| `EditAnywhere` | 编辑器访问 | 在**任何**细节面板中都**可编辑**。 |
| `EditDefaultsOnly` | 编辑器访问 | **常用**。仅在类默认对象（CDO）的细节面板中可编辑。 |
| `EditInstanceOnly` | 编辑器访问 | 仅在关卡中实例对象的细节面板中可编辑。 |
| `BlueprintReadOnly` | 蓝图访问 | **蓝图只读**。蓝图可以读取，但不能修改。 |
| `BlueprintReadWrite` | 蓝图访问 | **蓝图可读写**。蓝图可以读取和修改。**需谨慎使用。** |
| `Replicated` | 网络复制 | **常用(网络)**。变量变化时从服务器同步到客户端。**需实现 `GetLifetimeReplicatedProps`。** |
| `ReplicatedUsing=OnRep_Function` | 网络复制 | 变量复制时，在客户端上会调用指定的**复制回调函数** (RepNotify)。 |
| `Transient` | 内存/序列化 | 变量是临时的，不保存也不加载（如运行时临时指针）。 |
| `SaveGame` | 内存/序列化 | 变量将被包含在存档游戏数据中。 |
| `BlueprintAssignable` | 委托 | **用于多播委托**。允许在蓝图中绑定事件到此委托。 |
| `Category="Category\|SubCategory"` | 编辑器集成 | **强烈推荐**。在细节面板中将变量放入指定的分类中。 |

---

- 通用元数据
元数据通常以 `meta=(Key=Value, ...)` 的形式提供附加信息，常用于 `UPROPERTY()`。

| 元数据键 | 适用宏 | 作用描述 |
| :--- | :--- | :--- |
| `ClampMin="X"`, `ClampMax="X"` | `UPROPERTY` | 在细节面板中为数值型属性设置滑块范围**并钳制**输入值。 |
| `UIMin="X"`, `UIMax="X"` | `UPROPERTY` | 在细节面板中为数值型属性设置滑块范围，但**不钳制**输入值。 |
| `ToolTip="Description"` | `UPROPERTY`, `UFUNCTION` | 当鼠标悬停在属性或函数上时显示的工具提示文本。 |
| `DisplayName="Readable Name"` | `UPROPERTY`, `UFUNCTION` | 在编辑器UI中显示一个更易读的名称，而非变量/函数名。 |
| `BlueprintAutocast` | `UPROPERTY` | (高级) 允许在蓝图中自动将对象转换为此属性类。 |
| `AllowPrivateAccess` | `UPROPERTY` | 允许蓝图访问私有（`private`）属性。 |

---

## UFUNCTION()
宏，提供反射功能
- 关键功能：
  1. 蓝图交互：函数可以在蓝图中被调用（Call）、被重写（Override）或被绑定为事件（Event）。
  2. 动态调用：可以通过函数名称（字符串）在运行时查找并调用此函数，这是实现回调、事件系统等高级功能的基础。
  3. 网络复制（RPC）：函数可以在服务器和客户端之间远程调用，这是多玩家游戏功能的核心。
  4. 作为委托签名：函数可以作为动态多播委托的签名，允许蓝图绑定到它。
  5. 引擎回调：可以被引擎内部系统自动调用，例如 Tick()、BeginPlay() 或 Notify()（动画通知）。
- 常用说明符：

| 说明符 | 类别 | 作用描述 |
| :--- | :--- | :--- |
| `BlueprintCallable` | 蓝图交互 | **常用**。该函数可以在蓝图图表中作为一个节点被调用。逻辑在C++中实现。 |
| `BlueprintPure` | 蓝图交互 | 函数是“纯”函数，不修改状态（应声明为const）。其节点只有输出引脚，无执行引脚。 |
| `BlueprintImplementableEvent` | 蓝图交互 | 函数在C++中**只有声明**，其具体实现**完全由蓝图**提供。 |
| `BlueprintNativeEvent` | 蓝图交互 | 函数在C++中有一个默认实现（`_Implementation`），但**可以在蓝图中被重写**。 |
| `Server` | 网络复制 (RPC) | 标记为**服务器函数**。在客户端调用，在服务器上执行。 |
| `Client` | 网络复制 (RPC) | 标记为**客户端函数**。在服务器调用，在**拥有此Actor的客户端**上执行。 |
| `NetMulticast` | 网络复制 (RPC) | 标记为**多播函数**。在服务器调用，在**服务器和所有客户端**上执行。 |
| `Reliable` | 网络复制 (RPC) | 与RPC说明符联用，保证函数调用最终会送达（开销大）。 |
| `Unreliable` | 网络复制 (RPC) | 与RPC说明符联用，不保证函数调用会送达（开销小，用于频繁更新）。 |
| `BlueprintAssignable` | 委托 | **用于委托声明**。声明一个蓝图可绑定事件的多播委托。 |
| `Category="CategoryName"` | 编辑器集成 | **推荐**。在蓝图的节点菜单中，将函数放入指定的分类中。 |

---
  
