> 开源项目 paperclip 是一个Agent Company的概念，wxp本地化改造为AI产线，本篇根据源码理解它的设计

![[paperclip.excalidraw]]

# Architecture

1. paperclip -> control plane
2. adapters  -> execute 真正的执行层 

Paperclip不写runtime，而是编排任何的Agent（claude，cursor），只要实现对应的adapter *（这点很值得思考，因为runtime设计又是另一门大学问了，参见ClaudeCode的实现，Paperclip只解决它擅长的 ）*