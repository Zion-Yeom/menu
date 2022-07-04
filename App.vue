<template>
    <div>
        <div id="app">
            <div style="display: flex; justify-content: flex-start">
                <ul>
                    <li v-for="(menu, index) in menuItems" :key="index"
                        class="menu--none" :class="{selected: menu === selectedItem}"
                        @click="selected(menu)">
                        <span>menu : {{ menu.data }}  level : {{ menu.level }}</span>
                    </li>

                </ul>
                <button style="height: 40px; width: 100px"
                        @click="addMenu()" :disabled="!selectedItem">해당 위치에 메뉴 추가
                </button>
                <button style="height: 40px; width: 100px"
                        @click="deleteMenu(selectedItem)" :disabled="!selectedItem">메뉴 삭제
                </button>
                <button style="height: 40px; width: 100px"
                        @click="moveUp(selectedItem)" :disabled="!canMoveUp()">선택한 메뉴 위로이동
                </button>
                <button style="height: 40px; width: 100px"
                        @click="moveDown()" :disabled="!canMoveDown()">선택한 메뉴 아래로이동
                </button>
            </div>
        </div>
        <!--        <tree-data></tree-data>-->
    </div>
</template>

<script>
// import TreeData from "@/components/treeData";

export default {
    name: 'App',
    // components: {TreeData},
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


    computed: {
        menuItems() {
            return this.menuList || [];
        },

    },
    mounted() {
        this.getMenu();
    },

    watch: {
        menuItems: {
            deep: true,
            immediate: true,
            handler(value) {
                console.log('menuItems: ', value);
                // if (value.length === 0) this.getMenu();
            }
        },
        selectedItem: {
            handler(value) {
                console.log('selected : ', value)
            }
        },
        menuList: {
            deep: true,
            immediate: true,
            handler(value) {
                console.log('menuList: ', value);
                // if (value.length === 0) {
                //     this.getMenu();
                // }
            }
        },
    },
    methods: {
        /**
         * get menu list
         */
        getMenu() {
            this.menuList = [];
            this.convert(this.sample, 0);
        },

        /**
         * 메뉴 Tree to List
         */
        convert(node, level) {
            if (!node.level) {
                node.level = level;
            }

            if (node.children) {
                this.menuList.push(node);
                node.children.forEach(item => {
                    item.parent = node;
                    this.convert(item, level + 1);
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
        },


        /**
         * 메뉴 추가 ,삭제
         */
        addMenu() {
            const item = this.selectedItem;
            const index = this.menuList.indexOf(item);
            const newMenuName = prompt('새로운 메뉴 이름: ', 'default');
            const newMenu = {data: newMenuName, level: item.level + 1, parent: item};

            if (!newMenuName) {
                return false;
            }

            this.menuList.splice(index + 1, 0, newMenu);
            this.selectedItem = newMenu;


        },

        deleteMenu(node) {
            const index = this.menuList.indexOf(node);
            if (index < 0) {
                return false;
            }

            if (node.children) {
                this.menuList.splice(index, 1);
                this.selectedItem = null;
                node.children.forEach(item => {
                    this.deleteMenu(item);
                })
            } else {
                this.menuList.splice(index, 1);
                this.selectedItem = null;
            }

        },

        /**
         * 메뉴 위,아래 이동
         */

        moveUp(node) {
            const list = this.menuList;
            const index = list.indexOf(node);

            if (node.children) {
                list.splice(index, 1);
                list.splice(index - 1, 0, node);
                node.children.forEach(item => {
                    this.moveUp(item);
                })
            } else {
                list.splice(index, 1);
                list.splice(index - 1, 0, node);
            }


        },

        canMoveUp() {
            const item = this.selectedItem;
            if (item && item.parent) {
                const parent = item.parent;
                const itemIndex = this.menuList.indexOf(item);
                const parentIndex = this.menuList.indexOf(parent);

                return item && item.parent && (itemIndex > parentIndex + 1);
            } else {
                return false;
            }
            // const item = this.selectedItem;
            // const index = this.menuList.indexOf(item);
            // const prevItem = this.menuList[index - 1];
            // console.log(item, prevItem);

            // return item && (item.level > prevItem.level);

        },

        moveDown() {
            const item = this.selectedItem;
            const list = this.menuList;
            const index = list.indexOf(item);

            if (this.canMoveDown()) {
                list.splice(index, 1);
                list.splice(index + 1, 0, item);
            }

        },

        canMoveDown() {
            const item = this.selectedItem;
            const list = this.menuList;
            const index = list.indexOf(item);

            return item && (index < list.length - 1);
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

</style>
