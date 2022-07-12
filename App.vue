<template>
    <div>
        <div id="app">
            <div style="display: flex; justify-content: flex-start">
                <ul>
                    <li v-for="(menu, index) in menuList" :key="index"
                        class="menu--none" :class="{selected: menu === selectedItem,
                        level_1: menu.level === 1, level_2: menu.level === 2, level_3: menu.level === 3}"
                        @click="selected(menu)">
                        <span>menu : {{ menu.data }}</span>
                    </li>
                </ul>
                <!--                <button style="height: 40px; width: 100px"-->
                <!--                        @click="addNode(this.selectedItem, this.newData, 0)">아래에 메뉴 추가-->
                <!--                </button>-->
                <button style="height: 40px; width: 100px"
                        @click="removeNode(selectedItem)" :disabled="!selectedItem">메뉴 삭제
                </button>
                <!--                <button style="height: 40px; width: 100px"-->
                <!--                        @click="moveUp()" :disabled="!canMoveUp(selectedItem)">선택한 메뉴 위로이동-->
                <!--                </button>-->
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

            const children = node.children;
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
            console.log('select: ', node.data, node);
            console.log('parent: ', this.getParent(this.treeSampleData, node).data, this.getParent(this.treeSampleData, node));
            console.log('children : ', this.getChildren(node))
            console.log('siblings: ', this.getSiblings(node));
            console.log('ancestors: ', this.getAncestors(node));
            console.log('level : ', this.getNodeLevel(node));
            console.log('depth : ', this.getNodeDepth(node));
        },
        //


        getParent(root, node) {
            const children = root.children;
            if (root === node) {
                return;
            }

            if (Array.isArray(children)) {
                const check = children.includes(node);
                if (check) {
                    this.parent = root;
                } else {
                    children.forEach(item => {
                        this.getParent(item, node);
                    })
                }
            }
            return this.parent;
        },

        getAncestors(node) {
            let list = [];
            const parent = this.getParent(this.treeSampleData, node);

            if (parent) {
                list.push(parent);
                const result = this.getAncestors(parent);
                list = list.concat(result);
            }
            return list;
        },

        getChildren(node) {
            const children = node.children;
            if (children) {
                return children;
            }
            return [];

        },
        getNodeLevel(node) {
            return this.getAncestors(node).length;
        },

        getNodeDepth(node) {
            let maxDepth = 0;
            const children = this.getChildren(node);
            if (Array.isArray(children)) {
                let depth = 0;
                children.forEach(item => {
                    depth = this.getNodeDepth(item) + 1;
                    maxDepth = depth > maxDepth ? depth : maxDepth;
                })
            }
            return maxDepth;
        },

        getSiblings(node) {
            const list = [];

            const parent = this.getParent(this.treeSampleData, node);
            if (!parent) {
                return;
            }

            const children = this.getChildren(parent);
            if (Array.isArray(children)) {
                children.forEach(item => {
                    list.push(item);
                })
                const index = list.indexOf(node);
                list.splice(index, 1);
            }
            return list;
        },

        // addNode(parent, node, index) {
        //     const children = parent.children;
        //
        //     if (!children) {
        //         parent.children = [];
        //         parent.children.push(node);
        //     }
        //
        //     children.splice(index, 0, node);
        //     return this.loadMenu();
        // },
        // addNode(node, index) {
        //
        // },


        // removeNode(parent, node) {
        //
        //     return
        // },

        removeNode(node) {
            const parent = this.getParent(this.treeSampleData, node);
            const children = this.getChildren(parent);

            if (Array.isArray(children)) {
                const index = children.indexOf(node);
                children.splice(index, 1);
            }
            return this.loadMenu();
        },


        // moveNode(node, toParent, insertIndex) {
        //     this.removeNode(parent, node);
        //     this.addNode(toParent, node, insertIndex);
        // },
        //
        //
        /**
         * 메뉴 위,아래 이동
         */
        // moveUp() {
        //
        // },
        //
        // canMoveUp(item) {
        //
        // },
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
</style>
