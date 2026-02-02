# D028 vue+django知识图谱可视化系统|AI 相关

> 完整项目收费，可联系QQ: 81040295 微信: mmdsj186011 注明从github来的，谢谢！
也可以关注我的B站： 麦麦大数据 https://space.bilibili.com/1583208775
> 

up主B站：  **麦麦大数据**
关注B站，有好处！

编号:  D028
## 视频
《待发布》
## 1 系统简介
系统简介：本系统是一个基于Vue+Django构建的AI知识图谱可视化系统，其核心功能围绕AI相关知识的抓取、提取、存储、可视化和用户管理展开。主要包括：首页，用于展示系统概览和AI领域的热点知识图谱；知识图谱模块，通过爬虫抓取AI相关内容，提取三元组关系并存储到Neo4j数据库，提供知识点的可视化展示和关键词查询功能；可视化展示模块，采用多种图表形式直观呈现知识图谱结构，并支持用户与图谱节点的交互操作；以及用户管理模块，包含用户登录、注册、头像修改、个人信息维护以及密码修改功能，确保系统的安全性和个性化体验。
## 2 功能设计
该系统采用典型的B/S（浏览器/服务器）架构模式。用户通过浏览器访问Vue前端界面，该前端由HTML、CSS、JavaScript以及Vue.js生态系统中的Vuex（用于状态管理）、Vue Router（用于路由导航）和ECharts（用于数据可视化）等组件构建。前端通过API请求与Django后端进行数据交互，Django后端则负责业务逻辑处理，并利用Py2Neo（或类似工具）与Neo4j图数据库进行数据存储和查询。此外，系统还包含一个独立的爬虫模块，负责从外部来源抓取AI相关内容，通过自然语言处理技术提取三元组关系，并将其存储到Neo4j数据库，为整个系统提供数据支撑。
### 2.1系统架构图
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/533f08973ccc496b8d81395e8df64210.jpeg)
### 2.2 功能模块图
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/a4b728d78ee645b8a52f0cbd760bd54a.jpeg)
### 2.3 功能介绍文档
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/23888443a7a343c2a95ace37cd520df4.png)
## 3 功能展示
### 3.0 知识图谱的 neo4j 展示
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/0eb54100b4d44ce8bc1ca9a60ffd6afc.png)
### 3.1 登录 & 注册
登录界面背景是一个视频，展示和本文系统主题相匹配的内容，登录和注册界面在一个界面下，通过按钮来切换，注册界面输入用户名和密码，会检查这个用户是否存在，登录界面则要检查用户名是否存在以及用户名密码是否正确：  
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/929c7c085d4948bdb3717fe4994ee11d.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/3eba9581f93d4d33b9b3ee2fe9bd9ad8.png)
### 3.2 主页
如果通过校验，则可以进入主页，在主页是一个左侧菜单，右侧操作面板的布局，右上角是登录用户的头像和退出按钮：
### 3.3 知识图谱的可视化与检索
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/4cc7683a245a4de1912e1188d176b736.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/579772c58d5e4373a762447ad97cdec3.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/8ea934c039f4485d96ce8bfc9f22ffbb.png)
### 3.4 个人设置
个人设置方面包含了**用户信息修改**、**密码修改**功能。
用户信息修改中可以上传头像，完成用户的头像个性化设置，也可以修改用户其他信息。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/3b97f85d5a12435ab535c933757b7939.png)
修改密码需要输入用户旧密码和新密码，验证旧密码成功后，就可以完成密码修改。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/f4796a22766e42c881d6fbb0ef7f0ac5.png)
## 4程序代码
### 4.1 代码说明
以下是一个基于Vue前端和ECharts实现的三元组知识图谱可视化展示的代码示例。代码实现了知识点之间的关系展示，节点代表知识点，边代表知识点之间的关系，支持动态数据更新和交互式可视化：
### 4.2 流程图
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/c9c078da3ca7455290381e80baeba3d6.png)

### 4.3 代码实例
```python
<template>
  <div class="knowledge-graph">
    <div id="graph" :style="{width: '100%', height: '600px'}"></div>
  </div>
</template>

<script>
import echarts from 'echarts'
import 'echarts-graph'

export default {
  data() {
    return {
      chart: null,
      data: {
        nodes: [],
        edges: []
      }
    }
  },
  mounted() {
    this.initGraph()
    this.updateGraph()
  },
  methods: {
    initGraph() {
      this.chart = echarts.init(document.getElementById('graph'))
      this.chart.setOption({
        title: {
          text: 'AI知识图谱'
        },
        toolbox: {
          feature: {
            saveAsImage: {}
          }
        },
        legend: {
          data: ['知识点']
        },
        animation: true,
        series: [{
          type: 'graph',
          layout: 'force',
          data: this.data.nodes,
          edges: this.data.edges,
          force: {
            repulsion: 800,
            gravity: 0.1
          },
          lineStyle: {
            color: '#666',
            width: 2,
            type: 'solid'
          },
          label: {
            normal: {
              show: true,
              formatter: '{b}'
            }
          }
        }]
      })
    },
    updateGraph() {
      // 示例数据格式
      const nodes = [
        {id: 'A', label: '人工智能'},
        {id: 'B', label: '机器学习'},
        {id: 'C', label: '深度学习'},
        {id: 'D', label: '神经网络'}
      ]
      const edges = [
        {source: 'A', target: 'B', label: '属于'},
        {source: 'B', target: 'C', label: '发展为'},
        {source: 'C', target: 'D', label: '依赖于'}
      ]
      this.data = {nodes, edges}
      this.chart.setOption({
        series: [{
          data: nodes,
          edges: edges
        }]
      })
      // 动态添加节点
      setTimeout(() => {
        const newNode = {id: 'E', label: '自然语言处理'}
        const newEdge = {source: 'C', target: 'E', label: '应用'}
        this.data.nodes.push(newNode)
        this.data.edges.push(newEdge)
        this.chart.setOption({
          series: [{
            data: this.data.nodes,
            edges: this.data.edges
          }]
        })
      }, 3000)
    }
  }
}
</script>

<style scoped>
.knowledge-graph {
  padding: 20px;
  background: #f5f5f5;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}
</style>

```
