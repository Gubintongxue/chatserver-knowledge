## Json

### 介绍

Json是一种轻量级的数据交换格式（也叫数据序列化方式）。Json采用完全独立于编程语言的文本格式来存储和表示数据。简洁和清晰的层次结构使得 Json 成为理想的数据交换语言。 易于人阅读和编写，同时也易于机器解析和生成，并有效地提升网络传输效率。

客户端需要将数据序列化为[字节流](httpsso.csdn.netsosearchq=%E5%AD%97%E8%8A%82%E6%B5%81&spm=1001.2101.3001.7020)字符流发送到服务端，服务端需要进行反序列化为原来的消息格式。常用的数据传输序列化格式有`XML`，`Json`，`ProtoBuf`，在公司级别的项目中，大量的在使用`ProtoBuf`作为数据序列化的方式，以其数据压缩编码传输，占用带宽小，同样的数据信息，是`Json`的110，`XML`的120，但是使用起来比`Json`稍复杂一些，所以项目中我们选择常用的`Json`格式来打包传输数据.

设置`json`序列化时候，用法就和C++中`unordered_map`相似，是`kv`的方式进行存储数据

### 简单使用

我选择的是 JSON for Modern C++，其有如下特点：

-   直观的语法
-   整个代码由一个头文件组成 `json.hpp`，没有子项目，没有依赖关系，没有复杂的构建系统，使用起来非常方便
-   使用 C++ 11 标准编写
-   使用 json 像使用 `STL` 容器一样
-   STL 和 json 容器之间可以相互转换
-   严谨的测试：所有类都经过严格的单元测试，覆盖了 100％ 的代码，包括所有特殊的行为。此外，还检查了 `Valgrind` 是否有内存泄漏。为了保持高质量，该项目遵循核心基础设施倡议(CII)的最佳实践

使用该库之前需要包含头文件

```cpp
#include json.hpp
using json = nlohmannjson;
```

#### Json数据序列化

```cpp
 普通序列化
void func1()
{
     可以认为底层用链式哈希表存储，输出后不一定顺序
    json js;
    js[msg_type] = 2;
    js[from] = zhang san;
    js[to] = wang wu;
    js[msg] = hello, what are you doing;

    string sendBuf = js.dump();

    cout  sendBuf.c_str()  endl;
}
```

运行结果是：

{“id”[1,2,3,4,5],msg{“liu shuo”“hello china”,“zhang san”“hello world”},“name”“zhang san”}

kv对中v可以继续放一个kv对象,即可以嵌套使用，如下示例

```cpp
void func2()
{
    json js;
     添加数组
    js[id] = {1, 2, 3, 4, 5};
     添加key-value
    js[name] = zhangsan;
     添加对象
    js[msg][zhangsan] = hello world;
    js[msg][liu shuo] = hello Beijing;
     上面等同于下面一次性添加数组对象
    js[msg] = {{zhangsan, hello world}, {liu shuo, hello Beijing}};
    cout  js  endl;
}
```

运行结果为

{“id”[1,2,3,4,5],“msg”{“liu shuo”“hello Beijing”,“zhangsan”“hello world”},“name”“zhangsan”}

```cpp
 容器序列化
string func3()
{
    json js;
    vectorint vec;
    vec.push_back(1);
    vec.push_back(2);
    vec.push_back(6);
    js[list] = vec;

     序列化一个map容器
    mapint, string m;
    m.insert({1, 黄山});
    m.insert({2, 华山});
    m.insert({3, 泰山});
    js[path] = m;

    string sendBuf = js.dump();  json数据对象 - 序列化 json字符串
   cout  sendBuf.c_str()  endl;
}
```

运行结果是

{“list”[1,2,5],“path”[[1,“黄山”],[2,“华山”],[3,“泰山”]]}

#### Json数据[反序列化](httpsso.csdn.netsosearchq=%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96&spm=1001.2101.3001.7020)

```cpp
string jsonstr = js.dump();
coutjsonstrjsonstrendl;
 模拟从网络接收到json字符串，通过jsonparse函数把json字符串专程json对象
json js2 = jsonparse(jsonstr);
 直接取key-value
string name = js2[name];
cout  name  name  endl;
 直接反序列化vector容器
vectorint v = js2[list];
for(int val  v)
{
cout  val   ;
}
cout  endl;
 直接反序列化map容器
mapint, string m2 = js2[path];
for(auto p  m2)
{
cout  p.first     p.second  endl;
}
cout  endl;
```

## CMake

### CMake常用预定义变量

`PROJECT_NAME`  通过 project() 指定项目名称  
`PROJECT_SOURCE_DIR`  工程的根目录  
`PROJECT_BINARY_DIR`  执行 cmake 命令的目录  
`CMAKE_CURRENT_SOURCE_DIR`  当前 CMakeList.txt 文件所在的目录  
`CMAKE_CURRENT_BINARY_DIR`  编译目录，可使用 add subdirectory 来修改  
`EXECUTABLE_OUTPUT_PATH`  二进制可执行文件输出位置  
`LIBRARY_OUTPUT_PATH`  库文件输出位置  
`BUILD_SHARED_LIBS`  默认的库编译方式 ( shared 或 static ) ，默认为 static  
`CMAKE_C_FLAGS`  设置 C 编译选项  
`CMAKE_CXX_FLAGS`  设置 C++ 编译选项  
`CMAKE_CXX_FLAGS_DEBUG`  设置编译类型 Debug 时的编译选项  
`CMAKE_CXX_FLAGS_RELEASE`  设置编译类型 Release 时的编译选项  
`CMAKE_GENERATOR`  编译器名称  
`CMAKE_COMMAND`  CMake 可执行文件本身的全路径  
`CMAKE_BUILD_TYPE`  工程编译生成的版本， Debug  Release

### 使用例子

假设有如下目录结构

![在这里插入图片描述](httpsimg-blog.csdnimg.cn054a250b58ff4d48b9bb05bf1c577717.png#pic_center)

对应的 CMake文件分别如下

.CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.0)
project(chat)

# 配置编译选项
set(CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS} -g)

# 配置最终可执行文件输出的路径
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}bin)

# 配置头文件搜索路径
include_directories(${PROJECT_SOURCE_DIR}include)
include_directories(${PROJECT_SOURCE_DIR}includeserver)
#include_directories(${PROJECT_SOURCE_DIR}includeserverdb)
include_directories(${PROJECT_SOURCE_DIR}thirdparty)

# 此时 PROJECT_SOURCE_DIR 为当前CMakeLists.txt所在路径
# 加载子目录(即告诉CMake，src文件夹里也有CMakeLists.txt)
add_subdirectory(src)
```

.srcCMakeLists.txt

```cmake
add_subdirectory(server)
```

.srcserverCMakeLists.txt

```cmake
# 定义一个SRC_LIST变量，包含了当前目录下的所有源文件
aux_source_directory(. SRC_LIST)

# 指定生成可执行文件
add_executable(ChatServer ${SRC_LIST})

# 指定可执行文件链接时需要依赖的库文件（先特殊后一般）
target_link_libraries(ChatServer muduo_net muduo_base pthread)
```

使用时在根目录下创建`build`目录，并进入 ,输入`cmake ..` 然后 `make`