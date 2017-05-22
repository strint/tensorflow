# Graph
* Graph类描述了TensorFlow计算图，是由Node和Edge组成的有向无环图。
* Node对应计算图中的操作子(Operation)；
* Edge对应计算图中的操作子之间的数据依赖，即操作的输入或输出，在TensorFlow中，这些输入、输出叫做Tensor；
* Graph的成员变量
```cpp
  // Registry of all known ops.  Not owned.
  const OpRegistryInterface* const ops_; // 系统注册op的接口，通过这个可以查找在系统中已经注册的op

  // GraphDef versions
  VersionDef versions_; // ?

  // Allocator which will give us good locality.
  core::Arena arena_; // ?

  // Map from node ids to allocated nodes.  nodes_[id] may be nullptr if
  // the node with that id was removed from the graph.
  std::vector<Node*> nodes_; // Node id 到 Node*的记录

  // Number of nodes alive.
  int64 num_nodes_ = 0; // 当前计算图中包含的Node的数量

  // Map from edge ids to allocated edges.  edges_[id] may be nullptr if
  // the edge with that id was removed from the graph.
  std::vector<Edge*> edges_; // Edge id到 Edge* 的记录

  // For ease of iteration, we currently just keep a set of all live
  // edges.  May want to optimize by removing this copy.
  EdgeSet edge_set_; // 将图中包含的Edge又记录到了Set中

  // Allocated but free nodes and edges.
  std::vector<Node*> free_nodes_; // ?
  std::vector<Edge*> free_edges_; // ?

  // For generating unique names.
  int name_counter_ = 0;
```

* NodeIter
  * Graph中Node的迭代器，类似STL中的迭代器，作用是在Graph中按拓扑序遍历Graph中的Node和Edge

* NeighborIter
  * 对Node的相邻的Edge进行迭代遍历

## Node
* Node的成员变量
```cpp
  int id_;       // Node的id
  int cost_id_;  // 
  NodeClass class_; // Node的类别
  EdgeSet in_edges_; // 该Node的输入
  EdgeSet out_edges_; // 该Node的输出
  Properties* props_; // Node的属性
  string assigned_device_name_; // 该Node在什么设备上执行
```
* Node的类别
```cpp
 enum NodeClass {
    NC_UNINITIALIZED,
    NC_SWITCH,
    NC_MERGE,
    NC_ENTER,
    NC_EXIT,
    NC_NEXT_ITERATION,
    NC_LOOP_COND,
    NC_CONTROL_TRIGGER,
    NC_SEND,              // 一个发送数据的op
    NC_HOST_SEND,
    NC_RECV,              // 一个接收数据的op
    NC_HOST_RECV,
    NC_CONSTANT,          // 一个常数节点
    NC_VARIABLE,          // 一个变量
    NC_IDENTITY,
    NC_GET_SESSION_HANDLE,
    NC_GET_SESSION_TENSOR,
    NC_DELETE_SESSION_TENSOR,
    NC_OTHER  // Not a special kind of node
  };
```
* Node Properties
```cpp
  const OpDef* op_def_;  // Op的说明
  NodeDef node_def_; // Node的说明
  const DataTypeVector input_types_; // 输入数据的类型
  const DataTypeVector output_types_; // 输出数据的类型
```

## Edge
* Edge类的成员变量
```cpp
  Node* src_; // 生成该数据的操作
  Node* dst_; // 消费该数据的操作
  int id_; // 在计算图中的编号
  int src_output_; // 生成该数据的操作的编号
  int dst_input_; // 消费该数据的操作的编号
```
