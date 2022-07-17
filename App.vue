<template>
  <div>
    <div id="app">
      <div style="display: flex; justify-content: flex-start">
        <ul>
          <li v-for="(menu, index) in menuList" :key="index"
              class="menu--none" :class="{selected: menu === selectedItem,
                        level_1: menu.level === 1, level_2: menu.level === 2, level_3: menu.level === 3, level_4: menu.level === 4}"
              @click="selected(menu)">
            <span>menu : {{ menu.data }}</span>
          </li>
        </ul>
        <button style="height: 40px; width: 100px" :disabled="!selectedItem"
                @click="addNode( newData, selectedItem, 0)">아래에 메뉴 추가
        </button>
        <button style="height: 40px; width: 100px"
                @click="removeNode(selectedItem)" :disabled="!selectedItem">메뉴 삭제
        </button>
        <!--                <button style="height: 40px; width: 100px"-->
        <!--                        @click="moveNode(selectedItem, )" :disabled="!selectedItem">메뉴 이동-->
        <!--                </button>-->
        <button style="height: 40px; width: 100px"
                @click="moveUp(selectedItem)"> <!--:disabled="!canMoveUp(selectedItem)"-->선택한 메뉴 위로이동
        </button>
        <button style="height: 40px; width: 100px"
                @click="moveToTarget(selectedItem)">1-1 밑으로 이동
        </button>
        <!--                <button style="height: 40px; width: 100px"-->
        <!--                        @click="moveDown()" :disabled="!canMoveDown(selectedItem)">선택한 메뉴 아래로이동-->
        <!--                </button>-->
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      treeSampleData: {
        data: '1',
        children: [
          {
            data: '1-1',
          },
          {
            data: '1-2',
            children: [
              {
                data: '1-2-1',
              },
              {
                data: '1-2-2',
                children: [
                  {
                    data: '1-2-2-1',
                  },
                  {
                    data: '1-2-2-2',
                  },
                ]
              },
            ]
          },
          {
            data: '1-3',
            children: [
              {
                data: '1-3-1',
              },
              {
                data: '1-3-2',
                children: [
                  {
                    data: '1-3-2-1',
                  },
                  {
                    data: '1-3-2-2',
                  },
                ]
              },
            ]
          }
        ]
      },
      menuList: [],
      selectedItem: null,
      newData: {data: 'new'},
      parent: null,
    }
  },
  mounted() {
    this.loadMenu();
  },
  methods: {
    /**
     * tree to list 변경 후 메뉴 뿌리기
     */
    loadMenu() {
      this.menuList = [];
      this.flatten(this.treeSampleData);
    },
    /**
     * Tree to List 변경해주는 메소드
     */
    // convertTreeToList(node) {
    //     let list = this.menuList;
    //     list.push(node);
    //
    //     const children = node.children;
    //     if (Array.isArray(children)) {
    //         children.forEach(item => {
    //             const result = this.convertTreeToList(item);
    //             list = list.concat(result);
    //         })
    //     }
    //     return list;
    // },
    flatten(node) {
      let list = this.menuList;
      node.level = this.getNodeLevel(node);
      list.push(node);
      // const children = node.children;
      const children = this.getChildren(node);
      if (Array.isArray(children)) {
        children.forEach(item => {
          const result = this.flatten(item);
          list = list.concat(result);
        })
      }
      return list;
    },
    /**
     * 메뉴 클릭 시
     */
    selected(node) {
      this.selectedItem = node;
      // console.log(event);
      console.log('parent2: ', this.getParent2(node));
      // console.log('select: ', node.data, node);
      // console.log('parent: ', this.getParent(this.treeSampleData, node));
      // console.log('children : ', this.getChildren(node));
      // console.log('siblings: ', this.getSiblings(node));
      // console.log('ancestors: ', this.getAncestors(node));
      // console.log('level : ', this.getNodeLevel(node));
      // console.log('depth : ', this.getNodeDepth(node));
    },
    //
    /**
     * 부모구하기
     */
    getParent(root, node) {
      if (root === node) {
        return;
      }
      const children = root.children;
      if (Array.isArray(children)) {
        const check = children.includes(node);
        if (check) {
          // return root;
          this.parent = root;
        } else {
          children.forEach(item => {
            /*  const result =*/
            this.getParent(item, node);
            /* if(result){
                 return result;
             }*/
          })
        }
      }
      return this.parent;
    },

    getParent2(node) {
      const root = this.treeSampleData;
      if (node === root) {
        return {};
      }
      return this.getParent(root, node);
    },

    getFather(node) {
      const root = this.treeSampleData;
      if (node === root) {
        return {};
      }

      let rootChildren = this.getChildren(root);
      let target = rootChildren.find(item => item.data === node.data);
      let empty = {};
      let parentId = ''

      if (target) {
        // root 0level
        // target 1level
        // target 이 node 같은 레벨 경우니
        return root;
      } else {

        empty = this.test(rootChildren, node)
        console.log(empty)
        // console.log(parentId)
      }

    },

    test(rootChildren = [], node) {
      let target = {}
      rootChildren.forEach(item => {
        let target = item.data === node.data;
        if (target) {
          console.log(rootChildren)
          target = item;
        } else {
          console.log(item)
          if (item.children) this.test(item.children, node)
        }
      })
      return target;
    },

    // getAncestors(node) {
    //   //while 변경
    //   let list = [];
    //   const parent = this.getParent(this.treeSampleData, node);
    //   if (parent) {
    //     list.push(parent);
    //     const result = this.getAncestors(parent);
    //     list = list.concat(result);
    //   }
    //   return list;
    // },


    getAncestors(node) {
      //while 변경
      let list = [];
      let parent = this.getParent(this.treeSampleData, node);
      while (parent) {
        list.push(parent);
        parent = this.getParent(this.treeSampleData, parent);
      }
      return list;
    },
    /**
     * 자식구하기
     */
    getChildren(node) {
      const children = node.children;
      if (children) {
        return children;
      }
      return [];
    },
    /**
     * 형제 구하기
     */
    // getSiblings(node) {
    //   let list = [];
    //   const parent = this.getParent(this.treeSampleData, node);
    //   if (!parent) {
    //     return list;
    //   }
    //   const children = this.getChildren(parent);
    //   if (children) {
    //     //list = [].concat(children)
    //     //list = children.slice(0);
    //     list = [...children];
    //     // children.forEach(item => {
    //     //   list.push(item);
    //     // })
    //     const index = list.indexOf(node);
    //     list.splice(index, 1);
    //   }
    //   return list;
    // },

    getSiblings(node) {
      const parent = this.getParent(this.treeSampleData, node);
      if (!parent) {
        return [];
      }

      const children = this.getChildren(parent);
      let list = [];
      for (let i = 0; i < children.length; i++) {
        if (node === children[i]) {
          continue;
        }
        list.push(children[i]);
      }
      return list;
    },
    /**
     * level, depth 구하기
     */
    getNodeLevel(node) {
      return this.getAncestors(node).length;
    },

    getNodeDepth(node) {
      let maxDepth = 0;
      const children = this.getChildren(node);
      if (children) {
        let depth = 0;
        children.forEach(item => {
          depth = this.getNodeDepth(item) + 1;
          maxDepth = depth > maxDepth ? depth : maxDepth;
        })
      }
      return maxDepth;
    },
    /**
     * add / remove
     */
    addNode(node, parent, index) {
      const children = parent.children;
      if (!children) {
        /*  parent.children = [];
          parent.children.push(node);*/
        parent.children = [node];
        return this.loadMenu();
      }
      children.splice(index, 0, node);
      return this.loadMenu();
    },
    removeNode(node) {
      const parent = this.getParent(this.treeSampleData, node);
      const children = this.getChildren(parent);
      const index = children.indexOf(node);
      children.splice(index, 1);
      return this.loadMenu();
    },
    /**
     *  메뉴 이동
     */
    moveNode(node, toParent, insertIndex) {
      this.removeNode(node);
      this.addNode(node, toParent, insertIndex);
    },
    //
    //
    moveUp(node) {
      const toParent = this.getParent(this.treeSampleData, node)
      const children = this.getChildren(toParent);
      const insertIndex = children.indexOf(node) - 1;
      this.moveNode(node, toParent, insertIndex);
    },
    //
    canMoveUp(node) {
      if (node === this.treeSampleData) {
        return false;
      }
      const parent = this.getParent(this.treeSampleData, node)
      const children = this.getChildren(parent);
      const index = children.indexOf(node);
      return index > 0;
    },
    /**
     * 원하는 위치로 이동 (예제, 1-1 하위 메뉴로 이동)
     */
    getTarget(root) {
      let target = {};
      if (root.data === '1-1') {
        target = root;
      }
      const children = root.children;
      if (Array.isArray(children)) {
        target = children.filter(item => item.data === '1-1');
        if (target) {
          return target[0];
        } else {
          children.forEach(item => {
            this.getTarget(item);
          })
        }
      }
      return target;
    },
    moveToTarget(node) {
      const parentTo = this.getTarget(this.treeSampleData);
      if (this.removeNode(node)) {
        this.loadMenu();
      }
      if (this.addNode(node, parentTo, 0)) {
        this.loadMenu();
      }
    }
    //
    // moveDown() {
    //
    // },
    //
    // canMoveDown(item) {
    //
    // }
  }
}
</script>


<style>
li {
  list-style: none;
}

.menu--none {
  font-size: 20px;
  margin-top: 5px;
}

.menu--none:hover {
  cursor: pointer;
  border-radius: 5px;
  color: #0a7a5e;
}

.selected {
  background-color: #B6D9C8;
  border-radius: 5px;
}

.level_1 {
  margin-left: 30px;
}

.level_2 {
  margin-left: 60px;
}

.level_3 {
  margin-left: 90px;
}

.level_4 {
  margin-left: 120px;
}
</style>