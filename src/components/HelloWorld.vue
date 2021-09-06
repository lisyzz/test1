<template>
  <div>
    <div id="container" style="width: 75%; height:800px; background-color:#fff">
    </div>
    <el-dialog :visible.sync="showDialog" :show-close="false" width="500" @close="dialogClose">
      <el-card class="bigBox">
        <div
         v-for="(item, idx) in searchBox"
         :key="idx" @click="add(item)"
         :title="'点击添加该公司：' + item.label"
         class="searchCompany">
          {{item.label}}
        </div>
      </el-card>
      <p class="tips">Tips:余{{searchBox.length}}项，请点击您需要的数据予以显示</p>
    </el-dialog>
  </div>
</template>

<script>
import G6 from '@antv/g6'
export default {
  name: 'HelloWorld',
  data() {
    return {
      graph: '',
      Util: null,
      showDialog: false,
      hoverNode: '',
      searchId: '',
      preId: '',
      rawData: {
        label: 'Modeling Methods',
        // id: '0',
        children: [{
            label: 'Classification',
            // id: '0-1',
            type: 1,
            color: '#5AD8A6',
            children: [{
                label: 'Logistic regression',
                // id: '0-1-1',
              },
              {
                label: 'Linear discriminant analysis',
                // id: '0-1-2',
              },
              {
                label: 'Rules',
                // id: '0-1-3',
              },
              {
                label: 'Decision trees',
                // id: '0-1-4',
              },
              {
                label: 'Naive Bayes',
                // id: '0-1-5',
              },
              {
                label: 'K nearest neighbor',
                // id: '0-1-6',
              },
              {
                label: 'Probabilistic neural network',
                // id: '0-1-7',
              },
              {
                label: 'Support vector machine',
                // id: '0-1-8',
              },
            ],
          },
          {
            label: 'Consensus',
            // id: '0-2',
            type: 2,
            color: '#F6BD16',
            children: [{
                label: 'Models diversity',
                // id: '0-2-1',
                children: [{
                    label: 'Different initializations',
                    // id: '0-2-1-1',
                  },
                  {
                    label: 'Different parameter choices',
                    // id: '0-2-1-2',
                  },
                  {
                    label: 'Different architectures',
                    // id: '0-2-1-3',
                  },
                  {
                    label: 'Different modeling methods',
                    // id: '0-2-1-4',
                  },
                  {
                    label: 'Different training sets',
                    // id: '0-2-1-5',
                  },
                  {
                    label: 'Different feature sets',
                    // id: '0-2-1-6',
                  },
                ],
              },
              {
                label: 'Methods',
                // id: '0-2-2',
                children: [{
                    label: 'Classifier selection',
                    // id: '0-2-2-1',
                  },
                  {
                    label: 'Classifier fusion',
                    // id: '0-2-2-2',
                  },
                ],
              },
              {
                label: 'Common',
                // id: '0-2-3',
                children: [{
                    label: 'Bagging',
                    // id: '0-2-3-1',
                  },
                  {
                    label: 'Boosting',
                    // id: '0-2-3-2',
                  },
                  {
                    label: 'AdaBoost',
                    // id: '0-2-3-3',
                  },
                ],
              },
            ],
          },
          {
            label: 'Regression',
            // id: '0-3',
            type: 5,
            color: '#269A99',
            children: [{
                label: 'Multiple linear regression',
                // id: '0-3-1',
              },
              {
                label: 'Partial least squares',
                // id: '0-3-2',
              },
              {
                label: 'Multi-layer feedforward neural network',
                // id: '0-3-3',
              },
              {
                label: 'General regression neural network',
                // id: '0-3-4',
              },
              {
                label: 'Support vector regression',
                // id: '0-3-5',
              },
            ],
          },
        ],
      },
      childrenNodes: {},
      otherChildren: {},
      searchBox: []
    }
  },
  methods: {
    dialogClose () {
      this.searchBox = []
    },
    add (item) {
      this.searchBox.forEach(e => {
        if (e.label === item.label) {
          this.searchBox.splice(e, 1)
          this.childrenNodes[this.searchId].unshift(e)
          this.graph.updateChildren(this.childrenNodes[this.searchId], this.otherChildren.nodeId)
        }
      });
      if (this.searchBox.length === 0) {
        this.showDialog = false
        this.graph.removeChild(this.searchId)
      }
    },
    fn () {
      G6.registerNode(
  'dice-mind-map-root', {
    jsx: (cfg) => {
      const width = this.Util.getTextSize(cfg.label, 16)[0] + 30;
      const stroke = cfg.style.stroke || '#096dd9';
      return `
      <group>
        <rect style={{width: ${width}, height: 30, stroke: ${stroke}, radius: 4}} keyshape>
          <text style={{ fontSize: 16, marginLeft: 12, marginTop: 8 }}>${cfg.label}</text>
          <text style={{ marginLeft: ${
            width - 10
          }, marginTop: -10, stroke: '#66ccff', fill: '#000', cursor: 'pointer' }} action="add">+</text>
        </rect>
      </group>
    `;
    },
    getAnchorPoints() {
      return [
        [0, 0.5],
        [1, 0.5],
      ];
    },
  },
  'single-node',
);
  G6.registerEdge('right-tree', {
    draw (cfg, group) {
      const { startPoint, endPoint } = cfg
      const xOffset = 20
      const XDiff = endPoint.x - startPoint.x
      const QPoint = {
        x: startPoint.x + (XDiff > 0 ? xOffset : -xOffset),
        y: endPoint.y
      }
      const YDiff = endPoint.y - startPoint.y
      const path = YDiff === 0 ? [
        ['M', startPoint.x, startPoint.y],
        ['L', endPoint.x, endPoint.y]
      ] : [
        ['M', startPoint.x, startPoint.y],
        ['L', QPoint.x, startPoint.y],
        ['L', QPoint.x, endPoint.y + (YDiff > 0 ? -10 : 10)],
        ['Q', QPoint.x, QPoint.y, QPoint.x + (XDiff > 0 ? 10 : -10), endPoint.y],
        ['L', endPoint.x, endPoint.y]
      ]
      const shape = group.addShape('path', {
        attrs: {
          path,
          stroke: '#1890ff',
          ...cfg
        },
        name: 'right-tree-edge'
      })
      return shape
    }
  })
    },
    initTree() {
      console.log(this.Util)
      const container = document.getElementById('container');
      const width = container.scrollWidth;
      const height = (container.scrollHeight || 500) - 20;
      this.dataTransform = (data) => {
        const changeData = (d, level = 0) => {
          let data = {
            ...d,
          };
          data.type = 'dice-mind-map-root';
          data.hover = false;
          if (level === 1 && !d.direction) {
            if (!d.direction) {
              data.direction = d.type > 3 ? 'right' : 'left';
            }
          }
          if (d.children && d.children.length <= 5) {
            data.children = d.children.map((child) => changeData(child, level + 1));
          } else if (d.children && d.children.length > 5) {
            let id = this.Util.uniqueId()
            this.otherChildren[id] = d.children.slice(5)
            this.otherChildren[id].forEach(item => {
              item.id = this.Util.uniqueId()
              item.type = 'dice-mind-map-root'  
            })
            d.children = d.children.slice(0, 5)
            d.children.push({
              label: '搜索',
              id,
              type: 'dice-mind-map-root'
            })
            console.log(d.children)
            data.children = d.children.map(child => changeData(child, level + 1))
            this.childrenNodes[id] = data.children
          }
          return data;
        };
        return changeData(data);
      };
const tooltip = new G6.Tooltip({
  offsexX: 10,
  offsexY: 10,
  itemTypes: ['node'],
  getContent: e => {
    const outDiv = document.createElement('div')
    outDiv.style.width = 'fit-content'
    outDiv.innerHTML = `
      <h4>${e.item.getModel().label}</h4>
      <p>${e.item.getModel().id}</p>
    `
    return outDiv
  },
  shouldBegin: e => {
    return e.item.getModel().label.includes('搜索') ? false : true
  }
})
const tree = new G6.TreeGraph({
  container: 'container',
  width,
  height,
  fitView: true,
  plugins: [tooltip],
  fitViewPadding: [10, 20],
  layout: {
    type: 'mindmap',
    direction: 'H',
    getHeight: () => {
      return 16;
    },
    getWidth: (node) => {
      return node.level === 0 ?
        this.Util.getTextSize(node.label, 16)[0] :
        this.Util.getTextSize(node.label, 12)[0];
    },
    getVGap: () => {
      return 20;
    },
    getHGap: () => {
      return 70;
    },
    getSide: (node) => {
      return node.data.direction;
    },
  },
  defaultEdge: {
    type: 'right-tree',
    style: {
      lineWidth: 2,
    },
  },
  // minZoom: 0.5,
  modes: {
    default: ['drag-canvas', 'zoom-canvas', 'dice-mindmap', {
      type: 'collapse-expand',
      onChange (item, collapsed) {
        const data = item.get('model')
        tree.updateItem(item, { collapsed })
        data.collapsed = collapsed
        return true       
      }
    }],
  },
});
      tree.data(this.dataTransform(this.rawData));
      tree.render();
      tree.fitView();
      this.graph = tree
      tree.changeSize(window.screen.width, window.screen.height);
      // tree.on('node:mouseenter', e => {
      //   const nodeItem = e.item
      //   // console.log(nodeItem)
      //   if (nodeItem._cfg.model.label !== '搜索' && (this.hoverNode === '' || this.hoverNode !== nodeItem._cfg.model.label)) {
      //     this.hoverNode = nodeItem._cfg.model.label
      //     // console.log(this.hoverNode)
      //   }
      // })
      tree.on('node:click', e => {
        const nodeItem = e.item
        // 如果点击的节点不是搜索节点则判断是否添加子节点
        if (!nodeItem._cfg.model.label.includes('搜索')) {
          // 标识，用于判断是否有子节点，无子节点则获取子节点
          let flag = nodeItem._cfg.children
          if (!flag) {
            let children = [{
            label: 'Logistic regression',
            // id: '0-1-1',
            type: 'dice-mind-map-root',
            children: [{
              label: 'aaaa',
              type: 'dice-mind-map-root'
            }]
          },
          {
            label: 'Linear discriminant analysis',
            // id: '0-1-2',
            type: 'dice-mind-map-root'
          },
          {
            label: 'Rules',
            // id: '0-1-3',
            type: 'dice-mind-map-root'
          },
          {
            label: 'Decision trees',
            // id: '0-1-4',
            type: 'dice-mind-map-root'
          },
          {
            label: 'Naive Bayes',
            // id: '0-1-5',
            type: 'dice-mind-map-root'
          },
          {
            label: 'K nearest neighbor',
            // id: '0-1-6',
            type: 'dice-mind-map-root'
          },
          {
            label: 'Probabilistic neural network',
            // id: '0-1-7',
            type: 'dice-mind-map-root'
          },
          {
            label: 'Support vector machine',
            // id: '0-1-8',
            type: 'dice-mind-map-root'
          },
        ]
            // 给节点添加id，以保证布局，id需要字符串
            let newChildren = children.map(item => {
              item.id = this.Util.uniqueId()
              return item
            })
              let id = Date.now() + ''
            // 如果子节点较多，则显示部分，其余部分通过搜索代替
            if (newChildren.length > 5) {
              this.childrenNodes[id] = newChildren.slice(0, 5)
              this.otherChildren[id] = newChildren.slice(5)
              this.childrenNodes[id].push({
                label: `搜索`,
                id,
                type: 'dice-mind-map-root'
              })
              // 保存点击节点的id，作为更新节点时的父节点id
              this.otherChildren.nodeId = nodeItem._cfg.id
            } else {
              // 子节点较少时直接显示不做处理
              this.childrenNodes[id] = newChildren
            }
            tree.updateChildren(this.childrenNodes[id], nodeItem._cfg.id)
          }
        } else {
          // 如果点击的是搜索节点，获取响应的未显示数据，并在显示框中展示
          const { id } = nodeItem._cfg
          console.log(id);
          this.searchId = id
          this.searchBox = this.otherChildren[id]
          this.otherChildren.nodeId = nodeItem.get('parent')._cfg.id
          this.showDialog = true
        }
      })
    },
  },
  created () {
    const { Util } = G6
    this.Util = Util
  },
  mounted () {
    this.fn()
    this.initTree()
  }
}
</script>

<style lang="less" scoped>
  /deep/ .el-dialog {
    width: 500px;
    .el-dialog__header {
      display: none;
    }
    .el-dialog__body {
      // display: none;
      padding: 0;
      text-align: left;
    }
  }
  /deep/ .el-card__body {
    padding: 0;
  }
  .searchCompany {
    height: 30px;
    padding: 10px;
    line-height: 30px;
    font-size: 16px;
    border-bottom: 1px solid #ccc;
    cursor: pointer;
    &:hover {
      background-color: #ccc;
    }
  }
  .bigBox {
    max-height: 300px;
    overflow: auto;
  }
  .tips {
    margin: 0;
    padding: 5px 10px;
    color: #ccc;
  }
</style>