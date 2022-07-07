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
                <button style="height: 40px; width: 100px"
                        @click="addMenu()" :disabled="!selectedItem">메뉴 추가
                </button>
                <button style="height: 40px; width: 100px"
                        @click="deleteMenu()" :disabled="!selectedItem">메뉴 삭제
                </button>
                <button style="height: 40px; width: 100px"
                        @click="moveUp()" :disabled="!canMoveUp(selectedItem)">선택한 메뉴 위로이동
                </button>
                <button style="height: 40px; width: 100px"
                        @click="moveDown()" :disabled="!canMoveDown(selectedItem)">선택한 메뉴 아래로이동
                </button>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'App',
    data() {
        return {
            sample: {
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
                            },
                        ]
                    }
                ]
            },
            menuList: [],
            selectedItem: null,
        }
    },
    mounted() {
        this.getMenu();
    },
    methods: {
        /**
         * get menu list
         */
        getMenu() {
            this.menuList = [];
            this.treeToList(this.sample, 0);
        },

        /**
         * 메뉴 Tree to List
         */
        treeToList(node, level) {
            if (!node.level) {
                node.level = level;
            }

            if (node.children) {
                this.menuList.push(node);
                node.children.forEach(item => {
                    item.parent = node;
                    this.treeToList(item, level + 1);
                })
            } else {
                this.menuList.push(node);
            }
        },

        /**
         * 메뉴 클릭 시
         */
        selected(menu) {
            this.selectedItem = menu;
            console.log(menu);
        },

        /**
         * 메뉴 추가 ,삭제
         */
        addMenu() {
            const item = this.selectedItem;
            const newMenu = {data: 'new', level: item.level + 1};

            if (!item.children) {
                item.children = [];
            }

            item.children.splice(0, 0, newMenu);
            this.getMenu();
        },

        deleteMenu() {
            const item = this.selectedItem;

            if (item && item.parent) {
                const parent = item.parent;
                const siblings = parent.children;
                const index = siblings.indexOf(item);

                siblings.splice(index, 1);
                this.getMenu();
            } else {
                this.sample = {};
                this.getMenu();
            }
            this.selectedItem = null;
        },
        /**
         *
         */
        getAncestors(node){

        },
        getParent(node){
            return ''
        },
        getChildren(node){

        },
        getNodeLevel(node){
            //부모부터 자기까지
        },
        getNodeDepth(node){
            //순서바뀌고, 부모, 자식도 바뀜
            // 자기부터 0 자식 1,2,3,4.....
        },

        getSiblings(node){
            const parent = this.getParent(node);
            const children = this.getChildren(parent);
            const index = children.indexOf(node);
            return children.splice(index, 1);
        },
        addNode(parent, node, index){

        },
        removeNode(parent, node){

        },
        moveNode(node, toParent, insertIndex){
            this.removeNode(parent, node);
            this.addNode(toParent, node, insertIndex);
        },


        /**
         * 메뉴 위,아래 이동
         */
        moveUp() {
            const item = this.selectedItem;
            const parent = item.parent;
            const siblings = parent.children;
            const index = siblings.indexOf(item);

            siblings.splice(index, 1);
            siblings.splice(index - 1, 0, item);
            this.getMenu();
        },

        canMoveUp(item) {
            if (item && item.parent) {
                const parent = item.parent;
                const siblings = parent.children;
                const index = siblings.indexOf(item);

                return index > 0;
            }
        },

        moveDown() {
            const item = this.selectedItem;
            const parent = item.parent;
            const siblings = parent.children;
            const index = siblings.indexOf(item);

            siblings.splice(index, 1);
            siblings.splice(index + 1, 0, item);

            this.getMenu();
        },

        canMoveDown(item) {
            if (item && item.parent) {
                const parent = item.parent;
                const siblings = parent.children;
                const index = siblings.indexOf(item);

                return index < siblings.length - 1;
            }
        }
    },
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
